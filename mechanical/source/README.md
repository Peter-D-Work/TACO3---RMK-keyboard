# mechanical/source

Pete 可编辑 CAD。记录坐标、单位、与 PCB 接口基准（USB、插座、屏、旋钮、球、电池）。

## 接口输入（来自 `hardware/interfaces/`）

| 部件 | 外形（卖家资料） | 机械注意 |
|---|---|---|
| ESP32-S3 Super Mini | 23.2 × 18.2 mm | USB 外伸、板厚待实测（PD-134） |
| GC9A01 圆屏模块 | 36 × 44 × 2.7 mm，显示区 Ø32.4 | 四角安装孔坐标待测 |
| PMW3610 模组 | Ø32 mm，镜头在背面 | 光学距离 2.2–2.6 mm 以模组为基准 |
| Kailh 1350 轴体 | 上盖 15 × 15，底座 13.8 × 13.8 | Choc v1 键帽；定位板开孔 |
| Kailh 1350 热插拔座 | 最高 3.05 mm（贴 PCB 背面） | 底壳留空间 |

所有模块插在排母上：**模块高度 = 排母高 + 公针塑料座高**，排母选型后实测再定（`hardware/interfaces/connectors.md`）。
