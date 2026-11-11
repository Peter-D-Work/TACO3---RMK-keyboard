# 外部参考与许可证边界

| 来源 | 许可（调研时） | 可复用 | 不可直接照搬 |
|---|---|---|---|
| [RMK](https://github.com/rmk-rs/rmk) | MIT OR Apache-2.0 | 固件框架、配置模式、驱动能力 | 假设完整组合已验证 |
| [Charybdis wireless mini build guide](https://github.com/280Zo/charybdis-wireless-mini-3x6-build-guide) | 仓库未见 SPDX | PMW3610+LDO 断板思路、34mm 球、BTU/陶瓷轴承 BOM、光学距离 | 彩屏在 Prospector 接收器；固件为 ZMK；键数/外壳 |
| [victorlucachi/charybdis-pmw3610-breakout](https://github.com/victorlucachi/charybdis-pmw3610-breakout) | 待确认 | 1.8/1.9V LDO 断板设计 | 许可证未核清前勿商用衍生 |
| [siderakb/pmw3610-pcb](https://github.com/siderakb/pmw3610-pcb) | CERN-OHL-P | 带 LDO 的开源传感器板 | 脚序仍需对实物 |
| [maatthc/zmk-crosses](https://github.com/maatthc/zmk-crosses) | MIT | 分体+PMW3610 绑定思路 | GPIO/二极管方向与原版不同；OLED 非圆屏 |
| [Good-Great-Grand-Wonderful/crosses](https://github.com/Good-Great-Grand-Wonderful/crosses) | MIT + CC-BY-SA-4.0 并存 | Choc 热插拔、静态轨迹球结构概念 | 与 MiaoMiao 固件不互换；轨迹球电气板不完整 |
| [mornepousse/KeSp_firmware](https://github.com/mornepousse/KeSp_firmware) | **GPL-3.0** | 圆屏信息架构、休眠灭屏概念 | **勿整仓拷入非 GPL 固件**；C/LVGL ≠ RMK Rust |
| Espressif S3 HW guide | 官方 | 启动脚、USB、Flash/PSRAM GPIO 规则 | 不能替代具体开发板原理图 |

## PMW3610 电气要点（数据手册）

- VDD：**1.7–2.1 V**（禁止直接 3.3V）
- VDDIO：可达 3.3 V
- 接口：3-wire SPI（SDIO/SCLK/NCS）+ MOTION；SCLK max ~2 MHz
- 工作距 Z：约 **2.2–2.6 mm**（typ 2.4）；镜头常见 **LM18-LSI**

## 已知拼装陷阱

- Charybdis 彩屏 ≠ 左半 GC9A01。
- KeSp 圆屏 + 分体配置不能直接拼成整机。
- Crosses 变体与原版 GPIO / 二极管方向不同。
- 卖家 “PMW3610 模块” 必须确认 **板载 LDO**。
