---
name: bilibili-subtitle-downloader
description: 下载 Bilibili 视频字幕，将其进行分块以供 LLM（大语言模型）处理，并生成高质量的总结。当用户提供 Bilibili BV 号或 URL，并希望获取视频内容的总结、核心要点或详细的分解时使用。
---

# Bilibili 字幕下载器技能

此技能通过使用专用的 Python 脚本和子智能体 (sub-agent) 编排，自动化提取和总结 Bilibili 视频字幕的流程。

## 前置依赖

首次使用需安装依赖：
```bash
pip install bilibili-api-python
```

## 支持的字幕类型

| 字幕类型 | 字段 | 支持状态 |
|----------|------|----------|
| 用户上传字幕 | `lan='zh'` | ✅ 完全支持 |
| AI 自动字幕 | `lan='ai-zh'` | ✅ 完全支持 |

## 工作流程

1.  **提取字幕**: 运行脚本来下载并分块字幕。普通视频均为 BV 号开头
    ```bash
    cd ~  # 建议在 home 目录运行，避免路径问题
    PYTHONIOENCODING=utf-8 python ~/.claude/skills/bilibili-subtitle-downloader/scripts/download_and_chunk.py <BV_ID>
    ```
    * **登录检查**: 如果脚本输出 `QR_CODE_READY:<PATH>`，需要扫码登录。Cookie 保存到 `~/.openclaw/workspace/bilibili_cookie.txt`
    * **字幕检测**: 脚本优先获取用户字幕（`zh`），若无则获取 AI 字幕（`ai-zh`）

2.  **处理输出**: 解析脚本输出的 `RESULT_JSON`，分块文件命名格式：
    * 普通视频 (BV号): `bili_temp/<BV_ID>/<BV_ID>_chunk_0.txt`
    * 课程剧集 (EP号): `bili_temp/<EP_ID>/chunk_0.txt`

## Bilibili 课程 (Cheese) 工作流程

1.  **提取课程/剧集信息**: 使用课程专属脚本
    ```bash
    cd ~
    PYTHONIOENCODING=utf-8 python ~/.claude/skills/bilibili-subtitle-downloader/scripts/cheese_downloader.py <SS_ID or EP_ID>
    ```
    * **登录**: 脚本将生成 `bilibili_login_qr.png` 二维码
    * **SS_ID 模式**: 打印课程信息和剧集列表，需用 EP_ID 获取字幕
    * **EP_ID 模式**: 下载字幕并切分保存到 `bili_temp/ep456/` 目录

## 子智能体指令

在生成总结的子智能体时，请使用以下 prompt 模式：

> 请阅读以下 Bilibili 视频字幕分块，并提供全面、准确的总结。
>
> **要求：**
> - 捕获所有关键的技术细节、具体的数据点和逻辑步骤
> - 使用标题保持清晰的结构
> - 明确主旨和可执行的要点
> - 风格：专业，信息丰富且详细
>
> **字幕文件：** [PATH_TO_CHUNK]

## 资源

- **脚本**: `scripts/download_and_chunk.py` - 处理 Bilibili API 交互和基于 Token 的安全分块
- **依赖**: `bilibili-api-python` (包名变更，原 `bilibili-api` 已停更)
