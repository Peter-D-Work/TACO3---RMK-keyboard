# nice!view 状态屏（右）

状态：**已选型（卖家资料）**，Pete 2026-11-11  
依据：`hardware/references/nice-view/pinout-spec-seller.jpg`；RMK `main@84fd2e4`、ZMK `4dd95d7` 源码核对。未到货、未实测。

用途：右手本机状态显示——右手电量、与左手（central）的连接状态等。  
这是右手自带的第二块屏，不是“第三接收器屏”，不违反固定约束；左手圆屏仍是 GC9A01。

## 参数（卖家图）

| 项 | 值 |
|---|---|
| 屏 | Sharp **LS011B7DH03** 反射式 Memory LCD（单色） |
| 尺寸 / 分辨率 | 1.08"，160 × 68 |
| 模块外形 | **36 × 14 × 2.9 mm** |
| 刷新率 | 30 Hz |
| 功耗 | < 10 µA（典型） |
| 接口 | 3 线 SPI（只写） |
| 供电 | 3.3 V |
| 附件 | 自带排母和排针；表面有防短路涂层 |

## 引脚（屏面朝前、排针在下，从左到右）

| # | 丝印 | 接到右主控 |
|---|---|---|
| 1 | MOSI | GPIO（SPI 数据） |
| 2 | SCK | GPIO（SPI 时钟） |
| 3 | VCC | 3.3 V（常开，见下） |
| 4 | GND | GND |
| 5 | CS | GPIO（片选，**高有效**） |

## 引脚分配约束（交 PD-120）

- **与 PMW3610 不共线**，屏独占 3 根 GPIO，用 nRF52840 的 SPIM2/SPIM3（只发送）。
  - 原因：RMK PMW3610 驱动 `BitBangSpiBus::new(sck, sdio)` 独占两根脚；PMW3610 为 MSB 先发、CS 低有效，屏为 LSB 先发、CS 高有效。共线需自写互斥，收益只省 2 根脚。
- nice!view 官方默认脚为 CS=P0.06、MOSI=P0.17、SCK=P0.20；Crosses 的 PMW3610 恰好用 P0.17/P0.20/P0.06。**不要照搬任一参考项目的脚位**，统一由 PD-120 分配。
- 禁用：P0.13（VCC 开关）。电池检测若走 ADC 脚也要避开。

## 电气 / 固件要点

| 项 | 值 | 来源 / 状态 |
|---|---|---|
| SPI 模式 | Mode 0，**LSB 先发** | Zephyr `drivers/display/ls0xx.c` |
| CS | **高有效** | 同上（`SPI_CS_ACTIVE_HIGH`） |
| 时钟 | 1 MHz（ZMK `nice_view` overlay） | 屏上限未核规格书 |
| 命令 | 写 0x01、VCOM 位 0x02、清屏 0x04；每行 20 B 像素 + 地址/填充 | ls0xx 驱动 |
| VCOM | **必须持续软件翻转**（ZMK 每 33 ms 发一次空命令）；静态画面也不能停，否则直流偏压损伤屏 | ZMK；规格书频率要求待核 |
| 帧缓冲 | 160 × 68 / 8 = 1360 B | 计算 |

供电：

- **建议 VCC 接常开 3.3 V**，不接受 P0.13 控制的外部 VCC。关外部电源会让屏掉电丢画面，并使 VCOM 翻转失去意义。
- 若坚持接受控 VCC：固件必须明确 P0.13 状态，且休眠流程中先清屏再断电。RMK 是否默认驱动 P0.13 未知。

## RMK 集成路径

- RMK 内置显示驱动**不含** Sharp Memory LCD（仅 SSD1306 / oled-async / lcd-async）。
- `keyboard.toml` 配 SPI 显示会 `panic!("SPI display interface is not yet supported")` → 右手工程需用 **Rust API**（`use_rust` 写法或在 Rust 中初始化屏）。
- 自定义驱动：实现 `DrawTarget<Color = BinaryColor>` + `rmk::display::DisplayDriver::{init, flush}`，交给 `DisplayProcessor::with_renderer`；`flush` 只发变化行。
- 参考 crate：`memory-lcd-spi` 0.0.7（MIT/Apache，embedded-graphics-core 0.4，非 async）。不用 `sharp-memory-display`（GPL-3.0）。
- nRF 侧：embassy-nrf `Spim::new_txonly` + `bit_order` LSB；需在 RMK 锁定的 embassy-nrf 版本上确认。
- VCOM：独立 embassy 任务定时翻转，与 flush 共用 SPI 需互斥。

从机可用状态（RMK `RenderContext`，`split` 特性）：

| 字段 | 含义 | 备注 |
|---|---|---|
| `battery` | 右手本机电量 | 需右手配置电池采样 |
| `central_connected` | 是否连上左手 | 由 `split/ble/peripheral.rs` 发布 |
| 层 / WPM / 修饰键 | 由 central 同步 | 可选显示 |
| `ble_status` | 与电脑的连接 | **在左手**，从机侧大概率无意义 |

## 连接与机械

- 模块自带排针 / 排母：模块侧焊公针，我方 PCB 放 1×5 排母（见 `connectors.md`）。
- nice!view 原设计叠在插座式 nice!nano 上方（Corne OLED 位）；Taco3 右手布局另有轨迹球，位置由 Pete 决定，壳体需开窗。
- 排针针距（外观 2.54 mm）与安装方式待实物核对。

## 待核 / 待测

- [ ] 排针针距、模块安装位置与壳体开窗（机械）
- [ ] 规格书：最高 SPI 时钟、VCOM 最低翻转频率、EXTMODE 接法（克隆模块）
- [ ] 自定义驱动在 RMK 从机上显示电量与连接状态（PD-135）
- [ ] 实际电流（静态 + VCOM 翻转 / 刷新）
