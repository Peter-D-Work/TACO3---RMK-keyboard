# nice!view 状态屏（右）

状态：**已选型（卖家资料）**，Pete 2026-11-11  
依据：`hardware/references/nice-view/pinout-spec-seller.jpg`。未到货、未实测。

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
| 3 | VCC | 3.3 V |
| 4 | GND | GND |
| 5 | CS | GPIO（片选） |

信号脚 3 根。具体 GPIO 由 PD-120 分配；建议与 PMW3610（3 线半双工 SDIO）用**不同的 SPI 外设**，共线需论证。

## 电气 / 固件注意

- Sharp Memory LCD 的片选通常为**高有效**，且需要周期性翻转 VCOM（nice!view 只引出 5 脚，按软件翻转处理）——以 LS011B7DH03 规格书和 nice!view 原理图核对。
- VCC 若接 nice!nano 受 P0.13 控制的 VCC，关外部电源时屏会熄灭；是否这样接由 PD-120 定。
- RMK 内置显示驱动**不含** Sharp Memory LCD（[RMK Display 文档](https://rmk.rs/docs/features/display) 列表仅 SSD1306 / SH110x / SSD1309 / lcd-async 彩屏）。文档支持自定义：为屏实现 `DrawTarget<Color = BinaryColor>` + `rmk::display::DisplayDriver`。
- 状态数据来源：RMK `RenderContext` 在从机侧提供 `battery` 与 `central_connected`（需 `split` 特性）。

## 连接

- 模块自带排针 / 排母：模块侧焊公针，我方 PCB 放 1×5 排母（见 `connectors.md`）。
- 排针针距（外观为 2.54 mm）与安装方式待实物核对。

## 待核 / 待测

- [ ] 排针针距、模块安装位置与壳体开窗（机械）
- [ ] CS 有效电平、VCOM 翻转方式（规格书 / 原理图）
- [ ] 自定义驱动在 RMK 从机上显示电量与连接状态
- [ ] 实际电流（显示静止 / 刷新）
