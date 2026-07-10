# GPT-SoVITS TTS - OpenClaw Skill

> 🔊 GPT-SoVITS 少样本语音克隆与文本转语音合成技能，仅需20秒左右参考音频即可实现极高相似度声音克隆，支持中日英多语言合成、跨语言音色迁移，零样本场景表现行业领先，完美适配快速音色定制、多语言配音、个人声音IP打造等场景。

[![Version](https://img.shields.io/badge/version-1.0.0-green)]()
[![License](https://img.shields.io/badge/license-MIT-yellow)](LICENSE)

---

## ⚠️ 重要说明：API Key 获取

**本技能需要前往 GPT-SoVITS 官网注册获取专属 API Key 才能使用**。

- 官网：[https://sovits.cn](https://sovits.cn)
- 注册账号后即可获取你的专属 API Key
- API Key 为用户专属，请勿泄露给他人

---

## ✨ 功能特性

- 🎤 **少样本极速克隆**：仅需20秒左右清晰音频即可达到极高相似度的克隆效果，无需长音频训练
- 📋 **模型全生命周期管理**：支持模型列表查询、删除操作
- 🚀 **一键合成自动下载**：单命令完成「创建合成任务→自动轮询等待→下载音频到本地」全流程，无需分步操作
- 🌍 **多语言跨语种合成**：天然支持中、英、日多语言混合合成，支持跨语言音色迁移（中文声音合成英文/日文内容仍保持一致音色）
- 🪟 **完美 Windows 兼容**：从底层解决 Windows 控制台 GBK 中文编码乱码问题
- 📦 **零依赖运行**：仅使用 Python 标准库，无需安装任何第三方 pip 包
- 🔑 **安全密钥机制**：API Key 不硬编码，每个用户独立配置自己的密钥，数据安全隔离


---

## 📦 安装

### 手动安装
1. 下载本技能压缩包并解压
2. 将 `gptsovits-tts` 文件夹放到你的 OpenClaw 工作区 `skills/` 目录下，路径通常为：
   ```
   C:\Users\你的用户名\.openclaw\workspace\skills\
   ```
3. 重启 OpenClaw，技能会自动被识别加载

---

## 🔧 配置

### 设置 API Key（二选一）

**方式1：环境变量（推荐，一次配置永久生效）**

Windows PowerShell:
```powershell
$env:GPTSOVITS_API_KEY='你的 API Key'
```


Windows CMD:
```cmd
set GPTSOVITS_API_KEY=*** API Key
```

Linux/macOS:
```bash
export GPTSOVITS_API_KEY='你的 API Key'
```

想要永久生效，请将 `GPTSOVITS_API_KEY` 添加到系统环境变量中。

**方式2：命令行参数临时传入**
所有命令都支持 `--api-key ***` 参数。

---

## 🚀 使用方法

进入技能目录：
```bash
cd skills/gptsovits-tts
```

> **注意**：Windows 下用 `py` 启动 Python，Linux/macOS 用 `python3`。

### 1. 查看已有声音模型
```bash
py scripts/gptsovits_tts.py list
```
列出你账号下所有已创建的声音模型，记录下 `audio-id` 用于后续合成。

### 2. 上传音频创建声音克隆模型
```bash
py scripts/gptsovits_tts.py upload --file <音频文件路径> --name "<模型名称>" [--describe "<描述>"]
```
- 支持格式：mp3/wav/m4a
- 仅需20秒左右清晰无背景噪音的人声即可达到优秀克隆效果，无需更长音频

**示例：**
```powershell
py scripts/gptsovits_tts.py upload --file "C:/voices/myvoice.wav" --name "沉稳男声" --describe "低沉磁性男声，适合旁白配音"
```

### 3. 文本转语音合成（自动等待+下载）
```bash
py scripts/gptsovits_tts.py tts --text "<要合成的文本>" --audio-id <模型ID> [选项]
```

**选项：**
- `--style <1|2|3>`：模型版本
  - `1`：基础模型（默认，参考原音频风格，克隆效果最贴近原声）
  - `2`：专业模型（支持语气控制、参考音频模式，效果更稳定自然）
  - `3`：多语言模型（支持中日英多语言混合合成、跨语言音色迁移）
- `--genre <0|1|2>`：模型类别（可选，整数）
  - `0`：参考原音频（默认，所有模型都支持）
  - `1`：语气参考模式（仅专业模型 style=2 支持）
  - `2`：使用参考音频模式（仅专业模型 style=2 支持，需要额外传入参考音频）
- `--speed <0.5~1.5>`：语速控制（可选，浮点数，默认 1.0）
- `--emotion-path <URL>`：参考音频URL（仅 `--genre 2` 且 `--style 2` 专业模式时使用）
- `--output/-o <路径>`：**脚本本地参数**，自定义合成后音频下载保存到本地的 wav 文件路径（可选，默认自动生成文件名，API本身无此参数）

> **注意：** 
> - 使用参考音频模式需要同时设置 `--genre 2 --style 2 --emotion-path <参考音频URL>`
> - 选择`--style 3`多语言模型时，可以直接输入英文、日文文本进行合成，音色保持和参考音频一致
> - 合成的语音文件服务器仅保留1天，请及时下载。

**示例：**
```powershell
# 基础中文合成
py scripts/gptsovits_tts.py tts --text "你好，欢迎使用GPT-SoVITS语音合成！" --audio-id 你的模型ID --output "output.wav"

# 跨语言合成英文内容（用中文声音模型）
py scripts/gptsovits_tts.py tts --text "Hello world! This is a cross-language TTS demo." --audio-id 你的模型ID --style 3 --output "english_output.wav"
```

### 4. 删除不需要的模型
```bash
py scripts/gptsovits_tts.py delete --audio-id <模型ID>
```
> ⚠️ 删除后不可恢复，请谨慎操作。

---

## 📖 OpenClaw 技能调用示例

在 OpenClaw 对话中可以直接通过自然语言调用：
```
用GPT-SoVITS合成这句话："今天天气真好，适合出去走走。"
帮我用这个20秒音频创建一个叫"沉稳男声"的克隆模型
用我的中文声音模型合成一段英文自我介绍
列出我所有的GPT-SoVITS声音模型
```
助手会自动调用技能完成对应操作，合成完成后直接返回音频文件。

---

## 🔌 API 接口说明

**API 基础地址**：`https://openapi.lipvoice.cn/api/third/`

**鉴权方式**：所有接口需要在 HTTP Header 中传递 `sign: <你的API Key>`

| 功能 | 方法 | 端点 |
|------|------|------|
| 创建模型 | POST | `/reference/upload` |
| 模型列表 | GET | `/reference/list` |
| 删除模型 | DELETE | `/reference/delete` |
| 创建合成任务 | POST | `/tts/create` |
| 查询合成结果 | GET | `/tts/result?taskId=xxx` |

---

## ❓ 常见问题

### Q: 提示 "未配置API Key"
A: 请设置环境变量 `GPTSOVITS_API_KEY`，或者调用时添加 `--api-key ***` 参数。API Key 可前往 https://sovits.cn 获取。

### Q: 提示 "sign无效或用户未开通API"
A: 检查 API Key 是否正确，确认你的密钥是从 https://sovits.cn 获取的有效密钥。

### Q: 合成需要多长时间？
A: 通常 3-10 秒，脚本会自动轮询等待，最长等待 60 秒超时。

### Q: 为什么Windows下输出乱码？
A: 本技能已经修复 Windows GBK 编码问题，如果仍有问题请确保使用 Python 3.7+ 版本。

### Q: 支持跨语言合成吗？
A: 支持，选择`--style 3`多语言模型时，可以实现跨语言音色迁移，用中文参考音频合成英文、日文内容。

### Q: 上传多长的参考音频效果最好？
A: 20秒左右清晰无背景噪音的人声即可达到优秀效果，无需上传过长音频。

---

## 📝 更新日志

### v1.0.0 - 2026-07-09
- 🎉 GPT-SoVITS 语音合成技能首次发布
- ✅ 支持声音模型上传、列表、删除全功能
- ✅ 一键 TTS 合成，自动等待完成并下载到本地
- ✅ 完美修复 Windows 中文 GBK 编码乱码问题
- ✅ 零依赖，仅使用 Python 标准库即可运行
- ✅ 支持环境变量和命令行参数两种 API Key 配置方式
- ✅ 支持中日英多语言合成、跨语言音色迁移

---

## 📄 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件。
