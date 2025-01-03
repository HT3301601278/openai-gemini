## 为什么选择这个项目

Gemini API 是[免费的](https://ai.google.dev/pricing "有使用限制！")，但是有很多工具只支持 OpenAI API。

本项目提供了一个免费的、兼容 OpenAI 的个人端点。

## 无服务器？

虽然它在云端运行，但不需要服务器维护。
它可以轻松部署到各种提供商的平台上（具有适合个人使用的慷慨限额）。

> [!提示]
> 在本地运行代理端点也是一个选择，
> 不过这更适合开发使用。

## 如何开始

你需要一个 Google 的[API密钥](https://makersuite.google.com/app/apikey)。

> [!重要]
> 即使你所在的地区不在[支持地区](https://ai.google.dev/gemini-api/docs/available-regions#available_regions)内，
> 使用 VPN 仍然可以获取密钥。

按照以下说明将项目部署到其中一个提供商。你需要在那里注册一个账户。

如果你选择"一键部署"，系统会先引导你完成仓库分叉的过程，
这对持续集成（CI）来说是必需的。

### 部署到 Vercel

[![使用 Vercel 部署](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/PublicAffairs/openai-gemini&repository-name=my-openai-gemini)
- 也可以使用[命令行工具](https://vercel.com/docs/cli)部署：
  `vercel deploy`
- 本地运行：`vercel dev`
- Vercel _Functions_ [限制](https://vercel.com/docs/functions/limitations)（使用 _Edge_ 运行时）

### 部署到 Netlify

[![部署到 Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/PublicAffairs/openai-gemini&integrationName=integrationName&integrationSlug=integrationSlug&integrationDescription=integrationDescription)
- 也可以使用[命令行工具](https://docs.netlify.com/cli/get-started/)部署：
  `netlify deploy`
- 本地运行：`netlify dev`
- 提供两个不同的 API 基础路径：
  - `/v1`（例如 `/v1/chat/completions` 端点）  
    _Functions_ [限制](https://docs.netlify.com/functions/get-started/?fn-language=js#synchronous-function-2)
  - `/edge/v1`  
    _Edge functions_ [限制](https://docs.netlify.com/edge-functions/limits/)

### 部署到 Cloudflare

[![部署到 Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/PublicAffairs/openai-gemini)
- 也可以通过将 [`src/worker.mjs`](src/worker.mjs) 的内容
  粘贴到 https://workers.cloudflare.com/playground 来手动部署（查看那里的 `Deploy` 按钮）。
- 也可以使用[命令行工具](https://developers.cloudflare.com/workers/wrangler/)部署：
  `wrangler deploy`
- 本地运行：`wrangler dev`
- _Worker_ [限制](https://developers.cloudflare.com/workers/platform/limits/#worker-limits)

### 部署到 Deno

详情请参见[这里](https://github.com/PublicAffairs/openai-gemini/discussions/19)。

### 本地运行 - 使用 Node、Deno、Bun

仅限 Node：`npm install`

然后运行 `npm run start` / `npm run start:deno` / `npm run start:bun`。

#### 开发模式（监视源代码变化）

仅限 Node：`npm install --include=dev`

然后运行：`npm run dev` / `npm run dev:deno` / `npm run dev:bun`。

## 如何使用
如果你在浏览器中打开新部署的站点，你只会看到 `404 Not Found` 消息。这是正常的，因为该 API 不是为直接浏览器访问设计的。
要使用它，你应该在软件设置中输入你的 API 地址和 Gemini API 密钥。

> [!注意]
> 并非所有软件工具都允许覆盖 OpenAI 端点，但很多都支持
>（不过这些设置有时可能藏得很深）。

通常，你应该按以下格式指定 API 基础地址：  
`https://my-super-proxy.vercel.app/v1`

相关字段可能标记为"_OpenAI 代理_"。
你可能需要在"_高级设置_"或类似部分下查找。
或者，它可能在某个配置文件中（查看相关文档了解详情）。

对于某些命令行工具，你可能需要设置环境变量，例如：
```sh
OPENAI_BASE_URL="https://my-super-proxy.vercel.app/v1"
```
_或者_：
```sh
OPENAI_API_BASE="https://my-super-proxy.vercel.app/v1"
```

## 模型

如果指定的[模型]名称以 "gemini-"、"learnlm-" 或 "models/" 开头，请求将使用该模型。
否则，将使用以下默认值：

- `chat/completions`: `gemini-1.5-pro-latest`
- `embeddings`: `text-embedding-004`

[模型]: https://ai.google.dev/gemini-api/docs/models/gemini

## 媒体

支持根据 OpenAI [规范]的[视觉]和[音频]输入。
通过 [`inlineData`](https://ai.google.dev/api/caching#Part) 实现。

[视觉]: https://platform.openai.com/docs/guides/vision
[音频]: https://platform.openai.com/docs/guides/audio?audio-generation-quickstart-example=audio-in
[规范]: https://platform.openai.com/docs/api-reference/chat/create

---

## 支持的 API 端点和适用参数

- [x] `chat/completions`

  目前，已实现了大多数适用于两个 API 的参数，
  但函数调用除外。
  <details>

  - [x] `messages`
      - [x] `content`
      - [x] `role`
          - [x] "system" (=>`system_instruction`)
          - [x] "user"
          - [x] "assistant"
          - [ ] "tool" (v1beta)
      - [ ] `name`
      - [ ] `tool_calls`
  - [x] `model`
  - [x] `frequency_penalty`
  - [ ] `logit_bias`
  - [ ] `logprobs`
  - [ ] `top_logprobs`
  - [x] `max_tokens`, `max_completion_tokens`
  - [x] `n` (`candidateCount` <8, 流式传输时不可用)
  - [x] `presence_penalty`
  - [x] `response_format`
      - [x] "json_object"
      - [x] "json_schema" (OpenAPI 3.0 schema 对象的部分子集)
      - [x] "text"
  - [ ] `seed`
  - [x] `stop`: string|array (`stopSequences` [1,5])
  - [x] `stream`
  - [x] `stream_options`
      - [x] `include_usage`
  - [x] `temperature` (OpenAI 为 0.0..2.0，但 Gemini 支持到无穷大)
  - [x] `top_p`
  - [ ] `tools` (v1beta)
  - [ ] `tool_choice` (v1beta)
  - [ ] `parallel_tool_calls`

  </details>
- [ ] `completions`
- [x] `embeddings`
- [x] `models`
