# ESP32-S3 Super Mini（左主控）— 板级接口

状态：**M0 资料级通过**（PD-118，2026-11-11，Pete 决定）  
依据：卖家 GPIO 图 + 卖家商品图 + 嘉立创 EDA 库器件 `ESP32-S3-SuperMini`。  
**未实机核验**：Pete 尚未到货；实物尺寸 / 背面 / 电压表转 **PD-134**，USB↔电池切换与电流转 **PD-126**。

> 卖家图不是已验证原理图。封装以嘉立创 EDA 库为起点，打板前必须 1:1 打印并用实物对孔。

## 1. 连接方式（决定）

| 项 | 决定 |
|---|---|
| 主连接 | **侧边 2×9 排针（2.54 mm）→ 我方主板母座**；只依赖排针上的信号 |
| 背面触点（GPIO 焊盘、B+/B−） | **待考虑**，不进入 M0 基线；由 PD-120 决定转接板 / 飞线 / 扩展器 |
| EDA 器件 | 嘉立创 EDA 器件 ID **`ESP32-S3-SuperMini`** |

## 2. 身份

| 项 | 值 | 来源 / 状态 |
|---|---|---|
| 芯片 | **ESP32-S3FH4R2**（4MB Flash + 2MB PSRAM in-package） | 卖家商品图丝印 `FH4R2P3M4480`；**到货后核对本批丝印 / esptool** |
| 板型 | 电池版（`BAT` 丝印、背面 B+/B−/BOOST） | 卖家图 |

## 3. 尺寸

| 项 | 值 | 状态 |
|---|---|---|
| 板长 × 宽 | **23.2 × 18.2 mm**（**925 × 710 mil**） | 嘉立创 EDA 库 + Pete |
| 排针 | 2 列 × 9 针，沿长边，城堡孔 | 卖家商品图 |
| 排针中心距 | 2.54 mm | 卖家图标注；**待库封装 / 实物核对** |
| 两列排针间距 | — | **缺**（从 EDA 库封装读取） |
| 板厚 | — | **缺** |
| USB-C 外伸（超板边） | — | **缺** |
| USB / 天线位置 | USB-C 在一条短边；陶瓷天线在对侧短边 | 卖家商品图定性；坐标 **缺** |
| 背面焊盘间距 / 坐标 | — | 不进入 M0（排针方案） |

USB 朝上、元件面朝上时，排针顺序（卖家商品图）：

| # | 左列 | 右列 |
|---|---|---|
| 1 | TX（GPIO43） | 5V |
| 2 | RX（GPIO44） | GND |
| 3 | GPIO1 | 3V3 |
| 4 | GPIO2 | GPIO13 |
| 5 | GPIO3 | GPIO12 |
| 6 | GPIO4 | GPIO11 |
| 7 | GPIO5 | GPIO10 |
| 8 | GPIO6 | GPIO9 |
| 9 | GPIO7 | GPIO8 |

## 4. 引脚分类（按 FH4R2）

### 排针上（M0 基线可用范围）

| 状态 | GPIO | 说明 |
|---|---|---|
| **可用** | 1, 2, 4–13 | 12 根；1–10 为 ADC1（电池检测优先用） |
| **有条件** | 3 | strapping：JTAG 信号源选择，仅当 `EFUSE_STRAP_JTAG_SEL`=1 且 USB-JTAG / pad JTAG 均未禁用时生效（默认 eFuse 未烧 → 不影响启动）。芯片内部无上下拉，eFuse 生效时复位采样不得悬空；仍建议复位期间不被外设强拉 |
| **有条件** | 43（TX）, 44（RX） | UART0；上电有 ROM 日志输出，接输入器件需容忍 |
| 电源 | 5V, GND, 3V3 | 见 §5 |

排针信号合计：**15 根**（12 可用 + 3 有条件）。

### 背面焊盘 / 板内（不在排针上，M0 不用；引出方式待 PD-120）

背面焊盘位置按卖家图，到货后由 PD-134 核对。

| 状态 | GPIO | 说明 |
|---|---|---|
| **可用** | 14–18, 21 | 普通 GPIO（14–18 带 ADC2） |
| **可用** | 33–37 | FH4R2 片内为 **Quad** Flash + **Quad** PSRAM，不占 33–37（Octal 型号如 R8 才占用） |
| **可用** | 38, 47 | 普通 GPIO |
| **可用** | 39–42 | 默认可作 JTAG（MTCK/MTDO/MTDI/MTMS）；用作 GPIO 则放弃外部 JTAG，USB-JTAG 不受影响 |
| **有条件** | 0 | strapping（芯片默认上拉）：复位时 GPIO0=低 **且** GPIO46=低或悬空 → 下载模式；GPIO0=高 → 正常从 Flash 启动。板上 BOOT 键已接此脚；不要外接下拉或复位期间拉低 |
| **有条件** | 45 | strapping：VDD_SPI 电压（芯片默认下拉 → 3.3V）。数据手册 Table 3-4：`EFUSE_VDD_SPI_FORCE`=0（默认未烧）时由 GPIO45 决定，复位时为高 → 1.8V，FH4R2 的 3.3V 片内 Flash/PSRAM 无法正常工作。本批 eFuse 是否已烧录固定 3.3V 未知（PD-134 可用 `espefuse summary` 查）；设计上一律**不外部上拉** |
| **有条件** | 46 | strapping（芯片默认下拉）：启动组合 GPIO0=1 → SPI 启动（GPIO46 任意）；GPIO0=0 且 GPIO46=0 → 下载模式；GPIO0=0 且 GPIO46=1 → 无效。复位时被拉高会让 BOOT 键下载失效，并可能影响 ROM 日志输出。S3 上为双向 GPIO（S2 才是输入专用，见 ESP-IDF `esp32s3/soc_caps.h` 的 `SOC_GPIO_VALID_OUTPUT_GPIO_MASK`） |
| **板载占用** | 48 | 板载 RGB（WS2812 类）DIN |
| **禁用** | 19, 20 | USB D− / D+ |
| **禁用** | 26–32 | 片内 Flash/PSRAM SPI 总线 |
| 不存在 | 22–25 | S3 无此 GPIO |

分类依据：
[ESP32-S3 Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-s3_datasheet_en.pdf)（strapping、型号 Flash/PSRAM 配置）、
[ESP32-S3 Hardware Design Guidelines](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32s3/)、
ESP-IDF [`esp32s3/soc_caps.h`](https://github.com/espressif/esp-idf/blob/master/components/soc/esp32s3/include/soc/soc_caps.h)。

### 排针方案的预算风险（交 PD-120）

左半最少外设占用：GC9A01（SCL/SDA/RES/DC/CS）5 + BLK 1 + 编码器 A/B 2 + SK6812 DIN 1 + 背光电源使能 1 + 电池 ADC 1 = **11 根**。15 根排针信号只剩 **4 根给键矩阵**（2×2，最多 4 键）。仅用排针不够整块左半，PD-120 必须给出补齐方式（背面焊盘、扩展器/移位寄存器、或省脚设计）。

另：电池接在**背面 B+/B−**，不在排针上。即便信号只走排针，电池线仍需焊到背面焊盘或另行设计。

## 5. 电源

| 问题 | 结论（M0） | 状态 |
|---|---|---|
| 是否有完整 Power Path | **设计按“无 Power Path”处理**（公开资料：充电 IC + 二极管进 LDO） | 假设；USB↔电池切换实测转 **PD-126** |
| 5V 排针 | ≈ USB VBUS；电池供电时可能接近电池电压 → **不能当稳定 5V 外设电源** | 假设 |
| 3V3 排针 | 板载 LDO 输出；外设统一从这里取 3.3V | LDO 型号与余量待测 |
| BOOST | 充电电流约 100 → 300 mA 跳线，**不是升压** | 卖家图 |
| USB 插入 / 拔出电压表（5V、3V3、B+） | — | **缺**，转 **PD-134** |

## 6. 仍缺（到货后由 PD-134 关闭；USB↔电池切换与电流由 PD-126 关闭）

1. 本批芯片丝印 / `esptool` Flash/PSRAM。
2. 卡尺：板厚、USB 外伸、两列排针间距；与 EDA 库封装比对，1:1 打印对孔。
3. 背面照片：B+/B−/BOOST、背面 GPIO 丝印。
4. 万用表：USB 插入/拔出时 5V、3V3、B+。
