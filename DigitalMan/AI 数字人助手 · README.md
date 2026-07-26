# AI 数字人助手 · README

> 一个开箱即用的 Web 数字人交互界面，集成大语言模型（LLM）对话、语音合成（TTS）与语音识别（ASR），支持自定义角色人设，并可灵活对接外部 API。

------

## 📌项目简介

本项目是一个**纯前端**的数字人助手 Demo，旨在提供一个轻量、可扩展的 AI 交互框架。它包含了：

- 🧠 **大模型对话**：默认支持 OpenAI 兼容的 API（如 GPT-3.5/4、Azure OpenAI、国内代理），也可轻松替换为其他模型（如文心一言、讯飞星火、自建模型）。
- 🎤 **语音输入**：基于 Web Speech API 实现语音识别（ASR），点击麦克风按钮即可说话输入。
- 🔊 **语音合成**：内置 Web Speech API 的 TTS，同时预留了接入外部语音合成服务（如阿里云、微软 Azure、百度）的接口。
- 🧑‍🎤 **角色人设**：通过 System Prompt 定义 AI 的性格、风格与世界观，支持从 `.txt` / `.json` 文件导入。
- 💬 **消息朗读**：鼠标悬停任意消息气泡，点击右下角播放按钮即可朗读该条内容。
- ⚙️ **配置持久化**：所有设置（API Key、人设、语音选择）自动保存在浏览器本地，刷新不丢失。

适合用于快速搭建 AI 聊天机器人、虚拟助手、客服演示、教育工具等场景。

------

## ✨ 功能特性

| 功能模块         | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| **对话聊天**     | 支持用户发送消息，AI 通过大模型（或模拟模式）生成回复        |
| **语音输入**     | 点击麦克风按钮，实时语音转文字，自动填入输入框并发送         |
| **语音合成**     | AI 回复后自动朗读（可开关），支持选择浏览器内置语音          |
| **消息朗读**     | 点击任意消息的朗读按钮，单独朗读该条内容，支持暂停/继续      |
| **角色人设**     | 自定义 System Prompt，定义 AI 性格、说话风格、世界观         |
| **人设文件导入** | 上传 `.txt` 或 `.json` 文件，一键填充人设内容                |
| **多模型切换**   | 通过设置面板配置 API Base URL、Model Name，轻松对接不同 LLM 服务 |
| **配置本地存储** | API Key、人设、语音偏好等自动保存至 `localStorage`           |

------

## 🚀 快速开始

### 1. 前置条件

- 现代浏览器（推荐 Chrome / Edge，支持 Web Speech API）
- （可选）大模型 API Key（如 OpenAI API Key）

### 2. 下载与运行

bash

```
# github的仓库
https://github.com/deepaway/AI-assist-Project/blob/main/DigitalMan/DigitalMan.html
# 由于是纯前端，直接打开 DigitalMan.html 即可
```



### 3. 首次使用

1. 点击右上角 **⚙️ 设置** 按钮，展开配置面板。
2. 填写 **API Key**（如需使用真实大模型，否则自动使用模拟模式）。
3. 可选：填写 **Base URL**（默认 `https://api.openai.com/v1`）和 **模型名称**（如 `gpt-3.5-turbo`）。
4. 可选：在 **角色人设** 文本框中输入 AI 的性格描述（如：“你是一位赛博朋克风格的导师，说话带点俏皮和神秘”）。
5. 点击 **保存设置**，即可开始对话。

------

## ⚙️ 配置选项详解

### 设置面板参数

| 参数             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| **API Key**      | 你的大模型 API 密钥（如 OpenAI 的 `sk-...`）。留空则使用模拟模式。 |
| **Base URL**     | API 端点地址，可替换为 Azure OpenAI、代理服务或自定义后端。  |
| **模型**         | 指定使用的模型名称（如 `gpt-4`、`gpt-3.5-turbo`）。          |
| **语音**         | 选择浏览器内置的 TTS 音色（列表自动填充）。                  |
| **自动朗读**     | 开启后，AI 每次回复都会自动朗读。                            |
| **角色人设**     | 一段文本，作为 System Prompt 注入大模型，用于定义 AI 的角色与风格。 |
| **上传人设文件** | 支持 `.txt` 或 `.json` 文件。若为 JSON，会尝试提取 `system_prompt` / `prompt` / `character` 等字段。 |

### 环境变量（本地存储）

所有配置保存在 `localStorage` 中，键名如下：

| 键名               | 对应值               |
| :----------------- | :------------------- |
| `dh_api_key`       | API Key              |
| `dh_base_url`      | Base URL             |
| `dh_model`         | 模型名称             |
| `dh_voice_name`    | 选中的语音名称       |
| `dh_auto_speak`    | `"true"` / `"false"` |
| `dh_system_prompt` | 角色人设内容         |

------

## 🔌 外部 API 接入指南

### 一、更换大模型 API

项目中的 `callRealAPI` 函数（位于 `<script>` 内）负责调用外部 LLM。默认使用 OpenAI 兼容的 `/chat/completions` 接口。

#### 修改方法

1. 在源码中搜索 `async function callRealAPI`。
2. 修改 `url`、`headers` 和 `body` 以适配你的 API。
3. 调整响应解析逻辑（目前取 `data.choices[0].message.content`）。

**示例：对接百度文心一言（ERNIE）**

javascript

```
async function callRealAPI(userMessage) {
    const accessToken = 'your_access_token'; // 需动态获取
    const url = `https://aip.baidubce.com/rpc/2.0/ai_custom/v1/wenxinworkshop/chat/completions?access_token=${accessToken}`;
    const apiMessages = []; // 构建消息历史（参照原逻辑）
    const response = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            messages: apiMessages,
            temperature: 0.8,
            // 其他参数
        })
    });
    const data = await response.json();
    return data.result; // 文心返回的字段是 result
}
```



> **注意**：密钥应通过后端代理，避免前端暴露。

------

### 二、更换外部语音合成（TTS）

当前 TTS 使用 `window.speechSynthesis`。若需接入专业 TTS 服务（如阿里云、微软 Azure），请替换 `speakText` 和 `speakMessageContent` 函数。

#### 实现步骤

1. 在对应函数中，调用外部 API 获取音频数据（通常为 MP3 / WAV）。
2. 使用 `Audio` 对象或 `Web Audio API` 播放。
3. 处理播放结束回调，更新 UI 状态。

**示例：使用阿里云 TTS**

javascript

```
async function speakText(text, callback) {
    const response = await fetch('https://your-tts-api.com/synthesize', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text, voice: 'zh-CN-XiaoxiaoNeural' })
    });
    const audioBlob = await response.blob();
    const audioUrl = URL.createObjectURL(audioBlob);
    const audio = new Audio(audioUrl);
    audio.onended = () => {
        URL.revokeObjectURL(audioUrl);
        if (callback) callback();
    };
    audio.play();
}
```



------

### 三、自定义语音识别（ASR）

目前使用 Web Speech API，如需替换为第三方 ASR（如讯飞、百度语音），可修改 `startVoiceInput` 函数，将录音数据发送至外部 API，并处理返回的文字。

------

## 📁 项目结构

本项目为单页应用，所有代码集中在一个 `DigitalMan.html` 文件中，便于快速部署和修改。

text

```
DigitalMan.html          # 完整页面（HTML + CSS + JavaScript）
```



------

## 🤝 如何贡献

欢迎提交 Issue 和 Pull Request。请遵循以下规范：

1. **提交前**：确保代码在 Chrome 最新版上运行通过。
2. **代码风格**：保持现有缩进与命名风格。
3. **文档同步**：若修改了核心功能，请更新本 README 相应章节。
4. **测试**：建议在真实 API 和模拟模式下分别验证。

------

## 📄 许可证

MIT © [deepaway]

------

## 🧩 扩展建议

- **部署**：可直接部署至 GitHub Pages、Vercel、Netlify 等静态托管服务。
- **后端代理**：为避免 API Key 泄露，可搭建简单后端转发请求，前端仅调用自己的服务。
- **多轮对话记忆**：当前保留最近 10 条对话作为上下文，可根据需要调整。
- **多语言支持**：修改 `recognition.lang` 和 `utterance.lang` 即可支持其他语言。

------

## ❓ 常见问题

**Q: 语音识别没有反应？**
A: 请确保使用 HTTPS 或 localhost，并允许浏览器麦克风权限。检查浏览器是否支持 Web Speech API（推荐 Chrome）。

**Q: 自动朗读没有声音？**
A: 检查“自动朗读”开关是否开启，以及系统音量是否正常。在设置面板中选择其他语音试试。

**Q: 如何清除所有配置？**
A: 在浏览器开发者工具中执行 `localStorage.clear()`，或手动删除 `dh_*` 键值。

**Q: 人设文件上传后没有生效？**
A: 上传后需要点击“保存设置”才会写入 localStorage。JSON 文件需包含 `system_prompt` 字段，或直接上传纯文本。
