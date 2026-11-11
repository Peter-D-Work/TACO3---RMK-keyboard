# 资料核验：ESP32-S3 Super Mini（PD-118）

> **不是实机记录。** Pete 尚未到货；本页只记录卖家资料与嘉立创 EDA 库的核对结果。

- 日期：2026-11-11
- 执行者：Pete（决策 / 提供资料）+ Builder（整理）
- 固件：—

## 输入资料

| 资料 | 路径 / ID | 可信度 |
|---|---|---|
| 卖家商品图（正面，含芯片丝印与尺寸标注） | `hardware/references/esp32-s3-super-mini/seller-listing-front.jpg` | 卖家资料，非本批实物 |
| 卖家 GPIO 正反面图 | `hardware/references/esp32-s3-super-mini/gpio-pinout-seller.jpg` | 卖家资料，非原理图 |
| 嘉立创 EDA 库器件 | `ESP32-S3-SuperMini` | 库外形 23.2 × 18.2 mm（Pete 提供） |

## 结果

| # | 项 | 结果 | 依据 |
|---|---|---|---|
| 1 | 芯片型号 | ESP32-S3FH4R2 | 卖家商品图丝印 |
| 2 | 外形 | 23.2 × 18.2 mm | EDA 库 + 卖家图 |
| 3 | 排针 | 2 × 9，左 TX/RX/1–7，右 5V/GND/3V3/13–8 | 卖家商品图 |
| 4 | 连接方式 | 侧边排针接主板；背面触点待考虑 | Pete 决定 |
| 5 | Power Path | 设计按“无”处理 | 公开资料假设；USB↔电池切换实测转 PD-126 |
| 6 | 板厚 / USB 外伸 / 排针列距 / 背面 / USB 插拔电压表 | 缺 | 未到货；转 PD-134 |

## 结论

- PD-118 按 Pete 决定以**资料级**通过 M0；**不代表实机通过**。
- 到货实测（尺寸 / 背面 / 电压表）见 PD-134；USB↔电池切换与电流见 PD-126。汇总见 `hardware/interfaces/esp32-s3-super-mini.md`。
