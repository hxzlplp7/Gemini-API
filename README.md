<p align="center">
    <img src="https://raw.githubusercontent.com/HanaokaYuzu/Gemini-API/master/assets/banner.png" width="55%" alt="Gemini 横幅" align="center">
</p>
<p align="center">
    <a href="https://pypi.org/project/gemini-webapi">
        <img src="https://img.shields.io/pypi/v/gemini-webapi" alt="PyPI"></a>
    <a href="https://pepy.tech/project/gemini-webapi">
        <img src="https://static.pepy.tech/badge/gemini-webapi" alt="下载量"></a>
    <a href="https://github.com/HanaokaYuzu/Gemini-API/network/dependencies">
        <img src="https://img.shields.io/librariesio/github/HanaokaYuzu/Gemini-API" alt="依赖项"></a>
    <a href="https://github.com/HanaokaYuzu/Gemini-API/blob/master/LICENSE">
        <img src="https://img.shields.io/github/license/HanaokaYuzu/Gemini-API" alt="许可证"></a>
    <a href="https://github.com/psf/black">
        <img src="https://img.shields.io/badge/code%20style-black-000000.svg" alt="代码风格"></a>
</p>
<p align="center">
    <a href="https://star-history.com/#HanaokaYuzu/Gemini-API">
        <img src="https://img.shields.io/github/stars/HanaokaYuzu/Gemini-API?style=social" alt="GitHub 星标"></a>
    <a href="https://github.com/HanaokaYuzu/Gemini-API/issues">
        <img src="https://img.shields.io/github/issues/HanaokaYuzu/Gemini-API?style=social&logo=github" alt="GitHub 问题"></a>
    <a href="https://github.com/HanaokaYuzu/Gemini-API/actions/workflows/pypi-publish.yml">
        <img src="https://github.com/HanaokaYuzu/Gemini-API/actions/workflows/pypi-publish.yml/badge.svg" alt="CI"></a>
</p>

# <img src="https://raw.githubusercontent.com/HanaokaYuzu/Gemini-API/master/assets/logo.svg" width="35px" alt="Gemini 图标" /> Gemini-API

一个反向工程的异步 Python 封装库，用于 [Google Gemini](https://gemini.google.com) 网页应用（前身为 Bard）。

## 功能特性

- **持久化 Cookies** - 后台自动刷新 Cookies。为持续运行的服务优化。
- **图像生成** - 原生支持使用自然语言生成和修改图像。
- **扩展支持** - 支持开启 [Gemini 扩展](https://gemini.google.com/extensions)（如 YouTube 和 Gmail）生成内容。
- **分类输出** - 自动对响应中的文本、网页图片和 AI 生成的图片进行分类。
- **官方风格** - 提供一个简洁优雅的接口，灵感来源于 [Google Generative AI](https://ai.google.dev/tutorials/python_quickstart) 的官方 API。
- **异步** - 利用 `asyncio` 高效地运行生成任务并返回输出。

## 目录

- [功能特性](#功能特性)
- [目录](#目录)
- [安装](#安装)
- [认证](#认证)
- [使用方法](#使用方法)
  - [初始化](#初始化)
  - [选择语言模型](#选择语言模型)
  - [从文本生成内容](#从文本生成内容)
  - [使用文件生成内容](#使用文件生成内容)
  - [多轮对话](#多轮对话)
  - [继续之前的对话](#继续之前的对话)
  - [检索模型的思考过程](#检索模型的思考过程)
  - [检索响应中的图像](#检索响应中的图像)
  - [使用 Imagen3 生成图像](#使用-imagen3-生成图像)
  - [使用 Gemini 扩展生成内容](#使用-gemini-扩展生成内容)
  - [检查并切换到其他回复候选](#检查并切换到其他回复候选)
  - [控制日志级别](#控制日志级别)
- [参考资料](#参考资料)
- [星标关注者](#星标关注者)

## 安装

> [!注意]
>
> 此软件包需要 Python 3.10 或更高版本。

使用 pip 安装/更新软件包。

```bash
pip install -U gemini_webapi
```

可选地，该软件包提供了一种自动从本地浏览器导入 Cookies 的方法。要启用此功能，请同时安装 `browser-cookie3`。支持的平台和浏览器可以在[这里](https://github.com/borisbabic/browser_cookie3?tab=readme-ov-file#contribute)找到。

```bash
pip install -U browser-cookie3
```

## 认证

> [!提示]
>
> 如果已安装 `browser-cookie3`，您可以跳过此步骤直接进入[使用方法](#使用方法)部分。只需确保您已在浏览器中登录 <https://gemini.google.com>。

- 访问 <https://gemini.google.com> 并使用您的 Google 帐户登录。
- 按 F12 打开网页检查器，转到 `网络`(Network) 标签页并刷新页面。
- 点击任意请求，复制 `__Secure-1PSID` 和 `__Secure-1PSIDTS` 的 Cookie 值。

> [!注意]
>
> 如果您的应用程序部署在容器化环境（例如 Docker）中，您可能希望使用卷（volume）来持久化 Cookies，以避免每次容器重建时重新认证。
>
> 以下是一个示例 `docker-compose.yml` 文件的一部分：

```yaml
services:
    main:
        volumes:
            - ./gemini_cookies:/usr/local/lib/python3.12/site-packages/gemini_webapi/utils/temp
```

> [!注意]
>
> API 的自动 Cookie 刷新功能不需要 `browser-cookie3`，并且默认启用。它允许您保持 API 服务运行而无需担心 Cookie 过期。
>
> 此功能可能会导致您需要在浏览器中重新登录您的 Google 帐户。这是预期行为，不会影响 API 的功能。
>
> 为避免这种情况，建议从一个独立的浏览器会话中获取 Cookies，并尽快关闭它以实现最佳利用（例如，在浏览器的无痕模式下重新登录）。更多详情可以在[这里](https://github.com/HanaokaYuzu/Gemini-API/issues/6)找到。

## 使用方法

### 初始化

导入所需的包，并使用上一步获得的 Cookies 初始化客户端。成功初始化后，只要进程存在，API 就会在后台自动刷新 `__Secure-1PSIDTS`。

```python
import asyncio
from gemini_webapi import GeminiClient

# 将 "COOKIE VALUE HERE" 替换为您的实际 Cookie 值。
# 如果您的帐户没有 Secure_1PSIDTS，请将其留空。
Secure_1PSID = "COOKIE VALUE HERE"
Secure_1PSIDTS = "COOKIE VALUE HERE"

async def main():
    # 如果已安装 browser-cookie3，只需使用 `client = GeminiClient()`
    client = GeminiClient(Secure_1PSID, Secure_1PSIDTS, proxy=None)
    await client.init(timeout=30, auto_close=False, close_delay=300, auto_refresh=True)

asyncio.run(main())
```

> [!提示]
>
> `auto_close` 和 `close_delay` 是可选参数，用于在一段时间不活动后自动关闭客户端。此功能默认禁用。在像聊天机器人这样的持续运行的服务中，建议将 `auto_close` 设置为 `True`，并结合合理的 `close_delay` 秒数，以实现更好的资源管理。

### 选择语言模型

您可以通过将 `model` 参数传递给 `GeminiClient.generate_content` 或 `GeminiClient.start_chat` 来指定要使用的语言模型。默认值为 `unspecified`。

当前可用模型（截至 2025 年 2 月 5 日）：

- `unspecified` - 默认模型
- `gemini-2.0-flash` - Gemini 2.0 Flash
- `gemini-2.0-flash-thinking` - Gemini 2.0 Flash Thinking 实验版
- `gemini-2.5-flash` - Gemini 2.5 Flash
- `gemini-2.5-pro` - Gemini 2.5 Pro（有每日使用限制）

待更新模型（可能无法按预期工作）：

- `gemini-2.5-exp-advanced` - Gemini 2.5 Experimental Advanced **（需要 Gemini Advanced 帐户）**
- `gemini-2.0-exp-advanced` - Gemini 2.0 Experimental Advanced **（需要 Gemini Advanced 帐户）**

```python
from gemini_webapi.constants import Model

async def main():
    response1 = await client.generate_content(
        "你的语言模型版本是什么？仅回复版本号。",
        model=Model.G_2_0_FLASH,
    )
    print(f"模型版本 ({Model.G_2_0_FLASH.model_name}): {response1.text}")

    chat = client.start_chat(model="gemini-2.0-flash-thinking")
    response2 = await chat.send_message("你的语言模型版本是什么？仅回复版本号。")
    print(f"模型版本 (gemini-2.0-flash-thinking): {response2.text}")

asyncio.run(main())
```

### 从文本生成内容

通过调用 `GeminiClient.generate_content` 提出单轮快速问题。

```python
async def main():
    response = await client.generate_content("你好世界！")
    print(response.text)

asyncio.run(main())
```

> [!提示]
>
> 如果您只想查看响应文本，只需使用 `print(response)` 即可获得相同的输出。

### 使用文件生成内容

Gemini 支持文件输入，包括图像和文档。可选地，您可以将文件作为 `str` 或 `pathlib.Path` 的路径列表传递给 `GeminiClient.generate_content`，并附带文本提示。

```python
async def main():
    response = await client.generate_content(
            "介绍这两个文件的内容。它们之间有什么联系吗？",
            files=["assets/sample.pdf", Path("assets/banner.png")],
        )
    print(response.text)

asyncio.run(main())
```

### 多轮对话

如果您想保持对话的连续性，请使用 `GeminiClient.start_chat` 创建一个 `ChatSession` 对象，并通过它发送消息。对话历史将自动处理并在每轮后更新。

```python
async def main():
    chat = client.start_chat()
    response1 = await chat.send_message(
        "介绍这两个文件的内容。它们之间有什么联系吗？",
        files=["assets/sample.pdf", Path("assets/banner.png")],
    )
    print(response1.text)
    response2 = await chat.send_message(
        "使用图像生成工具修改横幅，使用另一种字体和设计。"
    )
    print(response2.text, response2.images, sep="\n\n----------------------------------\n\n")

asyncio.run(main())
```

> [!提示]
>
> 与 `GeminiClient.generate_content` 类似，`ChatSession.send_message` 也接受 `image` 作为可选参数。

### 继续之前的对话

要手动检索以前的对话，您可以在创建新的 `ChatSession` 时，将先前 `ChatSession` 的元数据传递给 `GeminiClient.start_chat`。或者，如果您需要在当前 Python 进程退出后访问它们，可以将先前的元数据持久化到文件或数据库中。

```python
async def main():
    # 开始一个新的聊天会话
    chat = client.start_chat()
    response = await chat.send_message("今天天气不错")

    # 保存聊天的元数据
    previous_session = chat.metadata

    # 加载之前的对话
    previous_chat = client.start_chat(metadata=previous_session)
    response = await previous_chat.send_message("我上一条消息是什么？")
    print(response)

asyncio.run(main())
```

### 检索模型的思考过程

当使用具有思考能力的模型时，模型的思考过程将填充在 `ModelOutput.thoughts` 中。

```python
async def main():
    response = await client.generate_content(
            "1+1等于几？", model="gemini-2.0-flash-thinking"
        )
    print(response.thoughts)
    print(response.text)

asyncio.run(main())
```

### 检索响应中的图像

API 输出中的图像存储为 `Image` 对象列表。您可以通过调用 `image.title`、`image.url` 和 `image.alt` 来分别访问图像的标题、URL 和描述。

```python
async def main():
    response = await client.generate_content("给我发一些猫的图片")
    for image in response.images:
        print(image, "\n\n----------------------------------\n")

asyncio.run(main())
```

### 使用 Imagen3 生成图像

您可以要求 Gemini 使用 Imagen3（Google 最新的 AI 图像生成器）通过自然语言生成和修改图像。

> [!重要]
>
> Google 对 Gemini 中的图像生成功能有一些限制，因此其可用性可能因地区/帐户而异。以下是摘自[官方文档](https://support.google.com/gemini/answer/14286560)的摘要（截至 2025 年 3 月 19 日）：
>
> > 此功能在任何特定 Gemini 应用中的可用性也受限于该应用支持的语言和国家/地区。
> >
> > 目前，此功能不适用于 18 岁以下的用户。
> >
> > 要使用此功能，您必须登录 Gemini 应用。

您可以通过调用 `Image.save()` 将从 Gemini 返回的图像保存到本地。可选地，您可以通过向该函数传递 `path` 和 `filename` 参数来指定文件路径和文件名，并通过传递 `skip_invalid_filename=True` 来跳过文件名无效的图像。此方法适用于 `WebImage` 和 `GeneratedImage`。

```python
async def main():
    response = await client.generate_content("生成一些猫的图片")
    for i, image in enumerate(response.images):
        await image.save(path="temp/", filename=f"cat_{i}.png", verbose=True)
        print(image, "\n\n----------------------------------\n")

asyncio.run(main())
```

> [!注意]
>
> 默认情况下，当被要求发送图像时（如上一个示例），Gemini 会发送从网络获取的图像，而不是使用 AI 模型生成图像，除非您在提示中明确要求“生成”图像。在此软件包中，网页图像和生成的图像被区别对待为 `WebImage` 和 `GeneratedImage`，并将在输出中自动分类。

### 使用 Gemini 扩展生成内容

> [!重要]
>
> 要在 API 中访问 Gemini 扩展，您必须首先在 [Gemini 网站](https://gemini.google.com/extensions)上激活它们。与图像生成一样，Google 对 Gemini 扩展的可用性也有一些限制。以下是摘自[官方文档](https://support.google.com/gemini/answer/13695044)的摘要（截至 2025 年 3 月 19 日）：
>
> > 要将应用连接到 Gemini，您必须开启“Gemini 应用活动记录”。
> >
> > 要使用此功能，您必须登录 Gemini 应用。
> >
> > 重要提示：如果您未满 18 岁，Google Workspace 和地图应用目前仅支持在 Gemini 中使用英语提示。

为您的帐户激活扩展后，您可以通过自然语言或在提示的开头使用“@”后跟扩展关键字来访问它们。

```python
async def main():
    response1 = await client.generate_content("@Gmail 我邮箱里最新的邮件是什么？")
    print(response1, "\n\n----------------------------------\n")

    response2 = await client.generate_content("@Youtube Taylor Swift 最新的动态是什么？")
    print(response2, "\n\n----------------------------------\n")

asyncio.run(main())
```

> [!注意]
>
> 关于可用地区的限制，实际上它仅要求您的 Google 帐户的**首选语言**设置为上述三种支持的语言之一。您可以在[这里](https://myaccount.google.com/language)更改您的语言设置。

### 检查并切换到其他回复候选

来自 Gemini 的响应通常包含多个具有不同生成内容的回复候选。您可以检查所有候选并选择一个来继续对话。默认情况下，会自动选择第一个候选。

```python
async def main():
    # 开始一个对话并列出所有回复候选
    chat = client.start_chat()
    response = await chat.send_message("给我推荐一本科学幻想小说。")
    for candidate in response.candidates:
        print(candidate, "\n\n----------------------------------\n")

    if len(response.candidates) > 1:
        # 通过手动选择候选来控制正在进行的对话流
        new_candidate = chat.choose_candidate(index=1)  # 此处选择第二个候选
        followup_response = await chat.send_message("告诉我更多关于它的信息。")  # 将基于所选候选生成内容
        print(new_candidate, followup_response, sep="\n\n----------------------------------\n\n")
    else:
        print("只有一个候选可用。")

asyncio.run(main())
```

### 控制日志级别

您可以将软件包的日志级别设置为以下值之一：`DEBUG`、`INFO`、`WARNING`、`ERROR` 和 `CRITICAL`。默认值为 `INFO`。

```python
from gemini_webapi import set_log_level

set_log_level("DEBUG")
```

## 参考资料

[Google AI Studio](https://ai.google.dev/tutorials/ai-studio_quickstart)

[acheong08/Bard](https://github.com/acheong08/Bard)

## 星标关注者

<p align="center">
    <a href="https://star-history.com/#HanaokaYuzu/Gemini-API">
        <img src="https://api.star-history.com/svg?repos=HanaokaYuzu/Gemini-API&type=Date" width="75%" alt="Star 历史图表"></a>
</p>
