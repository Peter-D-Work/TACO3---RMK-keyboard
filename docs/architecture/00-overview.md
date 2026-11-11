# Taco3 架构总览

更新日期：2026-11-11  
状态：M0 规划完成，等待开发启动

## 系统边界

```text
[PC]
  ^  BLE HID (keyboard + mouse) / USB HID
  |
[Left: ESP32-S3 Super Mini] ---- BLE split ---- [Right: nRF52840 board]
  RMK central                                  RMK peripheral
  matrix + encoder(+SW)                        matrix + encoder(+SW)
  GC9A01 SPI TFT (custom RGB565 renderer)      PMW3610 (3-wire SPI + MOTION)
  per-key RGB SK6812 MINI-E (1-wire)           per-key RGB SK6812 MINI-E (1-wire)
  all modules on pin headers -> our PCB female sockets
  battery ADC + charger (board)                battery (VDDH or ADC) + charger
```

## 复用优先结论

| 能力 | 结论 | 证据位置 |
|---|---|---|
| S3 central + nRF52 peripheral | **文档支持**；main 无官方混合示例 → **必须实机验证** | `02-rmk-evidence.md` |
| 从机 PMW3610 事件转发 | **支持**（nRF52/RP2040；3-wire SDIO） | 同上 |
| GC9A01 | **支持**，但需自定义彩色 `DisplayRenderer`；`keyboard.toml` SPI 显示会 panic | 同上 |
| 编码器两侧 | **支持**；peripheral 会转发 | 同上 |
| 按键背光 / RGB | RMK v0.9 LightAction **基本 noop** → 自定义 SK6812 串行驱动（S3 RMT / nRF PWM 序列） | 同上 |
| 双侧电量到 central | peripheral 电量可转发；ESP32 ADC 可能仅 main | 同上 |
| 轨迹球光学结构 | 优先复用 Charybdis/Crosses **已验证**模组几何，可平移组件 | `03-references.md` |
| 嘉立创 AI 桥接 | 本机 skill 可用；需单独验证连接与可写对象 | M2 issue |

## 关键架构决策

1. **双固件工程**：`firmware/left`（Xtensa/esp）与 `firmware/right`（ARM/stable）分开，共享约定放 `firmware/shared` 与文档，不共享强绑 workspace。
2. **RMK 版本**：联调锁定同一 git rev。v0.9.0（2026-08-29）发布；ESP32 电池 ADC 等能力可能晚于 v0.9.0，以 `02-rmk-evidence.md` 为准在 M0 冻结。
3. **显示**：优先自定义 `embedded-graphics` / `lcd-async` 渲染；**不**默认引入 LVGL（绑定与维护成本高）。
4. **电源**：S3 Super Mini 有充电 IC，但公开信息指向 **无完整 Power Path**；USB+电池并存行为必须实测。不满足时用现成 Power Path 模块，避免重复充电电路。
5. **引脚不足**：正面排针可能不够。优先评估**背面焊盘转接板**可制造性，再比较少量飞线；**不默认** I/O 扩展器，也不默认背面焊盘已是排针。
6. **PCB 范围**：插座、屏幕/轨迹球接口、矩阵、二极管、灯光及必要简单驱动；不设计裸 MCU / 射频 / 复杂电源。
7. **模块连接**：所有开发板/模块排针插接——模块焊公针，我方 PCB 放排母（`hardware/interfaces/connectors.md`）。
8. **按键**：Kailh 1350（Choc v1）+ 1350 热插拔座；逐键 SK6812 MINI-E 反贴（`hardware/interfaces/key-switch-backlight.md`）。

## 风险（高）

| 风险 | 影响 | 缓解 |
|---|---|---|
| 异构 BLE 无官方示例 | 配对/重连失败阻塞 M1 | 尽早最小分体联调；记录协议版本 |
| 圆屏 115KB 帧缓冲 + BLE | 卡顿 / 内存压力 | PSRAM 策略 + 并发负载测试 |
| S3 板无 Power Path / 5V=VBUS | USB 插拔复位、反灌 | 实测；必要时外挂 Power Path |
| 背面焊盘间距非 2.54 | 转接板难做 | 先测坐标；飞线备选 |
| PMW3610 模组 VDDIO 不稳压（≤ 3.3 V） | 接错电压损坏传感器 | 已选模组 VDD 经 2.0 V LDO；VDDIO 与 MCU 3.3 V 同源，禁止接电池 |
| SK6812 MINI-E VDD 下限 3.7 V | 锂电低电量时偏色/闪烁 | PD-120 决定：电池直供 / 5V 升压 / 仅 USB 开背光 |
| SK6812 静态 1 mA/颗 + 峰值 36 mA/颗 | 续航与供电能力 | 高边负载开关 + 固件限亮度；不从板载 LDO 取电 |
| SK6812 5 V 供电时 3.3 V 逻辑不足 | 数据错乱 | DIN 前加电平转换缓冲 |
| 模块插排母后的堆叠高度 | 光学距离 / 壳体厚度偏差 | 排母选型后实测，机械以模块为基准 |
| Super Mini 深睡电流高（WS2812/LDO） | 续航差 | 记录实测电流；勿承诺未测小时数 |
| 键数未冻结 | PCB/外壳返工 | M2 前由 Pete 冻结布局；参考键数不作需求 |

## 不在本轮实现

整机固件、PCB 布线、外壳造型。本轮仅仓库底座 + Linear 可执行规划。
