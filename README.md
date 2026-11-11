# Taco3

个人自研无线分体矮轴键盘。

| 侧 | 主控板 | 固件角色 | 主要外设 |
|---|---|---|---|
| 左 | ESP32-S3 Super Mini（电池焊盘版） | RMK BLE **central** | 按键、旋钮（带按压）、1.28" GC9A01 圆屏、逐键 RGB 背光 |
| 右 | nRF52840 开发板（nice!nano 兼容） | RMK BLE **peripheral** | 按键、旋钮、PMW3610 轨迹球、逐键 RGB 背光 |

左右独立电池；左手向电脑输出键盘 + 鼠标 HID。

按键：Kailh 1350（Choc v1 矮轴）+ 1350 热插拔座；背光 SK6812 MINI-E。所有开发板/模块以排针插在我方 PCB 的排母上。

## 当前阶段

**M0 选型与底座**（进行中）。本仓库已初始化目录与架构文档；固件工程与实机联调尚未开始。

- Linear 项目：[Taco3](https://linear.app/peter-d-personal/project/taco3-6ae9bc996457)
- 本地路径：`D:\TACO3`
- 远程 Git：GitHub [`Peter-D-Work/TACO3---RMK-keyboard`](https://github.com/Peter-D-Work/TACO3---RMK-keyboard)（`origin`）
- 开发机工具链：[`docs/bringup/dev-machine-setup.md`](docs/bringup/dev-machine-setup.md)（2026-11-11：`espup` / `espflash` / `probe-rs` 尚未安装，由 Pete 决定是否安装）

## 固定约束

- 圆屏必须是 GC9A01，不换成 OLED。
- 左右都刷 RMK；右侧不保留 ZMK。
- 不引入 RP2040 辅助主控、nRF5340 或第三个屏幕接收器。
- **开发板最小联调（M1）必须在 PCB 定版 / 下单前完成。**
- 编译通过 ≠ 实机通过。

## 目录

| 路径 | 内容 |
|---|---|
| `docs/architecture/` | 技术决策、证据、未决项 |
| `docs/bringup/` | 接线、烧录、测试步骤与结果 |
| `firmware/left/` `firmware/right/` | 两侧独立构建入口（工具链不同） |
| `firmware/shared/` `firmware/ui/` | 共享配置约定与圆屏渲染 |
| `hardware/references/` | 数据手册、卖家 GPIO 图等 |
| `hardware/interfaces/` | 统一 pinmap、电源预算、模块尺寸 |
| `hardware/eda/` | 嘉立创 EDA 工程 |
| `hardware/bom/` | BOM 与采购信息 |
| `mechanical/` | CAD 源文件与导出 |
| `validation/` | 实机测试记录 |
| `releases/` | 按整机版本归档 |

## 快速入口

1. 架构总览：[`docs/architecture/00-overview.md`](docs/architecture/00-overview.md)
2. 技术选型表：[`docs/architecture/01-selection-matrix.md`](docs/architecture/01-selection-matrix.md)
3. 开发机工具链：[`docs/bringup/dev-machine-setup.md`](docs/bringup/dev-machine-setup.md)
4. 引脚与电源：[`hardware/interfaces/pinmap-draft.md`](hardware/interfaces/pinmap-draft.md)
5. BOM 候选：[`hardware/bom/bom-candidates.md`](hardware/bom/bom-candidates.md)
6. 协作规则：[`AGENTS.md`](AGENTS.md)
