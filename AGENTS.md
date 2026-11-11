# AGENTS.md — Taco3 协作规则

## 角色

| 角色 | 职责 |
|---|---|
| Architect | 仓库调查、架构决策、Linear 规划、验收标准；不擅自改产品意图 |
| Builder | 可复现的固件/文档/EDA 实现；提交可检查证据 |
| Pete | 采购、外壳、打印、装配、实机测量；关闭“需实测”任务 |

## 验证分层

1. **编译通过**：CI / 本地构建成功。
2. **接线/配置通过**：文档化接线与 `keyboard.toml` / Rust 配置一致。
3. **实机通过**：Pete 按 `docs/bringup/` 或 `validation/` 记录结果后，任务才可标完成。

禁止用模拟数据代替实测；禁止把“能编译”写成“已验证硬件”。

## 版本锁定

- 左右固件锁定**同一 RMK git commit**（见 `docs/architecture/02-rmk-evidence.md`）。
- 每次改接口必须同步：`hardware/interfaces/`、固件配置、EDA、BOM、机械接口。
- 整机版本清单放在 `releases/`，关联 PCB / 外壳 / 固件 commit。

## 禁止事项

- 未经论证改主控族（RP2040 辅助、nRF5340、第三接收器屏）。
- 把参考项目键数当成已确认需求。
- 把卖家 GPIO 图当已验证原理图 / 生产封装。
- 把无 LDO 的 PMW3610 裸芯片当 3.3V 插接模块。
- 静默改变固定选型；技术不支持时记录证据与替代方案，交 Pete 决策。

## 嘉立创 EDA

本机有 EasyEDA Pro API skill + WebSocket bridge（端口 49620–49629）。桥接可读写工程对象，但：

- 生成 JSON / 描述文件 ≠ PCB 已完成。
- 封装必须按实物或官方机械图核对，并安排 1:1 打印对孔。

## 构建提示（目标态）

| 目标 | 工具链 | 烧录 |
|---|---|---|
| ESP32-S3 | `esp` channel（espup）+ `espflash` | USB-C |
| nRF52840 | `stable` + `thumbv7em-none-eabihf` | UF2（Adafruit/nice!nano）或 probe-rs |

两侧**不要**强行共用单一 Cargo workspace。当前主机尚未安装 `espup` / `espflash` / `probe-rs`（2026-11-11 检查）。
