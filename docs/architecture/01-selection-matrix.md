# 技术选型表

查询日期：2026-11-11

| 项目 | 状态 | 当前选择 / 事实 | 待核实 | 需实测 |
|---|---|---|---|---|
| 左主控板 | 资料级通过（M0） | ESP32-S3 Super Mini **电池版**；外形 **23.2×18.2 mm**；嘉立创 EDA 器件 `ESP32-S3-SuperMini`；**侧边 2×9 排针接主板**，背面触点待 PD-120 | 店铺批次链接 | 到货：板厚、USB 外伸、排针列距、背面照片 |
| 左 MCU 型号 | 资料级 | **ESP32-S3FH4R2**（卖家图丝印 `FH4R2P3M4480`） | 到货批次是否一致 | `esptool` 读 flash/psram |
| 右主控板 | 已选型（卖家资料，PD-119） | **优信电子 nRF52840 ProMicro**（丝印 `V1940`），33×18 mm，每侧 13 孔（含 B+/B−），nice!nano v2 bootloader；可用 GPIO 21 根，其中高频仅 8 根；见 `hardware/interfaces/nrf52840-promicro.md` | 本批充电 IC / LDO 丝印、VCC 上拉阻值（漏电批次）、中间三脚坐标 | UF2 是否预装、VCC_OFF(P0.13)、电池读数 |
| 固件 | 已确定 | 双侧 **RMK**；左 central、右 peripheral | 锁定 git rev（建议同 commit） | S3↔nRF 配对与重连 |
| RMK 版本基线 | 待核实 | 发布标签 `rmk-v0.9.0`；调研时 main=`84fd2e4…`（2026-11-10） | ESP32 电池 ADC 是否需 main | 选定 rev 上两侧构建 |
| 分体通信 | 已确定 | BLE split | 地址/bonding 配置细节 | 休眠后重连、单侧按键唤醒 |
| 左显示 | 已选型（卖家资料） | 1.28" **GC9A01** 240×240；**8 针带 BLK** 模块（GND/VCC/SCL/SDA/RES/DC/CS/BLK），36×44×2.7 mm，3–5 V；见 `hardware/interfaces/gc9a01-tft.md` | 排针针距、安装孔坐标 | SPI 时序、背光电流、与 BLE 并发 |
| 右显示 | 已选型（卖家资料） | **nice!view**（Sharp LS011B7DH03 Memory LCD，1.08" 160×68，36×14×2.9 mm，3.3 V，<10 µA）；显示右手电量、连接状态；见 `hardware/interfaces/nice-view.md` | 规格书 SPI 上限 / VCOM 频率；克隆模块 EXTMODE（CS 高有效、软件 VCOM 已由 ZMK 驱动证实） | RMK 自定义 `DisplayDriver`（内置驱动不含 Sharp）；VCOM 持续翻转下的电流（PD-135） |
| UI 路线 | 已确定方向 | 自定义 RGB565 renderer；先状态页再菜单 | 帧缓冲放 SRAM 还是 PSRAM | 操作时局部动画 ~30fps 可否达成 |
| 左旋钮 | 已确定 | 机械编码器 A/B + 按压 | 具体型号 | 消抖、按压并入矩阵/直连 |
| 右轨迹球传感器 | 已选型（卖家资料） | `PMW3610-Module 20240425`：PMW3610DM-SUDU + **板载 2.0 V LDO**（VDD ≤ 6 V）；**VDDIO 不稳压、≤ 3.3 V**；圆形 Ø32 mm，镜头在背面；见 `hardware/interfaces/pmw3610-module.md` | 镜头型号、排针针距、安装孔 | CPI/延迟、光学距离 2.2–2.6 mm（含排母堆叠高度） |
| 球 / 轴承 | 待核实 | 参考 Charybdis：34 mm 球 + BTU/陶瓷轴承路径 | Crosses 静态托架实际球径 | 与外壳干涉 |
| 按键轴体 / 轴座 | 已确定 | **Kailh 1350（Choc v1 矮轴）** + **Kailh 1350 矮轴热插拔座**；键帽须 Choc 轴心；见 `hardware/interfaces/key-switch-backlight.md` | 凯华官方封装图；键距随布局 | 1:1 打印对孔、透光 |
| 矩阵二极管 | 已确定 | **1N4148W**（贴片 SOD-123），每键 1 颗 | 矩阵方向 | — |
| 按键背光 | 已确定 | **SK6812 MINI-E** 反贴逐键 RGB（替代原单色 LED + PWM） | LED 供电方式（电池直供 / 5V 升压）、电平转换、负载开关（PD-120） | VDD 3.7 V 下限 vs 锂电；静态 1 mA/颗；RMK 自定义驱动（PD-125） |
| 电源-左 | 设计假设 | **按无 Power Path 设计**；5V 排针≈VBUS 不作稳定 5V；BOOST=充电电流 100→300 mA（非 5V 升压） | 充电 IC 丝印 | USB 插拔 5V/3V3/B+ 电压表；切换是否复位（PD-126） |
| 电源-右 | 设计假设 | **按无 Power Path 设计**；同类板常见 TP4054/LTH7R + LDO；BOOST=充电 100→300 mA；P0.13 置低关 VCC；电池电压读 VDDH（USB 插入时≈5 V） | 本批充电 IC / LDO、分压是否焊接 | 漏电、充电终止、USB↔电池切换（PD-126） |
| 电池容量 | 未定 | — | 壳体空间 | 按实测电流估算续航（不承诺未测小时数） |
| PCB 策略 | 已确定 | 开发板插座 + 外设接口；不设计裸 MCU/射频 | 排母型号（标准 / 矮型） | 堆叠高度 |
| 开发板连接 | 已确定 | **所有开发板/模块排针连接**：模块焊公针，我方 PCB 放排母；见 `hardware/interfaces/connectors.md` | — | 插拔可靠性、堆叠高度 |
| 背面 GPIO 引出 | 待决策 | 优先转接板；其次飞线；扩展器最后 | 焊盘间距/可制造性 | 焊接可靠性 |
| EDA | 已确定工具 | 嘉立创 EDA Pro + 本机 API bridge skill | 桥接实机读写能力清单 | — |
| 机械 | Pete 负责 | 外壳、打印、试装 | 接口基准（USB/屏/球/电池） | 纸样/假板试装 |

状态定义：

- **已确定**：产品方向冻结，实现不得静默偏离。
- **待核实**：可用文档/采购信息补齐，不必须先上电。
- **需实测**：必须有 Pete 实机记录才能关闭。
