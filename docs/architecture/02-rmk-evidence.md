# RMK 复用证据

调研日期：2026-11-11  
许可：`MIT OR Apache-2.0`（crate）；仓库页常显示 Apache-2.0

## 版本锚点

| 项 | 值 | 来源 |
|---|---|---|
| 最新发布标签 | `rmk-v0.9.0`（annotated → `986017d…`；tag object `3b834d0…`） | `git ls-remote` 2026-11-11 |
| 调研时 main HEAD | `84fd2e4f3bcc35942cdfbb72ad590716e36583d5` | 同日 ls-remote |
| 文档站 | https://rmk.rs | |
| 仓库 | https://github.com/rmk-rs/rmk | |

> 主分支已出现 v0.9→v1.0 migration 文档迹象；**配置语法可能与 crates.io 0.9 不完全一致**。两侧必须锁同一 commit。

## 能力对照

| 需求 | 文档/代码结论 | 对我们的含义 |
|---|---|---|
| ESP32-S3 central + nRF52 peripheral | wireless 文档明确支持异构分体 | **可规划**；main 无 S3+nRF 示例 → M1 必测 |
| 示例 | `esp32_ble_split`（C6/C3）、`nrf52840_ble_split*`、`esp32s3_ble`；无官方 S3+nRF | 自建双工程 |
| PMW3610 | 仅 nRF52/RP2040；半双工 SDIO；peripheral 配置可转发 | 右半匹配；MOSI=MISO=SDIO |
| GC9A01 | `lcd_async` + 自定义彩色 renderer；toml SPI 显示会 panic | 走 Rust API；`firmware/ui` |
| RenderContext | layer、battery、ble_status、peripherals_connected、peripheral_batteries 等 | 状态页数据基本够用 |
| 编码器 | central/peripheral 均可配；编号 central 先 | 按压键当矩阵/直连脚 |
| 背光/RGB | LightAction 在 v0.9 基本 noop | 自定义 embassy/esp-hal PWM |
| 电池 | nRF ADC 或 `vddh`；ESP32 ADC 能力可能晚于 v0.9.0 | 冻结 rev 时核对 |
| 鼠标 HID | central BLE/USB 有 mouse report | 轨迹球经分体到 HID |
| 深睡 | Deep-sleep 未完成；有空闲睡眠超时 | 勿承诺 System OFF 级续航 |
| 构建 | S3：`channel=esp` + espflash；nRF：stable + UF2/probe-rs | 分目录；本机缺 espup/espflash/probe-rs |

## 参考链接

- Wireless：https://rmk.rs/docs/features/wireless
- PMW3610：https://rmk.rs/docs/configuration/input_device/pmw3610
- Display：https://rmk.rs/docs/features/display
- Keyboard/board：https://rmk.rs/docs/configuration/keyboard_device

## 冻结策略（建议）

1. M0 末：选定 commit，写入 `firmware/*/rmk-rev.txt` 与本文件。
2. 若需要 ESP32 电池 ADC 等 main-only 能力，锁 main 某日 commit，而不是只锁 crates.io 0.9.0。
3. CI：分别构建 left/right；产物带版本 + commit；**不**把编译绿当成实机绿。
