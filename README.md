# astrbot_plugin_koharu

Koharu 漫画翻译插件。通过 Koharu HTTP API 在 AstrBot 聊天中翻译漫画图片。

## 功能

- 使用指令 `漫画翻译` 触发漫画图片翻译。
- 支持单图和多图。
- 支持两种使用方式：
  - 发送 `/漫画翻译 Simplified Chinese` 并附带图片。
  - 先发送 `/漫画翻译`，再按提示发送图片。
- 支持 AstrBot WebUI 配置管理。
- 支持中文和英文 WebUI 文案，资源位于 `.astrbot-plugin/i18n/`。
- 可选将返回图片压缩为 WebP 或 JPG 后发送。
- 翻译结果保存到 `data/plugin_data/astrbot_plugin_koharu/outputs/`。

## 前置条件

- 请先完成 `Koharu(>=0.61.0,<=0.61.2)` 本体部署。
- 官方仓库：[Koharu](https://github.com/mayocream/koharu)
- 参考指南：[Koharu 安装指南](https://koharu.rs/zh-CN/how-to/install-koharu/)

插件可接受 `http://host:port` 或 `http://host:port/api/v1`。

## Koharu 调用流程

每次翻译请求会执行：

1. 调用 `GET /meta` 等待 Koharu 就绪。
2. 调用 `POST /projects` 创建 Koharu 项目。
3. 调用 `POST /pages` 上传图片。
4. 可选调用 `PUT /llm/current` 加载 LLM。
5. 调用 `POST /pipelines` 启动翻译 pipeline。
6. 调用 `GET /operations` 轮询任务状态。
7. 调用 `POST /projects/current/export` 导出 rendered 图片。
8. 可选压缩导出图片后，将翻译后的图片发送回聊天。

## 配置项

- `koharu_api_base_url`：Koharu HTTP API 地址。
- `target_language`：指令未指定语言时的默认目标语言。
- `pipeline_steps`：可选，逗号分隔的 engine id。留空则从 Koharu `/config` 读取当前选择的引擎。
- `auto_load_llm`：当 Koharu 没有预先加载翻译模型时启用。
- `llm_kind`、`llm_provider_id`、`llm_model_id`：LLM 加载目标。
- `pipeline_timeout_seconds`：等待 Koharu 翻译完成的最长时间。
- `max_images_per_request`：单次输入图片数限制。
- `max_send_images`：最多返回图片数，`0` 表示全部返回。
- `compress_return_images`：是否在发送前压缩返回图片，默认关闭。
- `return_image_format`：压缩返回图片格式，可选 `webp` 或 `jpg`。
- `return_image_quality`：压缩质量，范围 `1-100`，默认 `85`。
- `result_retention_policy`：翻译结果缓存策略，可选 `days`、`forever`、`none`。
- `result_retention_days`：按天保留时的保留天数，默认 `7` 天。

## 指令

```text
/漫画翻译
/漫画翻译 Simplified Chinese
/manga_translate English
```

如果指令消息没有附带图片，插件会等待同一会话中的下一条图片消息。

---

# English

Koharu manga translation plugin. It translates manga images in AstrBot chats through the Koharu HTTP API.

## Features

- Use the `漫画翻译` command to trigger manga image translation.
- Supports single-image and multi-image translation.
- Supports two usage styles:
  - Send `/漫画翻译 Simplified Chinese` with image(s) attached.
  - Send `/漫画翻译` first, then send image(s) when prompted.
- Supports AstrBot WebUI configuration.
- Supports Chinese and English WebUI text under `.astrbot-plugin/i18n/`.
- Optionally compresses returned images as WebP or JPG before sending.
- Stores translated output images under `data/plugin_data/astrbot_plugin_koharu/outputs/`.

## Prerequisites

- Deploy `Koharu(>=0.61.0,<=0.61.2)` first.
- Official repository: [Koharu](https://github.com/mayocream/koharu)
- Installation guide: [Koharu installation guide](https://koharu.rs/how-to/install-koharu/)

The plugin accepts either `http://host:port` or `http://host:port/api/v1`.

## Koharu Workflow

Each translation request runs the following workflow:

1. Call `GET /meta` and wait until Koharu is ready.
2. Call `POST /projects` to create a Koharu project.
3. Call `POST /pages` to upload image(s).
4. Optionally call `PUT /llm/current` to load an LLM.
5. Call `POST /pipelines` to start the translation pipeline.
6. Call `GET /operations` to poll the operation status.
7. Call `POST /projects/current/export` to export rendered image(s).
8. Optionally compress exported image(s), then send translated image(s) back to the chat.


## Configuration

- `koharu_api_base_url`: Koharu HTTP API address.
- `target_language`: Default target language when the command does not specify one.
- `pipeline_steps`: Optional comma-separated engine ids. Leave empty to read the currently selected engines from Koharu `/config`.
- `auto_load_llm`: Enable when Koharu has not preloaded a translation model.
- `llm_kind`, `llm_provider_id`, `llm_model_id`: LLM loading target.
- `pipeline_timeout_seconds`: Maximum time to wait for Koharu translation completion.
- `max_images_per_request`: Limit for input image count per request.
- `max_send_images`: Maximum number of images to send back. `0` means send all images.
- `compress_return_images`: Whether to compress returned images before sending. Disabled by default.
- `return_image_format`: Compressed return image format. Available values: `webp`, `jpg`.
- `return_image_quality`: Compression quality. Range: `1-100`. Default: `85`.
- `result_retention_policy`: Translated result cache policy. Available values: `days`, `forever`, `none`.
- `result_retention_days`: Retention days when using the `days` policy. Default is `7` days.

## Commands

```text
/漫画翻译
/漫画翻译 Simplified Chinese
/manga_translate English
```

If the command message has no attached image, the plugin waits for the next image message in the same session.
