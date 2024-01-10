---
title: Send messages to telegram bot via POST
date: 2024-01-10 19:44:16
tags:
  - telegram
  - bot
---

## Introduction

Sending messages to a Telegram bot via POST can be a powerful way to interact with your bot programmatically. In this guide, we'll explore the basics and limitations of using the Telegram Bot API method [sendMessage](https://core.telegram.org/bots/api#sendmessage).

## Basic Knowledge

To send a message using the `sendMessage` method, you'll need to include two required fields:

- `chat_id`: Identifies the chat to which the message will be sent. You can find it by following [this StackOverflow post](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id]).
- `text`: Represents the content of the message, and it must be an arbitrary string 1-4096 characters long.

To get started, create a new bot using [BotFather](https://t.me/botfather) and obtain a bot token like `012345678:someletters`.

The URL to send messages will include your bot token:

```
https://api.telegram.org/bot012345678:someletters/sendMessage'
```

## Making requests

You can experiment with sending messages using cURL or Python. Here's an example using cURL:

```shell
curl https://api.telegram.org/bot012345678:someletters/sendMessage \
  --request POST \
  --header "Content-Type: application/json" \
  -d '{"chat_id": 1234567, "text": "hello, world!"}'
```

For a simpler approach, you can use [httpie](https://httpie.io/docs/cli/optional-get-and-post):

```shell
http https://api.telegram.org/bot012345678:someletters/sendMessage chat_id=192475189 text='hello, world!'
```

If you prefer Python, here's an example using the `requests` library:

```python
import requests

token = "012345678:someletters"
url = f'https://api.telegram.org/bot{token}/sendMessage'
data = {"chat_id": 1234567, "text": "hello, world!"}

requests.post(url, json=data)
```

## Limitation

Keep in mind that the text parameter has a maximum limit of 4096 bytes:

```python
response = requests.post(url, json=data | {"text": "a" * 4096})

response.status_code
# 200
```

Exceeding this limit may result in a 400 status code. Here's an example:

```python
response = requests.post(url, json=data | {"text": "a" * 4097})

response.status_code
# 400
```
