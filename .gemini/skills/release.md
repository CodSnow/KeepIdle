# Skill: 发布 KeepIdle 新版本 (Release Workflow)

**描述 (Description):**
本 Skill 旨在指导 Agent 协助开发者为 KeepIdle 项目自动发布新版本。这套流程包含了修改代码版本号、代码提交、打 Tag 标签以及触发基于 GitHub Actions 的全自动跨平台编译与发布流水线。

**触发条件 (When to use):**
当用户提出诸如“发布一个新版本”、“打包 v1.x.x” 或 “打个 tag 更新发布一下” 等需求时。

**执行步骤 (Instructions):**

1. **版本号更新 (Bump Version):**
   - 使用 `read_file` 工具读取 `main.go`。
   - 找到代码中定义的版本号变量（例如 `const Version = "1.0.x"`）。
   - 使用 `replace` 工具将旧版本号替换为用户要求的新版本号（例如 `v1.x.y` 或 `1.x.y`）。

2. **本地代码提交 (Commit Changes):**
   - 执行 `git add .` 将所有变更放入暂存区。
   - 执行 `git commit -m "chore: bump version to v1.x.y"` 进行提交。如果还有其他功能更新，也可以一并写在 commit message 中。

3. **打上 Git Tag (Create Tag):**
   - 执行命令：`git tag v1.x.y`（注意：Tag 名称必须以 `v` 开头，以便触发 GitHub Actions 流水线中的 `v*` 条件）。

4. **推送至远端仓库 (Push to Remote):**
   - 推送代码分支：`git push origin main`（或当前默认分支）。
   - 推送 Tag 标签：`git push origin v1.x.y`。
   - （通常可以将这几条命令合并：`git push origin main && git push origin v1.x.y`）

5. **验证与反馈 (Verification & Feedback):**
   - 告知用户上述步骤已经执行完毕。
   - 提示用户：由于项目中已经配置了 `.github/workflows/release.yml`，现在 GitHub 的云端服务器正在自动为 Linux、Windows、macOS 的各个架构（amd64, arm64）进行交叉编译，并打包成 `.zip` 文件。
   - 引导用户等待流水线（Actions）完成后，直接在 GitHub 的 Releases 页面下载最新生成的可执行文件。
   - 同样提示用户，GHCR 对应的最新 Docker 镜像（如配置了 `docker-publish.yml`）也将会在 Actions 跑完后自动构建并更新为 `latest`。