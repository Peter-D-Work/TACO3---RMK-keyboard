# hardware/interfaces

左右统一的电气/机械接口约定：pinmap、电源预算、模块外形与安装基准。

| 文件 | 内容 |
|---|---|
| `connectors.md` | 开发板/模块一律排针连接（模块焊公针、我方 PCB 放排母） |
| `esp32-s3-super-mini.md` | 左主控板级事实与引脚分类 |
| `gc9a01-tft.md` | 1.28" GC9A01 圆屏模块 |
| `pmw3610-module.md` | PMW3610 轨迹球传感器模组 |
| `key-switch-backlight.md` | Kailh 1350 轴体、热插拔座、SK6812 MINI-E 逐键背光 |
| `pinmap-draft.md` | 功能→引脚预算（未冻结分配） |

改接口时必须同步固件配置、EDA、BOM、机械。
