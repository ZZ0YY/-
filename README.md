# Auto Step Runner - 自动刷步与通知助手

这是一个使用 GitHub Actions 实现的自动化项目，旨在每日自动完成以下任务：

1.  在预设的时间（如每天早上）自动运行。
2.  在一个设定的范围内生成一个随机的步数。
3.  向指定的目标服务器 API 发送带有该步数的请求。
4.  将执行结果（成功或失败）通过 **PushPlus** 推送到你的微信。

---

### ⚠️ **重要安全警告**

-   **密码安全**: 此项目操作的 API 以**明文形式**传输密码。为了保护你的账户安全，**强烈建议、必须将此项目部署在 GitHub 的私有仓库 (Private Repository) 中**。
-   **Secrets 管理**: 项目代码通过 GitHub Secrets 来管理你的敏感信息（账号、密码、Token），你的密码不会被硬编码在代码中，请放心使用。

---

### 部署指南

#### 第一步：创建私有仓库

1.  在 GitHub 上，点击右上角的 `+` 号，选择 **New repository**。
2.  给你的仓库起一个名字（例如 `my-step-runner`）。
3.  **最重要的一步**: 选择 **Private** 选项，创建一个私有仓库。
4.  点击 **Create repository**。

#### 第二步：设置仓库 Secrets

你需要将你的个人凭证安全地存储在仓库的 Secrets 中，Action 会在运行时自动调用它们。

1.  进入你刚创建的仓库页面，点击顶部导航栏的 **Settings**。
2.  在左侧菜单中，选择 **Secrets and variables** > **Actions**。
3.  点击 **New repository secret** 按钮，依次创建以下 **3** 个 Secrets：

| Secret 名称 | 对应的说明 | 示例值 |
| :--- | :--- | :--- |
| `USER_EMAIL` | 你的登录邮箱账号 | `2111@qq.com` |
| `USER_PASSWORD` | 你的登录密码 | `0####yj` |
| `PUSHPLUS_TOKEN` | 你在 PushPlus 官网获取的 Token | `a1b2c3dkskso###nz9d0e1f2a3b4c5d6` |

#### 第三步：添加工作流文件

1.  在你的仓库页面，点击顶部导航栏的 **Actions**。
2.  GitHub 可能会提示你一些模板，选择 **set up a workflow yourself**。
3.  将文件名修改为 `daily-step-runner.yml`。
4.  将本文档下面提供的 `.github/workflows/daily-step-runner.yml` 文件中的**全部内容**复制并粘贴到编辑器中。
5.  点击 **Commit changes...** 按钮保存文件。

#### 第四步：测试与验证

1.  工作流默认会在每天北京时间上午8:30自动运行。
2.  如果你想立即测试，可以进入 **Actions** 标签页，在左侧点击 `Daily Step Runner with Notification`，然后在右侧会出现一个 **Run workflow** 按钮，点击它即可手动触发一次任务。
3.  几秒后，刷新页面查看运行日志。如果一切顺利，你的微信应该会收到一条来自 PushPlus 的通知。

---

---
### 自定义配置

你可以直接编辑 `.github/workflows/daily-step-runner.yml` 文件来自定义运行时间和步数范围。

#### 1. 修改执行时间

脚本默认在北京时间 **早上8点** 和 **下午2点** 各运行一次。`cron` 语法基于 UTC 时间，北京时间(CST) = UTC+8。

-   **早上 8:00 (CST)** 对应 `00:00 (UTC)`
-   **下午 14:00 (CST)** 对应 `06:00 (UTC)`

你可以修改 `on.schedule` 部分的 `cron` 表达式来改变执行时间：

```yaml
schedule:
  # 任务一：北京时间 早上 8:00
  - cron: '0 0 * * *'
  # 任务二：北京时间 下午 14:00
  - cron: '0 6 * * *'```

#### 2. 修改步数范围

脚本会根据当前的运行时间自动选择步数范围。你可以在脚本的 `run` 部分修改 `if/else` 逻辑块中的 `MIN_STEPS` 和 `MAX_STEPS` 值。

```bash
# 在 jobs.run-and-notify.steps.run 下找到此逻辑块

# ...
if (( CURRENT_HOUR_UTC < 3 )); then
  # 这是早间任务的配置
  RUN_TYPE="清晨"
  MIN_STEPS=1000
  MAX_STEPS=9999
else
  # 这是午后任务的配置
  RUN_TYPE="午后"
  MIN_STEPS=20000
  MAX_STEPS=30000
fi
```
