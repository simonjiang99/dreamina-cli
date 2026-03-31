# dreamina-cli

> 即梦（Dreamina）官方 CLI 工具的使用指南、一键安装脚本与 Agent Skill。

即梦是字节跳动推出的 AIGC 平台，支持文生图、文生视频、图生视频、多模态视频等能力。本仓库收录了 CLI 的安装方式、常用命令速查，以及供 AI Agent 调用的 Skill 文件。

---

## 一键安装

```bash
curl -fsSL https://jimeng.jianying.com/cli | bash
```

安装完成后，可执行文件为 `dreamina`，默认安装到 `~/.local/bin/`。

---

## 登录

### 方式一：浏览器扫码（推荐）

```bash
dreamina login --headless
```

用**抖音 App** 扫描终端/图片中的二维码，并在手机上确认授权。

### 方式二：网页导入 JSON

1. 浏览器登录即梦：`https://jimeng.jianying.com/ai-tool/login`
2. 访问以下链接，复制返回的完整 JSON：
   ```
   https://jimeng.jianying.com/dreamina/cli/v1/dreamina_cli_login
   ```
3. 保存为文件后导入：
   ```bash
   dreamina import_login_response --file /path/to/dreamina-login.json
   ```

---

## 命令速查

### 账户

| 命令 | 说明 |
|------|------|
| `dreamina login` | 登录（浏览器回调） |
| `dreamina login --headless` | 登录（无头浏览器+二维码，适合 Agent） |
| `dreamina logout` | 退出登录 |
| `dreamina relogin` | 强制重新登录 |
| `dreamina user_credit` | 查看剩余积分 |

### 任务管理

| 命令 | 说明 |
|------|------|
| `dreamina list_task` | 列出历史任务 |
| `dreamina list_task --gen_status=success` | 只看成功的任务 |
| `dreamina query_result --submit_id=<id>` | 查询单个任务结果 |

### 图像生成

| 命令 | 说明 |
|------|------|
| `dreamina text2image --prompt="..." --ratio=1:1` | 文生图 |
| `dreamina image2image --images ./input.png --prompt="..."` | 图生图 |
| `dreamina image_upscale --image ./input.png --resolution_type=4k` | 图像超分 |

### 视频生成

| 命令 | 说明 |
|------|------|
| `dreamina text2video --prompt="..." --duration=5` | 文生视频 |
| `dreamina image2video --image ./first.png --prompt="..."` | 单图生视频 |
| `dreamina frames2video --first ./a.png --last ./b.png --prompt="..."` | 首尾帧生视频 |
| `dreamina multiframe2video --images ./a.png,./b.png --prompt="..."` | 多图故事视频 |
| `dreamina multimodal2video --image ./input.png --audio ./music.mp3` | 多模态旗舰视频 |

---

## 常用示例

```bash
# 文生图（默认模型，1:1 比例，2K）
dreamina text2image --prompt="赛博朋克城市夜景，霓虹灯倒影" --ratio=1:1 --resolution_type=2k

# 文生视频（5秒，16:9）
dreamina text2video --prompt="一只猫在草地上奔跑" --duration=5 --ratio=16:9

# 图生视频，提交后轮询30秒
dreamina image2video --image ./photo.png --prompt="镜头缓慢推进" --poll=30

# 查询异步任务结果并下载
dreamina query_result --submit_id=3f6eb41f425d23a3 --download_dir=./output

# 查看剩余积分
dreamina user_credit
```

---

## 模型参考

### 文生图 / 图生图

| model_version | resolution_type | 备注 |
|---------------|-----------------|------|
| 3.0 / 3.1 | 1k, 2k | - |
| 4.0 / 4.1 / 4.5 / 4.6 / 5.0 | 2k, 4k | - |
| lab | 2k, 4k | 需要 VIP |

### 视频生成

| model_version | 时长 | 分辨率 | 命令 |
|---------------|------|--------|------|
| seedance2.0 | 4-15s | 720p | text2video / image2video / frames2video / multimodal2video |
| seedance2.0fast | 4-15s | 720p | 同上，速度更快 |
| 3.5pro | 4-12s | 720p/1080p | image2video / frames2video |

> 使用前建议先 `dreamina <subcommand> -h` 确认当前支持的参数组合。

---

## 给 Agent 的说明（Auto-Install Instructions）

如果你是 AI Agent，请按以下步骤操作：

### 第一步：安装 CLI

```bash
curl -fsSL https://jimeng.jianying.com/cli | bash
```

### 第二步：安装 Skill

安装完成后，Skill 文件已自动下载到：

```
~/.dreamina_cli/dreamina/SKILL.md
```

将其复制到你的 Agent skills 目录（以 Claude Code 为例）：

```bash
# 查找 Claude Code 的 skills 目录
ls ~/.claude/

# 复制 skill
cp ~/.dreamina_cli/dreamina/SKILL.md ~/.claude/skills/dreamina-cli.md
```

也可以直接从本仓库获取 Skill 文件：

```bash
curl -fsSL https://raw.githubusercontent.com/simonjiang99/dreamina-cli/main/skill/SKILL.md \
  -o ~/.claude/skills/dreamina-cli.md
```

### 第三步：登录（Agent 代操作）

在 headless 环境中，优先使用 `--headless` 模式，生成二维码让用户扫描：

```bash
dreamina login --headless
```

或者让用户手动完成网页登录后，将 JSON 文件路径告知 Agent：

```bash
dreamina import_login_response --file /path/to/dreamina-login.json
```

### 注意事项

- 所有生成操作均**消耗积分**，执行前务必告知用户
- 部分任务为异步，提交后需用 `query_result` 查询结果
- 部分模型首次使用前需在即梦 Web 端完成授权确认
- 详细参数以 `dreamina <subcommand> -h` 的输出为准，不要依赖本文档的静态描述

---

## 相关链接

- 即梦官网：[jimeng.jianying.com](https://jimeng.jianying.com)
- CLI 安装脚本：`curl -fsSL https://jimeng.jianying.com/cli | bash`
