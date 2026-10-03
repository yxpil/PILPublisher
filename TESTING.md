# PILPublisher 测试说明

- 测试完成：是（2026-10-04）
- 测试日期：2026-10-04
- 测试内容：单元测试覆盖 `server.rs` 纯函数——HTML 转义 `html_escape`、字节大小 `format_size`、`percent_decode`（含畸形转义）、MIME 猜测 `mime_guess`、字节查找 `find_bytes`、multipart 文件名提取（目录剥离）与数据提取、`ServerState` 默认值。集成测试在 `src-tauri/tests/` 真实启动 tiny_http 文件服务（环回端口 + 临时共享目录），覆盖根目录列表、文件夹列表（隐藏点文件与 .lnk）、文件下载、密码门禁（错密 403 / 对密下发 Cookie）、上传。注入测试针对不可信输入：上传文件名带路径穿越 `../../../evil.exe`（断言只落 basename、不写出共享目录外）、`.lnk` 快捷方式上传被拒、畸形 percent-encode 不 panic。另有一个 `#[ignore]` 的路径穿越探测用例，记录 `/files`、`/dl` 未对 `../` 归一化这一已知缺口（默认不跑，`cargo test -- --ignored` 可复现）。本仓库无自定义钩子/插件/事件注册表（重命名/删除/上传是 fetch 路由而非钩子链；Tauri 托盘事件需 GUI 运行时），故钩子测试不适用。
- 运行命令：在 `src-tauri/` 目录执行 `cargo test`（单 crate，非 workspace）
- 测试框架：Rust `#[cfg(test)]` + 自写原始 TCP/multipart 客户端（无额外测试依赖）
- 模型：豆包（Doubao）生成

## 测试布局

Tauri v2 应用，Rust crate 在 `src-tauri/`，故：

- **单元测试**：`src-tauri/src/server.rs` 末尾 `#[cfg(test)] mod tests`（8 个）。
- **集成测试**：`src-tauri/tests/file_server.rs`（7 个默认通过 + 1 个 `#[ignore]` 探测）。
- 为让集成测试从 crate 外部驱动真实服务，`src-tauri/src/lib.rs` 的 `mod server;` 改为 `pub mod server;`（仅可见性，无行为变化）。

## 如何运行

```bash
cd src-tauri
cargo test
```

复现已知路径穿越缺口：

```bash
cargo test -- --ignored probe_path_traversal_escapes_folder
```

## 预期结果

```
test server::tests::...            8 passed
test file_server::...              7 passed; 1 ignored
test result: ok. 15 passed; 0 failed; 1 ignored
```

## 原有测试覆盖

仓库原本**没有任何自动化测试**（无 `#[cfg(test)]`、无 `tests/`、无 `.github/workflows` CI）。本次新增 8 个单元 + 7 个集成（含 1 个 ignored 探测），其中 3 个为注入/恶意输入用例。

## 已知安全缺口（如实记录）

`/files/<folder>/<sub>` 与 `/dl/<folder>/<file>` 直接把 percent-decode 后的子路径 `join` 到共享根目录，未做 `..` 归一化，理论上可越出共享目录读文件。上传路径因 `extract_multipart_filename` 取 `file_name()` 已收敛为 basename，不受此影响。该缺口用 `#[ignore]` 测试固定复现，未在本次提交中修改产品代码（任务范围为补测试）。
