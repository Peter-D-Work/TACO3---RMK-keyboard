# ESP32-S3 Super Mini 参考

| 文件 / ID | 说明 | 可信度 |
|---|---|---|
| `gpio-pinout-seller.jpg` | 卖家/社区 GPIO 正反面标注图 | **核验输入**，非已验证原理图 |
| `seller-listing-front.jpg` | 卖家商品图（正面丝印、23.2×18.2 mm 标注） | 卖家资料，**非本批实物** |
| 嘉立创 EDA `ESP32-S3-SuperMini` | 库器件 / 封装 | PCB 起点；打板前 1:1 对孔 |

板级汇总：`hardware/interfaces/esp32-s3-super-mini.md`  
资料核验记录：`docs/bringup/2026-11-11-esp32-s3-super-mini-desk-review.md`

## 使用规则

1. 卖家图可用于列功能与大致丝印名称。
2. 不可用于生产封装尺寸、孔位、板厚推算。
3. 主连接用侧边 2×9 排针；背面触点方案待 PD-120。
4. BOOST 标注含义：充电电流选择（约 100mA → 300mA），仅当电池容量足够时使用；**不是升压电路**。

## 到货后补档（PD-134）

- [ ] 本批正反面照片 + 卡尺读数
- [ ] 板厚、USB 外伸、两列排针间距（与 EDA 库比对）
- [ ] USB 插拔电压表（5V / 3V3 / B+）
- [ ] `esptool` 读到的芯片型号与 Flash/PSRAM
