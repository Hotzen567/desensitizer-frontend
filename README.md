# desensitizer-frontend

脱敏软件（Rust 中文 PII 脱敏）的**共享前端**，被以下产品仓库以 git submodule 方式引入：

- `desensitizer`（master，自动 + 手动）
- `desensitizer-manual`（lacuna，纯手动）

## 单一真源

本仓库是**唯一**的前端源码。两个产品仓库都不再各自维护 `index.html`，
而是把本仓库作为 submodule 放在各自仓库根的 `frontend/` 目录，
后端通过 `rust-embed` 把 `frontend/index.html` 嵌入二进制。

## 能力驱动（capability-driven）

前端**不区分** master 还是 lacuna。所有功能差异都来自后端在
`GET /api/health` 的 `engines` 声明里广播的「能力位」：

| 能力位 | 含义 |
|---|---|
| `regex` / `ner` | 只要有其一即显示「自动识别」面板 |
| `library` | 显示脱敏库浏览 / 分类计数 |
| `import_mapping` | 显示「还原导入」按钮 |
| `adjust` | 显示调整 / 工作日志按钮 |

lacuna 把自动引擎与 `import_mapping`/`adjust` 置为 `false`，前端据此
自动降级为纯手动界面。前端永远不要写 `if (isLacuna)` 这类硬编码分支。

## 本地开发

后端在 **debug 构建**下，`rust-embed` 直接从磁盘读取 `frontend/index.html`，
所以改完本文件、刷新浏览器即可生效，无需重新 `cargo build`。
（release 构建才把文件编译进二进制。）

改动请在本仓库提交并 push，然后在各产品仓库执行
`git submodule update --remote frontend` 以更新指针。
