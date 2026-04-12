---
library_name: l0ng-api
---
<div align="center">
  <img src="https://raw.githubusercontent.com/nonmex9-design/L0NG---API/refs/heads/main/main/fullimage_v2.png" width="50%" height="30%" alt="L0NG" />
</div>

<hr>

<div align="center" style="line-height: 1;">
  <a href="https://github.com/nonmex9-design/L0NG---API" target="_blank" style="margin: 2px;">
    <img alt="Homepage" src="https://img.shields.io/badge/Homepage-L0NG-536af5?color=536af5&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://nonmex9-design.github.io/L0NG---API" target="_blank" style="margin: 2px;">
    <img alt="Chat" src="https://img.shields.io/badge/🤖%20Chat-L0NG-536af5?color=536af5&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://github.com/nonmex9-design/L0NG---API/blob/main/api-docs.html" target="_blank" style="margin: 2px;">
    <img alt="Documentation" src="https://img.shields.io/badge/📖%20Docs-API%20Reference-ffc107?color=ffc107&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<div align="center" style="line-height: 1;">
  <a href="https://discord.gg/yourserver" target="_blank" style="margin: 2px;">
    <img alt="Discord" src="https://img.shields.io/badge/Discord-L0NG%20Community-7289da?logo=discord&logoColor=white&color=7289da" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://twitter.com/l0ng_ai" target="_blank" style="margin: 2px;">
    <img alt="Twitter Follow" src="https://img.shields.io/badge/Twitter-@l0ng__ai-white?logo=x&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<div align="center" style="line-height: 1;">
  <a href="https://github.com/nonmex9-design/L0NG---API/blob/main/LICENSE" style="margin: 2px;">
    <img alt="License" src="https://img.shields.io/badge/License-MIT-f5de53?&color=f5de53" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<p align="center">
  <a href="https://github.com/nonmex9-design/L0NG---API"><b>GitHub Repository</b> 👁️</a>
</p>

## 1. Introduction

**L0NG** is an AI chat interface that learns from every conversation. It combines a local n‑gram model with cloud‑assisted inference, offering three performance tiers: **Instant**, **Expert**, and **Apex**. The L0NG API provides programmatic access to all core capabilities—conversational AI, image analysis, and model training—via a simple REST interface.

* **Base URL:** `https://chat.monyephoyn.workers.dev`
* **Format:** JSON in, JSON out
* **CORS:** Enabled for all origins
* **Authentication:** Not required for basic endpoints; admin routes need an `X-Owner-Email` header

## 2. Quick Start

```bash
curl -X POST [https://chat.monyephoyn.workers.dev/chat](https://chat.monyephoyn.workers.dev/chat) \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello, who are you?", "modelMode": "expert"}'
````

**Response:**

```json
{
  "response": "Hello! I'm L0NG, an AI assistant that learns from our conversations.",
  "vocabSize": 12450,
  "usedGroq": false
}
```

## 3\. Authentication & Rate Limits

| Tier | Limit | Requirements |
| :--- | :--- | :--- |
| **Free** | 20 messages / session | None |
| **Signed‑in** | 50 messages / session | Google OAuth (via chat interface) |
| **Admin** | Higher limits (custom) | `X-Owner-Email` header required |

Excessive requests may be throttled per IP. For production use, consider signing in or contacting us for increased limits.

## 4\. API Reference

### 4.1 Chat Completion

`POST /chat`

Send a message to L0NG and receive an AI‑generated response.

#### Request Body

| Field | Type | Description | Default |
| :--- | :--- | :--- | :--- |
| `message` | string | **Required.** The user's message. | – |
| `sessionId` | string | Unique session identifier. Used to maintain conversation context. | `"default"` |
| `userId` | string | User identifier for tracking and personalization. | – |
| `modelMode` | string | `"instant"`, `"expert"`, or `"apex"`. Controls response quality/speed trade‑off. | `"expert"` |
| `raw` | boolean | If `true`, bypasses Groq assistance and uses only the local n‑gram model. | `false` |
| `imageBase64` | string | Base64‑encoded image for vision analysis. | – |
| `temperature` | number | Creativity (0.0–1.0). Higher values produce more random outputs. | `0.7` |
| `maxTokens` | number | Maximum number of tokens in the response. | `80` |
| `useSearch` | boolean | Enables web search augmentation (requires `searchEnabled` on client). | `false` |
| `deepThink` | boolean | Activates deeper reasoning (chain‑of‑thought). | `false` |
| `systemPrompt` | string | Custom system prompt to guide the assistant's behavior. | – |

#### Response Body

| Field | Type | Description |
| :--- | :--- | :--- |
| `response` | string | The AI's reply. |
| `thought` | string | Internal reasoning (if `deepThink` was `true`). |
| `vocabSize` | number | Current size of the local n‑gram vocabulary. |
| `usedGroq` | boolean | Whether Groq was used to generate this response. |

#### Example: Basic Chat

```bash
curl -X POST [https://chat.monyephoyn.workers.dev/chat](https://chat.monyephoyn.workers.dev/chat) \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Explain quantum computing in one sentence.",
    "modelMode": "expert"
  }'
```

### 4.2 Train Local Model

`POST /train`

Feeds custom text into the local n‑gram model, improving its understanding of domain‑specific language.

#### Request Body

| Field | Type | Description |
| :--- | :--- | :--- |
| `text` | string | **Required.** Text to learn from. |

#### Example

```bash
curl -X POST [https://chat.monyephoyn.workers.dev/train](https://chat.monyephoyn.workers.dev/train) \
  -H "Content-Type: application/json" \
  -d '{"text": "L0NG is an adaptive AI assistant that learns from user interactions."}'
```

**Response:**

```json
{ 
  "success": true, 
  "vocabSize": 12512 
}
```

### 4.3 Upload Image

`POST /upload`

Upload an image for later use in chat requests. Returns both a URL and base64 data.

  * **Content-Type:** `multipart/form-data`
  * **Max file size:** 5 MB

#### Form Data

| Field | Type | Description |
| :--- | :--- | :--- |
| `file` | file | **Required.** Image file (PNG, JPG, etc.) |

### 4.4 Health Check

`GET /health`

Returns the current status and vocabulary size of the local model.

**Response:**

```json
{
  "status": "ok",
  "vocabSize": 12450,
  "uptime": 86400
}
```

## 5\. Code Examples

\<div align="center"\>

| Language | Example |
| :--- | :--- |
| **curl** | `curl -X POST https://chat.monyephoyn.workers.dev/chat -H "Content-Type: application/json" -d '{"message":"Hello"}'` |
| **Python** | `requests.post("https://chat.monyephoyn.workers.dev/chat", json={"message": "Hello"})` |
| **Node.js** | `fetch("https://chat.monyephoyn.workers.dev/chat", {method:"POST", body: JSON.stringify({message:"Hello"})})` |

\</div\>

**Python (full example):**

```python
import requests

response = requests.post(
    "[https://chat.monyephoyn.workers.dev/chat](https://chat.monyephoyn.workers.dev/chat)",
    json={
        "message": "Write a haiku about coding.",
        "modelMode": "expert",
        "temperature": 0.8
    }
)

print(response.json()["response"])
```

**Node.js (full example):**

```javascript
fetch("[https://chat.monyephoyn.workers.dev/chat](https://chat.monyephoyn.workers.dev/chat)", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    message: "Write a haiku about coding.",
    modelMode: "expert",
    temperature: 0.8
  })
})
.then(r => r.json())
.then(data => console.log(data.response));
```

## 6\. Error Codes

| Status | Meaning |
| :--- | :--- |
| 200 | Success |
| 400 | Bad request – missing or invalid parameters |
| 429 | Rate limit exceeded – wait and retry |
| 500 | Internal server error – please report |
| 503 | Service unavailable – temporary overload or maintenance |

## 7\. License

This API and its documentation are licensed under the [MIT License](https://www.google.com/search?q=LICENSE).

## 8\. Contact

  * **Discord Community:** [Join our Discord](https://discord.gg/EcySwhb8cT)
  * **Email:** [nonmex9@gmail.com](mailto:nonmex9@gmail.com)

\<p align="center"\>
\<b\>Built by Fendson H. (RoCode)\</b\>
\</p\>
```
