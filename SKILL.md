---
name: gptsovits-tts
description: GPT-SoVITS 少样本语音克隆与合成服务：仅需20秒左右参考音频即可实现高相似度声音克隆，支持中日英多语言合成、跨语言音色迁移，零样本场景表现优异，适配快速音色定制、多语言配音等场景。
triggers:
  - 语音合成
  - 声音克隆
  - TTS
  - GPT-SoVITS
  - GPTSoVITS
  - 少样本克隆
  - 跨语言合成
  - 多语言TTS
  - 文本转语音
  - 声音模型
  - 参考音频
---

# GPT-SoVITS TTS 语音合成技能

> [!IMPORTANT]
> ## ⚠️ API Key 配置说明
> 
> **本技能需要前往 GPT-SoVITS 官网获取专属 API Key 后方可使用**
> 
> | 项目 | 说明 |
> |------|------|
> | **API Key 获取地址** | [https://sovits.cn](https://sovits.cn) |
> | **适用场景** | 快速音色定制、多语言配音、跨语言内容创作、个人声音IP打造 |
> | **克隆效果** | 20秒左右参考音频即可实现极高相似度声音克隆，支持中日英多语言合成、跨语言音色迁移，零样本效果行业领先 |
> 
> **未获取API Key请先前往官网注册获取！**

## 技能描述
GPT-SoVITS 是基于开源 GPT-SoVITS 大模型打造的少样本语音克隆和语音合成 API 服务。本技能提供简洁的命令行接口，支持声音模型管理、一键文本转语音（自动等待完成并下载）等核心功能，在极短参考音频的克隆效果上表现突出，天然支持多语言和跨语言合成。

## 适用场景
- 快速创建高相似度声音克隆模型（仅需20秒左右清晰音频样本）
- 管理已创建的语音模型（列表查询、删除）
- 使用克隆的声音进行文本转语音（TTS），自动轮询等待并下载音频
- 支持中日英多语言合成、跨语言音色迁移（中文参考音频合成英文/日文等）
- Windows 系统中文输出兼容

## 环境要求
- Python 3.6+（系统自带 urllib，无需额外依赖）
- **需要配置 API Key**（前往 https://sovits.cn 获取）

## 配置 API Key

配置方式二选一：

### 方式1：环境变量（推荐，一次配置永久生效）
**Windows PowerShell:**
```powershell
$env:GPTSOVITS_API_KEY='你的 API Key'
```

**Windows CMD:**
```cmd
set GPTSOVITS_API_KEY=*** API Key
```

**Linux/macOS:**
```bash
export GPTSOVITS_API_KEY='你的 API Key'
```

### 方式2：命令行参数传入
调用命令时添加 `--api-key ***` 参数

## 命令参考

脚本路径：`scripts/gptsovits_tts.py`（相对于本技能目录）

> **注意**：Windows 下使用 `py` 启动，Linux/macOS 使用 `python3`。

---

### 一、声音模型管理

#### 1. 查询模型列表
```bash
py scripts/gptsovits_tts.py list
```
列出所有已上传的声音模型及其 ID，合成语音时需要用到模型 ID。

---

#### 2. 上传音频创建声音克隆模型
```bash
py scripts/gptsovits_tts.py upload --file <音频文件路径> --name "<模型名称>" [--describe "<描述>"]
```

**参数说明：**
- `--file` - 参考音频文件路径，支持 mp3/wav/m4a 格式（必需）
- `--name` - 自定义模型名称（必需）
- `--describe` - 模型描述（可选）

**建议：** 使用 20秒左右清晰无背景噪音的人声即可达到优秀克隆效果，无需长音频。

**示例：**
```powershell
py scripts/gptsovits_tts.py upload --file "C:/Users/xxx/Desktop/myvoice.wav" --name "我的声音" --describe "沉稳男声"
```

---

#### 3. 删除模型
```bash
py scripts/gptsovits_tts.py delete --audio-id <模型ID>
```

**参数说明：**
- `--audio-id` - 要删除的模型 ID（从 `list` 命令获取，必需）
> ⚠️ 删除后不可恢复

---

### 二、文本转语音（TTS）

#### 4. 一键语音合成（自动等待+下载）
```bash
py scripts/gptsovits_tts.py tts --text "<要合成的文本>" --audio-id <模型ID> [选项]
```

**参数说明：**
- `--text` - 要合成的文本内容（必需，最大5000字符）
- `--audio-id` - 使用的声音模型 ID（必需）
- `--style` - 模型版本（可选，默认 `1`）：
  - `1` - 基础模型（参考原音频风格，克隆效果最贴近原声）
  - `2` - 专业模型（支持语气控制、参考音频，更稳定自然）
  - `3` - 多语言模型（支持中日英多语言混合合成、跨语言音色迁移）
- `--genre` - 模型类别（可选，整数）：
  - `0` - 参考原音频（默认，所有模型都支持）
  - `1` - 语气参考模式（仅专业模型 style=2 支持，需要配合情绪参数）
  - `2` - 使用参考音频（仅专业模型 style=2 支持，需要传入 emotionPath）
- `--speed` - 语速控制（可选，浮点数，范围 0.5~1.5，默认 1.0）
- `--emotion-path` - 参考音频URL（仅 genre=2 且 style=2 专业模式时使用）
- `--output/-o` - **脚本本地参数**：自定义合成后音频下载保存到本地的 wav 文件路径（可选，默认自动生成文件名，API本身无此参数）

> **说明：** 
> - 使用参考音频模式需要同时设置 `--genre 2 --style 2 --emotion-path <参考音频URL>`
> - 选择style=3多语言模型时，支持直接合成中文、英文、日文内容，自动迁移音色
> - 合成的语音文件服务器仅保留1天，请及时下载。

**示例：**
```powershell
# 默认风格合成，自动下载
py scripts/gptsovits_tts.py tts --text "你好，欢迎使用GPT-SoVITS语音合成！" --audio-id 你的模型ID

# 跨语言合成：用中文声音模型合成英文内容
py scripts/gptsovits_tts.py tts --text "Hello, welcome to use GPT-SoVITS speech synthesis!" --audio-id 你的模型ID --style 3

# 指定输出路径
py scripts/gptsovits_tts.py tts --text "今天天气真不错" --audio-id 你的模型ID --output "C:/Users/xxx/Desktop/output.wav"
```

**功能说明：**
- 自动创建 TTS 任务
- 自动轮询等待合成完成（最长等待60秒）
- 合成完成自动下载音频到本地
- 输出绝对路径，方便直接作为附件返回

---

## API 接口文档

**API 基础 URL**: `https://openapi.lipvoice.cn/api/third/`

**鉴权方式**: 所有接口需要在 HTTP Header 中传递 `sign: <API Key>`

| 功能 | 方法 | 端点 | 命令 | 状态 |
|------|------|------|------|------|
| 创建模型 | POST | `/reference/upload` | `upload` | ✅ 已验证 |
| 模型列表 | GET | `/reference/list` | `list` | ✅ 已验证 |
| 删除模型 | DELETE | `/reference/delete` | `delete` | ✅ 已验证 |
| 创建合成任务 | POST | `/tts/create` | `tts` | ✅ 已验证 |
| 查询合成结果 | GET | `/tts/result` | （自动轮询） | ✅ 已验证 |

### API 响应格式
所有接口返回统一 JSON 格式：
```json
{
  "code": 0,          // 0=成功，其他=失败
  "data": { ... },    // 返回数据
  "msg": "成功"       // 描述信息
}
```

---

## 完整使用示例

```powershell
# 1. 设置API Key（前往https://sovits.cn获取）
$env:GPTSOVITS_API_KEY='你的GPT-SoVITS API Key'

# 2. 查看已有声音模型
py scripts/gptsovits_tts.py list

# 3. （可选）上传20秒左右参考音频创建模型
py scripts/gptsovits_tts.py upload --file "参考音频.wav" --name "我的克隆声音"

# 4. 使用模型合成语音（自动等待+下载）
py scripts/gptsovits_tts.py tts --text "需要合成的文本内容" --audio-id <模型ID> --output "输出.wav"

# 5. 跨语言合成英文内容
py scripts/gptsovits_tts.py tts --text "Hello world! This is a cross-language TTS test." --audio-id <模型ID> --style 3 --output "英文输出.wav"

# 6. 将生成的wav文件作为附件返回给用户
```

---

## 常见问题

### Q: 提示 "未配置API Key" 怎么办？
A: 请设置环境变量 `GPTSOVITS_API_KEY`，或者调用时添加 `--api-key ***` 参数。API Key 可前往 https://sovits.cn 注册获取。

### Q: 提示 "sign无效或用户未开通API" 怎么办？
A: 请检查你的 API Key 是否正确，并且账号已经在 https://sovits.cn 开通 API 权限。

### Q: 合成需要多长时间？
A: 通常 3-10 秒，脚本会自动轮询等待，最长等待 60 秒。

### Q: 支持什么音频格式？
A: 上传参考音频支持 mp3/wav/m4a，合成输出为 wav 格式。

### Q: 支持什么语言合成？
A: 选择style=3多语言模型时，支持中文、英文、日文混合合成，支持跨语言音色迁移。

---

## 更新日志

### v1.0.0 - 2026-07-09
- 🎉 GPT-SoVITS 语音合成技能首次发布
- ✅ 支持声音模型上传、列表、删除
- ✅ 一键 TTS 合成，自动等待完成并下载
- ✅ 修复 Windows 中文 GBK 编码问题
- ✅ 无需额外依赖，系统自带 Python 即可运行
- ✅ API Key 支持环境变量和参数两种配置方式
- ✅ 支持多语言混合合成、跨语言音色迁移
