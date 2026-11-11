# 开发机工具链清单（Windows）

更新日期：2026-11-11  
主机：`D:\TACO3` 所在 Windows 10/11（PowerShell）  
范围：能**编译**左右固件所需的本机工具安装与验证步骤。  
**不在本文件范围**：完整固件实现、接线联调、实机验证结论。

> 编译通过 ≠ 硬件已验证。实机结果只写在 `docs/bringup/YYYY-MM-DD-*.md` 或 `validation/`。

## 1. 目标与角色

| 侧 | 目标三元组 / channel | 烧录 |
|---|---|---|
| 左 ESP32-S3 | `xtensa-esp32s3-none-elf`，`esp`（espup） | `espflash`（USB-C） |
| 右 nRF52840 | `thumbv7em-none-eabihf`，`stable` | UF2 或 `probe-rs` |

两侧**不要**强行共用单一 Cargo workspace（见 `AGENTS.md`）。

| 事项 | 负责人 |
|---|---|
| 编写本清单、记录本机探测结果 | Builder |
| 是否在本机安装缺失工具 | Pete |

## 2. 本机探测快照（2026-11-11）

在仓库根目录 PowerShell 执行：

```powershell
git --version
git lfs version
rustup --version
rustc --version
cargo --version
rustup show
rustup target list --installed
espup --version
espflash --version
probe-rs --version
```

| 工具 | 状态 | 版本 / 说明 | 安装责任 |
|---|---|---|---|
| Git | 已安装 | `git version 2.52.0.windows.1` | — |
| Git LFS | 已安装 | `git-lfs/3.7.1`；仓库已 `filter=lfs` | — |
| rustup | 已安装 | `1.28.2` | — |
| rustc / cargo | 已安装 | `1.93.0`（`stable-x86_64-pc-windows-msvc`） | — |
| `thumbv7em-none-eabihf` | **尚未安装** | `rustup target list --installed` 仅有 host | Pete（装固件前） |
| espup | **尚未安装** | 命令未找到 | Pete |
| esp channel（Xtensa） | **尚未安装** | `rustup show` 无 `esp` | Pete（`espup install`） |
| espflash | **尚未安装** | 命令未找到 | Pete |
| probe-rs | **尚未安装** | 命令未找到 | Pete |
| UF2 拷贝工具 | 可选 | 资源管理器拖放即可；可选 `adafruit-nrfutil` 等 | Pete（按板卡需要） |

远程 Git：`origin` → `https://github.com/Peter-D-Work/TACO3---RMK-keyboard.git`（与根 `README.md` 一致）。

## 3. 前置（已满足则可跳过）

1. 安装 [Visual Studio Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) 的 **Desktop development with C++**（MSVC ABI；espup / probe-rs 源码安装常用）。
2. 安装 [rustup](https://rustup.rs/)（默认 `x86_64-pc-windows-msvc`）。
3. 建议开启 Windows 长路径（esp / 依赖树较深时）：
   - `gpedit.msc` → 计算机配置 → 管理模板 → 系统 → 文件系统 → **启用 Win32 长路径** → 重启；或按微软文档设注册表后重启。
4. Git LFS：`git lfs install`（本机已可用；克隆含大文件的远程后需 `git lfs pull`）。

## 4. 左侧：ESP32-S3（espup + espflash）

官方参考：[The Rust on ESP Book — Toolchain](https://docs.espressif.com/projects/rust/book/getting-started/toolchain.html)、[espup](https://github.com/esp-rs/espup/)。

```powershell
# 4.1 安装 espup
cargo install espup --locked
espup --version   # 记录到下方「安装后回写」

# 4.2 安装 Espressif / Xtensa 工具链（会占用数 GB）
espup install
rustup toolchain list   # 应出现 esp
# Windows 上环境变量由 espup 写入系统，一般无需 source；若新开终端仍找不到，注销重登后再测

# 4.3 烧录工具
cargo install espflash --locked
espflash --version

# 4.4 冒烟（固件工程就绪后）
# cd firmware\left
# cargo +esp build   # 或工程 rust-toolchain.toml 指定的方式
# espflash flash --monitor <固件路径>
```

可选：`cargo install ldproxy --locked`（部分 esp-rs 链接流程需要；若构建报错再装）。

## 5. 右侧：nRF52840（stable target + UF2 / probe-rs）

```powershell
# 5.1 交叉编译目标
rustup target add thumbv7em-none-eabihf
rustup target list --installed   # 确认含 thumbv7em-none-eabihf

# 5.2 probe-rs（调试器 / 非 UF2 烧录）
# 预编译安装脚本（推荐）或：
cargo install probe-rs-tools --locked
probe-rs --version

# 预编译脚本（二选一即可）：
# irm https://github.com/probe-rs/probe-rs/releases/latest/download/probe-rs-tools-installer.ps1 | iex
```

### UF2（nice!nano 兼容板）

1. 双击复位进入 UF2 盘符。
2. 将构建产物 `.uf2` 拷入该盘（资源管理器即可）。
3. 板卡自动复位；**不要**把生成的 `.uf2` 提交进 Git（见根 `.gitignore`）。

可选 CLI：按所用引导程序文档安装 `adafruit-nrfutil` 或厂商工具，并在「安装后回写」记版本。

## 6. 仓库约定复核清单

| 项 | 位置 | 期望 |
|---|---|---|
| LFS 追踪 | `.gitattributes` | 图片/PDF/STEP/STL/3MF/DXF/ZIP 走 LFS |
| 忽略构建物 | `.gitignore` | `target/`、`*.uf2`、`*.bin`、`*.elf` 等 |
| 目录说明 | 各层 `README.md` | 标明用途；勿把「能编译」写成「已验证」 |
| CI | `.github/workflows/firmware-ci.yml` | 固件骨架未就绪前仅为 placeholder |

## 7. 安装后回写（Pete 安装完成后填写）

| 工具 | 命令 | 记录版本 | 日期 | 执行者 |
|---|---|---|---|---|
| espup | `espup --version` | | | |
| esp toolchain | `rustup run esp rustc --version` | | | |
| espflash | `espflash --version` | | | |
| thumb target | `rustup target list --installed` | | | |
| probe-rs | `probe-rs --version` | | | |
| UF2 工具（若用） | （自填） | | | |

新克隆：`git clone https://github.com/Peter-D-Work/TACO3---RMK-keyboard.git` 后执行 `git lfs pull`。
