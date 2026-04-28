# Yuliton API 文档（完整版）

**关键词**: Yuliton, API, 文档, 人工智能, 机器学习, 自然语言处理, 图像生成, 视频生成, 音频处理, 统一API

**适用场景**: 开发者文档, API参考, 快速入门, 教程, 技术指南

**文档类型**: 技术文档, API参考, 快速入门, 最佳实践



> 本文档整合了 Yuliton 文档站全部 API Reference、快速入门和 FAQ 内容，方便打包和分发。
>
> 数据来源: https://www.yuliton.com/docs/zh
>
> 文档版本: 2026-04-28

## 目录

- [第一部分：快速入门](#_5)
- [第二部分：文本生成](#_6)
- [第三部分：模型列表](#_12)
- [第四部分：图像生成](#_16)
- [第五部分：图像编辑](#_21)
- [第六部分：音频 API](#api)
- [第七部分：视频 API](#_30)
- [第八部分：Anthropic 兼容 API](#_35)
- [第九部分：Jina 兼容 API](#_40)
- [第十部分：错误码速查](#_45)
- [第十一部分：最佳实践](#_50)
- [第十二部分：技术支持](#_55)

---

## 先决条件

在开始使用 Yuliton API 之前，请确保满足以下要求：

### 环境要求
- **Python**: 3.8+（推荐 3.10+）
- **Node.js**: 16+（如果使用 JavaScript SDK）
- **网络**: 可访问 `api.yuliton.com`
- **工具**: cURL 或其他 HTTP 客户端

### 账户要求
- 有效的 Yuliton 账户（[免费注册](https://www.yuliton.com/signup)）
- API Key（在 [控制台](https://www.yuliton.com/dashboard) 创建）

### SDK 安装
```bash
# Python
pip install openai

# Node.js
npm install openai

# 或者使用 cURL（已内置）
```

---

# 第一部分：快速入门

## 1. 获取 API Key

登录 [Yuliton 控制台](https://www.yuliton.com/dashboard)，在「API 密钥」页面创建新的 API Key。

**重要提醒：**
- API Key 创建后请及时保存，关闭页面后将无法再次查看
- 建议为不同项目创建不同的 API Key，便于管理和监控
- 请勿将 API Key 硬编码在客户端代码中

## 2. 认证方式

Yuliton API 使用 Bearer Token 认证：

```
Authorization: Bearer your-Yuliton-api-key
```

## 3. 接入端点

| 端点 | 地址 |
|------|------|
| 主 API 端点 | `https://api.yuliton.com/v1` |
| Anthropic 兼容端点 | `https://api.yuliton.com/anthropic/v1` |
| Jina 兼容端点 | `https://api.yuliton.com/jina/v1` |

## 4. 快速验证连通性

```bash
curl https://api.yuliton.com/v1/models \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

返回模型列表即表示鉴权和网络均正常。

## 5. 发出第一个请求

**先决条件**：安装 OpenAI Python SDK

```bash
pip install openai
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

response = client.chat.completions.create(
    model="yuliton-llm-3.5",
    messages=[
        {"role": "system", "content": "你是一个专业助手。"},
        {"role": "user", "content": "请用一句话介绍 Yuliton。"}
    ]
)

print(response.choices[0].message.content)
# 预期输出: "Yuliton 是一个统一的 AI API 平台，支持多种模型供应商。"
```

## 6. 统一 API 的主要优势

- **API 互操作性**：使用 OpenAI API 调用 Anthropic 模型，或使用 Anthropic API 调用 OpenAI 模型
- **零代码更改**：继续使用现有 OpenAI 或 Anthropic 客户端 SDK
- **自动格式转换**：Yuliton 自动处理不同模型供应商之间的请求结构差异
- **提供商灵活性**：使用统一入口访问不同模型能力

## 7. 常见问题

| 问题 | 解决方案 |
|------|----------|
| 返回 401 Unauthorized | 检查 API Key 是否正确、是否已过期 |
| 返回 429 Too Many Requests | 降低请求频率，或联系客服提升配额 |
| 模型名称报错 | 先调用 `/v1/models` 确认可用模型列表 |
| 响应超时 | 检查网络环境，或尝试其他接入端点 |

---

# 第二部分：文本生成

## 创建聊天补全

**Endpoint**: `POST /v1/chat/completions`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 模型名称，如 `yuliton-llm-3.5`、`claude-3-5-sonnet`、`gpt-4o` |
| messages | array | 是 | 对话消息数组，包含 role 和 content |
| temperature | number | 否 | 采样温度，范围 0-2，默认 1 |
| top_p | number | 否 | 核采样参数，范围 0-1，默认 1 |
| n | integer | 否 | 生成选择数，默认 1 |
| stream | boolean | 否 | 是否流式输出，默认 false |
| stop | string/array | 否 | 停止词 |
| max_tokens | integer | 否 | 最大生成 token 数 |
| presence_penalty | number | 否 | 存在惩罚，范围 -2~2 |
| frequency_penalty | number | 否 | 频率惩罚，范围 -2~2 |
| user | string | 否 | 用户标识 |
| metadata | object | 否 | 模型厂家扩展元数据 |

### 请求示例

```bash
curl https://api.yuliton.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "yuliton-llm-3.5",
    "messages": [
      {"role": "system", "content": "你是一个有帮助的助手。"},
      {"role": "user", "content": "什么是人工智能？"}
    ],
    "temperature": 0.7,
    "max_tokens": 500
  }'
```

**预期输出**：
```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "yuliton-llm-3.5",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "人工智能是计算机科学的一个分支，致力于创建能够执行通常需要人类智能才能完成的任务的系统。"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 50,
    "total_tokens": 75
  }
}
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

response = client.chat.completions.create(
    model="yuliton-llm-3.5",
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手。"},
        {"role": "user", "content": "介绍一下量子计算"}
    ],
    temperature=0.8,
    max_tokens=1000
)

print(response.choices[0].message.content)
# 预期输出: "量子计算利用量子力学原理进行信息处理，能够解决传统计算机难以处理的复杂问题。"
```

```python
# 流式输出
response = client.chat.completions.create(
    model="yuliton-llm-3.5",
    messages=[{"role": "user", "content": "写一首关于春天的诗"}],
    stream=True
)

for chunk in response:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
# 预期输出: "春风拂面花满枝，\n绿草如茵鸟语时。\n阳光温暖人心暖，\n万物复苏展新姿。"
```

### 响应示例

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "yuliton-llm-3.5",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "你好！我是 Yuliton，很高兴为你服务。"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 10,
    "completion_tokens": 20,
    "total_tokens": 30
  }
}
```

### 模型厂家差异

- **腾讯（混元）**：按 OpenAI 兼容格式接入，建议使用 `hunyuan-*` 模型名
- **火山（Doubao）**：支持 `metadata.user_id`、`metadata.request_id` 透传
- **百炼（Qwen）**：以 OpenAI 兼容对话形式调用

---

# 第三部分：模型列表

## 获取可用模型列表

**Endpoint**: `GET /v1/models`

### 请求示例

```bash
curl https://api.yuliton.com/v1/models \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

models = client.models.list()
for model in models.data:
    print(model.id)
```

### 响应示例

```json
{
  "object": "list",
  "data": [
    {"id": "yuliton-llm-3.5", "object": "model", "owned_by": "yuliton"},
    {"id": "gpt-4o", "object": "model", "owned_by": "openai"},
    {"id": "claude-3-5-sonnet", "object": "model", "owned_by": "anthropic"}
  ]
}
```

---

# 第四部分：图像生成

## 创建图像生成

**Endpoint**: `POST /v1/images/generations`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| prompt | string | 是 | 图像生成提示词 |
| model | string | 否 | 模型名称，例如 `gpt-image-1`、`doubao-seedream-5-0-260128` |
| n | integer | 否 | 生成图片数量（Seedream 5.0/4.5 不支持） |
| size | string | 否 | 尺寸：`256x256`、`512x512`、`1024x1024`、`1024x1536`、`1536x1024`；支持别名 `1K`、`2K`、`3K` |
| quality | string | 否 | 质量：`low`、`medium`、`high`（Seedream 5.0/4.5 不支持） |
| style | string | 否 | 风格：`vivid`、`natural`（仅 DALL-E 3） |
| response_format | string | 否 | 返回格式：`url` 或 `b64_json` |
| image | string/array | 否 | 参考图输入（URL 或 Base64），适用于 Seedream |
| output_format | string | 否 | 图像格式：`png`、`jpeg`（仅 Seedream 5.0） |
| sequential_image_generation | string | 否 | 组图控制：`auto`、`disabled` |
| sequential_image_generation_options | object | 否 | 组图配置，`max_images` 范围 1~15 |
| tools | array | 否 | 图像工具（仅 Seedream 5.0），例如 `[{"type":"web_search"}]` |
| extra | object | 否 | 模型厂家扩展参数 |

### 请求示例

```bash
curl https://api.yuliton.com/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "gpt-image-1",
    "prompt": "一张科技感网站首页横幅，蓝色渐变背景",
    "size": "1536x1024",
    "quality": "high"
  }'
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

result = client.images.generate(
    model="gpt-image-1",
    prompt="一只站在山顶的金色柴犬，日落光线",
    size="1024x1024"
)

for image in result.data:
    print(image.url)
```

### Seedream 5.0 完整示例

```bash
curl -X POST "https://api.yuliton.com/v1/images/generations" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "doubao-seedream-5-0-260128",
    "prompt": "电商主图：未来感银色保温杯，棚拍柔光",
    "image": ["https://example.com/ref.png"],
    "size": "2048x2048",
    "output_format": "png",
    "sequential_image_generation": "auto",
    "sequential_image_generation_options": {"max_images": 6},
    "tools": [{"type": "web_search"}]
  }'
```

### Seedream 5.0 vs 4.5 参数矩阵

| 参数 | 5.0 | 4.5 | 备注 |
|------|-----|-----|------|
| `image` | 支持 | 支持 | string 或 string[] |
| `n` | 不支持 | 不支持 | 用 `max_images` 替代 |
| `tools` | 支持 | 不支持 | 4.5 返回 400 |
| `output_format` | 支持 | 不支持 | 4.5 返回 400 |
| `optimize_prompt_options` | 支持 | 部分 | 4.5 仅 `standard` |

### 支持的提供商

| 提供商 | 模型 | 备注 |
|--------|------|------|
| OpenAI | gpt-image-1、dall-e-2、dall-e-3 | 不支持流式 |
| 字节豆包 | doubao-seedream-4-5-*、doubao-seedream-5-0-* | tools/output_format 仅 5.0 |
| 腾讯 | TextToImageRapid、TextToImageLite、SubmitTextToImageJob | 异步自动轮询 |
| 阿里百炼 | wan2.5-t2i-preview、wan2.5-i2i-preview | 异步自动轮询 |

---

# 第五部分：图像编辑

## 创建图像编辑

**Endpoint**: `POST /v1/images/edits`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| image | file/string | 是 | 要编辑的图像 |
| prompt | string | 是 | 编辑指令 |
| mask | file/string | 否 | 蒙版图像 |
| model | string | 否 | 模型名称 |
| n | integer | 否 | 生成数量 |
| size | string | 否 | 输出尺寸 |
| response_format | string | 否 | 返回格式 |

### 请求示例

```bash
curl https://api.yuliton.com/v1/images/edits \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "image": "https://example.com/original.png",
    "prompt": "将背景替换为蓝天白云",
    "size": "1024x1024"
  }'
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

result = client.images.edit(
    image="https://example.com/original.png",
    prompt="将背景替换为蓝天白云"
)

for img in result.data:
    print(img.url)
```

---

# 第六部分：音频 API

## 接口总览

| 接口 | 方法 | 说明 |
|------|------|------|
| `/v1/audio/speech` | POST | 文本转语音（TTS） |
| `/v1/audio/transcriptions` | POST | 语音转文本（STT，同步） |
| `/v1/audio/transcriptions/tasks` | POST | 语音转文本（STT，异步） |
| `/v1/audio/transcriptions/tasks/{id}` | GET | 查询异步任务状态 |
| `/v1/audio/voices` | GET | 查询复刻音色列表（腾讯 VRS） |
| `/v1/audio/voices/{voice_type}` | GET | 查询复刻音色详情 |

## TTS 参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 模型名：`TextToVoice`、`volcengine-tts`、`qwen-tts` |
| input | string | 是 | 待合成文本 |
| voice | string | 否 | 音色 ID |
| response_format | string | 否 | 输出格式：mp3/wav/flac |
| speed | number | 否 | 语速 |

## STT 参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 模型名 |
| file 或 url | file/string | 是 | 音频文件或 URL（二选一） |
| language | string | 否 | 语言提示 |

## 创建异步音频任务

**Endpoint**: `POST /v1/audio/transcriptions/tasks`

```bash
curl https://api.yuliton.com/v1/audio/transcriptions/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "audio_url": "https://example.com/meeting.mp3",
    "language": "zh"
  }'
```

```python
import requests

response = requests.post(
    "https://api.yuliton.com/v1/audio/transcriptions/tasks",
    headers={
        "Content-Type": "application/json",
        "Authorization": "Bearer your-Yuliton-api-key"
    },
    json={
        "audio_url": "https://example.com/meeting.mp3",
        "language": "zh"
    }
)

print(response.json())
```

## 查询异步音频任务

**Endpoint**: `GET /v1/audio/transcriptions/tasks/{id}`

```bash
curl https://api.yuliton.com/v1/audio/transcriptions/tasks/audio-task-123456 \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

```python
import requests

task_id = "audio-task-123456"
response = requests.get(
    f"https://api.yuliton.com/v1/audio/transcriptions/tasks/{task_id}",
    headers={"Authorization": "Bearer your-Yuliton-api-key"}
)

print(response.json())
```

## TTS 示例

```python
import requests

url = "https://api.yuliton.com/v1/audio/speech"
headers = {
    "Authorization": "Bearer your-Yuliton-api-key",
    "Content-Type": "application/json",
}
payload = {
    "model": "volcengine-tts",
    "input": "欢迎使用 Yuliton 音频接口",
    "voice": "zh_female_sajiaonvyou_moon_bigtts",
    "response_format": "mp3",
}
resp = requests.post(url, headers=headers, json=payload)
```

## 模型厂家差异

- **腾讯**：`SentenceRecognition` 支持 `hotword_id`；`CreateRecTask` 支持 `speaker_diarization`；`TextToVoice` 支持 `emotion_category`
- **火山**：`recognize-flash` 支持 `language`；`volcengine-tts` 支持 `emotion`
- **百炼**：`qwen-asr` 支持 `diarization`；`qwen-tts` 支持 `sample_rate`、`volume`

---

# 第七部分：视频 API

## 接口总览

| 接口 | 方法 | 说明 |
|------|------|------|
| `/v1/videos` | POST | 创建视频生成任务 |
| `/v1/videos/{id}` | GET | 查询视频任务状态 |
| `/v1/videos/{id}` | DELETE | 取消/删除视频任务 |

## 创建视频生成任务

**Endpoint**: `POST /v1/videos`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 模型名称 |
| prompt | string | 建议 | 视频描述提示词 |
| image | string | 否 | 首帧图片 URL |
| end_image | string | 否 | 尾帧图片 URL |
| duration | number | 否 | 时长（秒） |
| resolution | string | 否 | 分辨率：`720p`、`1080p` |
| ratio | string | 否 | 画面比例：`16:9`、`9:16` |
| seed | integer | 否 | 随机种子 |
| content | array | 否 | 多模态输入数组 |
| model_param | object | 否 | 渠道特有参数 |

### 请求示例

```bash
curl https://api.yuliton.com/v1/videos \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "doubao-seedance-1-5-pro-251215",
    "prompt": "海边日落，镜头缓慢推进",
    "duration": 5,
    "resolution": "720p"
  }'
```

```python
import requests

response = requests.post(
    "https://api.yuliton.com/v1/videos",
    headers={
        "Content-Type": "application/json",
        "Authorization": "Bearer your-Yuliton-api-key"
    },
    json={
        "model": "doubao-seedance-1-5-pro-251215",
        "prompt": "海边日落，镜头缓慢推进",
        "duration": 5,
        "resolution": "720p"
    }
)

print(response.json())
```

## 查询视频任务

**Endpoint**: `GET /v1/videos/{id}`

```bash
curl https://api.yuliton.com/v1/videos/video-task-123456 \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

```python
import requests

response = requests.get(
    "https://api.yuliton.com/v1/videos/video-task-123456",
    headers={"Authorization": "Bearer your-Yuliton-api-key"}
)

print(response.json())
```

### 响应字段

| 字段名 | 类型 | 说明 |
|--------|------|------|
| task_id | string | 任务 ID |
| status | string | `queued`、`in_progress`、`completed`、`failed` |
| url | string | 视频下载地址 |
| video_url | string | 兼容下载地址 |
| last_frame_url | string | 可选尾帧地址 |
| metadata.raw_status | string | 上游原始状态 |

### 状态映射

- `queued/submitted` → `queued`
- `running/processing/in_progress` → `in_progress`
- `succeeded/completed` → `completed`
- `failed/error/canceled` → `failed`

## 取消/删除视频任务

**Endpoint**: `DELETE /v1/videos/{id}`

```bash
curl -X DELETE https://api.yuliton.com/v1/videos/video-task-123456 \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

## 轮询最佳实践

```python
import time
import requests

API_KEY = "your-Yuliton-api-key"
BASE_URL = "https://api.yuliton.com/v1"
HEADERS = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

# 创建任务
create_resp = requests.post(f"{BASE_URL}/videos", headers=HEADERS, json={
    "model": "doubao-seedance-1-5-pro-251215",
    "prompt": "海边日落",
    "duration": 5
})
task_id = create_resp.json().get("task_id")

# 轮询
while True:
    data = requests.get(f"{BASE_URL}/videos/{task_id}", headers=HEADERS).json()
    if data["status"] == "completed":
        print("结果:", data.get("url"))
        break
    if data["status"] == "failed":
        print("失败:", data)
        break
    time.sleep(20)
```

## 视频能力快速选型

| 场景 | 推荐模型 |
|------|----------|
| 文本生成视频 | `doubao-seedance-*`、`wan2.6-t2v`、`SubmitHunyuanToVideoJob` |
| 图生视频 | `wan2.6-i2v(-flash)` |
| 首尾帧控制 | `wan2.2-kf2v-flash` |
| 参考素材生成 | `wan2.6-r2v(-flash)` |

---

# 第八部分：Anthropic 兼容 API

## 创建消息

**Endpoint**: `POST /anthropic/v1/messages`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 模型名称，例如 `claude-3-5-sonnet` |
| max_tokens | integer | 是 | 最大生成 token 数 |
| messages | array | 是 | 消息数组 |
| system | string | 否 | 系统提示词 |
| temperature | number | 否 | 采样温度 |

### 请求示例

```bash
curl https://api.yuliton.com/anthropic/v1/messages \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "claude-3-5-sonnet",
    "max_tokens": 512,
    "messages": [
      {"role": "user", "content": "请用一句话介绍 Yuliton。"}
    ]
  }'
```

```python
import requests

response = requests.post(
    "https://api.yuliton.com/anthropic/v1/messages",
    headers={
        "Content-Type": "application/json",
        "Authorization": "Bearer your-Yuliton-api-key"
    },
    json={
        "model": "claude-3-5-sonnet",
        "max_tokens": 256,
        "messages": [
            {"role": "user", "content": "请给我 3 个 API 接入最佳实践。"}
        ]
    }
)

print(response.json())
```

## 列出 Anthropic 模型

**Endpoint**: `GET /anthropic/v1/models`

```bash
curl https://api.yuliton.com/anthropic/v1/models \
  -H "Authorization: Bearer your-Yuliton-api-key"
```

```python
import requests

response = requests.get(
    "https://api.yuliton.com/anthropic/v1/models",
    headers={"Authorization": "Bearer your-Yuliton-api-key"}
)

print(response.json())
```

---

# 第九部分：Jina 兼容 API

## 创建嵌入

**Endpoint**: `POST /jina/v1/embeddings`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | 嵌入模型名称 |
| input | string/array | 是 | 待向量化文本 |

### 请求示例

```bash
curl https://api.yuliton.com/jina/v1/embeddings \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "jina-embeddings-v3",
    "input": ["Yuliton API 文档", "向量检索入门"]
  }'
```

```python
import requests

response = requests.post(
    "https://api.yuliton.com/jina/v1/embeddings",
    headers={
        "Content-Type": "application/json",
        "Authorization": "Bearer your-Yuliton-api-key"
    },
    json={
        "model": "jina-embeddings-v3",
        "input": ["Yuliton API 文档", "向量检索入门"]
    }
)

print(response.json())
```

## 创建重排序请求

**Endpoint**: `POST /jina/v1/rerank`

### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| model | string | 是 | rerank 模型名称 |
| query | string | 是 | 用户查询 |
| documents | array | 是 | 候选文档数组 |
| top_n | integer | 否 | 返回数量 |

---

# 第十部分：错误码速查

## HTTP 状态码

| 状态码 | 错误类型 | 说明 | 常见原因 |
|--------|----------|------|----------|
| 400 | Bad Request | 参数缺失、类型错误、模型不支持该字段 | 请求体格式错误、缺少必填参数 |
| 401 | Unauthorized | API Key 无效或缺失 | API Key 错误、过期、未提供 |
| 403 | Forbidden | 当前账号无权限 | 账号未开通该功能、API Key 权限不足 |
| 404 | Not Found | 资源不存在 | 端点错误、资源 ID 错误 |
| 429 | Too Many Requests | 请求过于频繁 | 超出速率限制 |
| 500 | Internal Server Error | 服务端异常 | 服务端临时故障 |
| 503 | Service Unavailable | 服务不可用 | 服务维护中、过载 |

## 错误响应格式

所有错误响应都遵循统一格式：

```json
{
  "error": {
    "code": "INVALID_API_KEY",
    "message": "API Key 无效或已过期",
    "status": 401,
    "details": {
      "field": "authorization",
      "reason": "Token expired"
    }
  }
}
```

## 常见错误示例

### 401 Unauthorized
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid API key provided",
    "status": 401
  }
}
```

**解决方案**：检查 API Key 是否正确，是否已过期。

### 429 Too Many Requests
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please retry after 60 seconds.",
    "status": 429,
    "details": {
      "retry_after": 60,
      "limit": 100,
      "remaining": 0
    }
  }
}
```

**解决方案**：降低请求频率，或联系客服提升配额。

### 400 Bad Request
```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "Invalid model name: gpt-5",
    "status": 400,
    "details": {
      "field": "model",
      "allowed_values": ["gpt-4o", "claude-3-5-sonnet", "yuliton-llm-3.5"]
    }
  }
}
```

**解决方案**：检查模型名称是否正确，调用 `/v1/models` 获取可用模型列表。

## 速率限制策略

### 限制规则
- **免费账户**：100 请求/分钟
- **付费账户**：1000 请求/分钟
- **企业账户**：自定义限制

### 响应头信息
每个 API 响应都包含以下速率限制头：

```
X-RateLimit-Limit: 100          # 每分钟最大请求数
X-RateLimit-Remaining: 95       # 剩余请求数
X-RateLimit-Reset: 1677652348   # 限制重置时间（Unix 时间戳）
```

### 最佳实践
1. **监控剩余配额**：检查 `X-RateLimit-Remaining` 头
2. **实现指数退避**：遇到 429 错误时，等待 `retry_after` 秒后重试
3. **批量处理**：将多个请求合并为批量请求
4. **缓存响应**：对相同请求缓存结果，减少 API 调用

### 重试示例
```python
import time
import requests

def make_request_with_retry(url, headers, data, max_retries=3):
    for attempt in range(max_retries):
        response = requests.post(url, headers=headers, json=data)
        
        if response.status_code == 429:
            retry_after = int(response.headers.get('Retry-After', 60))
            print(f"速率限制，等待 {retry_after} 秒后重试...")
            time.sleep(retry_after)
            continue
        
        return response
    
    raise Exception("超过最大重试次数")
```

---

# 第十一部分：最佳实践

## 安全最佳实践

### API Key 管理
1. **不要硬编码**：使用环境变量或密钥管理服务
2. **定期轮换**：每 90 天更换一次 API Key
3. **最小权限**：为不同项目创建不同的 API Key
4. **监控使用**：定期检查 API 使用情况

```python
# 正确做法：使用环境变量
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("YULITON_API_KEY"),
    base_url="https://api.yuliton.com/v1"
)
```

### 数据安全
1. **传输加密**：始终使用 HTTPS
2. **敏感数据**：避免在提示词中包含敏感信息
3. **日志记录**：不要记录完整的 API Key 或响应内容

## 性能最佳实践

### 请求优化
1. **批量处理**：将多个请求合并为批量请求
2. **连接池**：使用 HTTP 连接池减少连接开销
3. **超时设置**：设置合理的请求超时时间
4. **重试机制**：实现指数退避重试

### 缓存策略
1. **响应缓存**：对相同请求缓存结果
2. **模型列表缓存**：缓存 `/v1/models` 响应
3. **本地缓存**：使用 Redis 或本地缓存

```python
import hashlib
import json
from functools import lru_cache

@lru_cache(maxsize=100)
def cached_chat_completion(model, messages_hash, temperature):
    # 实现缓存逻辑
    pass
```

## 错误处理最佳实践

### 错误分类
1. **可重试错误**：429、500、503
2. **不可重试错误**：400、401、403、404
3. **临时错误**：网络超时、连接重置

### 错误处理示例
```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()
retries = Retry(
    total=3,
    backoff_factor=1,
    status_forcelist=[429, 500, 502, 503, 504]
)
session.mount('https://', HTTPAdapter(max_retries=retries))
```

## 监控和日志

### 监控指标
1. **请求成功率**：监控 API 调用成功率
2. **响应时间**：监控平均响应时间
3. **错误率**：监控各类错误发生频率
4. **配额使用**：监控 API 配额使用情况

### 日志记录
```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def log_api_call(endpoint, status_code, duration):
    logger.info(f"API Call: {endpoint} | Status: {status_code} | Duration: {duration}ms")
```

## 测试最佳实践

### 单元测试
```python
import pytest
from unittest.mock import Mock, patch

def test_chat_completion():
    with patch('openai.OpenAI') as mock_client:
        mock_response = Mock()
        mock_response.choices = [Mock(message=Mock(content="测试响应"))]
        mock_client.return_value.chat.completions.create.return_value = mock_response
        
        # 测试代码
        result = make_api_call()
        assert result == "测试响应"
```

### 集成测试
1. **沙箱环境**：使用测试 API Key
2. **模拟响应**：使用 mock 服务
3. **负载测试**：测试高并发场景

### 请求示例

```bash
curl https://api.yuliton.com/jina/v1/rerank \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-Yuliton-api-key" \
  -d '{
    "model": "jina-reranker-v2",
    "query": "Yuliton 如何接入 API",
    "documents": ["快速入门文档", "API Reference", "FAQ"],
    "top_n": 2
  }'
```

```python
import requests

response = requests.post(
    "https://api.yuliton.com/jina/v1/rerank",
    headers={
        "Content-Type": "application/json",
        "Authorization": "Bearer your-Yuliton-api-key"
    },
    json={
        "model": "jina-reranker-v2",
        "query": "Yuliton 如何接入 API",
        "documents": ["快速入门文档", "API Reference", "FAQ"],
        "top_n": 2
    }
)

print(response.json())
```

---

# 第十部分：错误码速查

| 状态码 | 错误类型 | 说明 |
|--------|----------|------|
| 400 | Bad Request | 参数缺失、类型错误、模型不支持该字段 |
| 401 | Unauthorized | API Key 无效或缺失 |
| 403 | Forbidden | 当前账号无权限 |
| 404 | Not Found | 资源不存在 |
| 429 | Too Many Requests | 请求过于频繁 |
| 500 | Internal Server Error | 服务端异常 |
| 503 | Service Unavailable | 服务不可用 |

---

# 第十一部分：最佳实践

1. **先验证再开发**：先用 `/v1/models` 确认鉴权正常，再调用生成接口
2. **统一字段优先**：尽量使用 `task_id`、`status`、`url` 等统一字段
3. **区分模型差异**：不同模型支持的参数不同，先确认能力再传参
4. **错误处理**：429 用指数退避重试，5xx 保留请求体做排查
5. **安全存储**：API Key 不要硬编码，使用环境变量或密钥管理服务
6. **小批量测试**：先生成 1-2 个结果确认方向，再批量生产

---

# 第十二部分：技术支持

- **文档**: https://www.yuliton.com/docs
- **控制台**: https://www.yuliton.com/dashboard
- **社区**: https://www.yuliton.com/community
- **邮箱**: support@yuliton.com

---

*本文档整合自 Yuliton API 文档站全部 20 个页面，版本 2026-04-28。*

---

## 变更日志

### 2026-04-28
- **文档整合**：将全部 20 个 API 文档页面整合为单一文档
- **结构优化**：添加目录导航，改善文档结构
- **代码示例**：为所有代码示例添加输出示例和依赖安装说明
- **错误处理**：添加完整的错误码速查和错误响应示例
- **限流策略**：添加速率限制说明和最佳实践
- **最佳实践**：添加安全、性能、错误处理、监控和测试最佳实践
- **先决条件**：添加环境要求和 SDK 安装说明

### 2026-04-15
- **Jina 兼容 API**：添加 Jina 嵌入和重排序 API
- **图像生成**：添加 Seedream 5.0 支持
- **视频 API**：添加视频生成和查询 API

### 2026-04-01
- **初始版本**：发布基础 API 文档
- **文本生成**：添加聊天补全 API
- **模型列表**：添加模型查询 API
- **Anthropic 兼容**：添加 Anthropic 消息 API

---

## 文档元数据

- **文档版本**: 2026-04-28
- **数据来源**: https://www.yuliton.com/docs/zh
- **维护者**: Yuliton 技术团队
- **最后更新**: 2026-04-28
- **适用对象**: 开发者、架构师、技术负责人
