# opsgenie-to-telegram

A Dockerized service that forwards [OpsGenie](https://www.atlassian.com/software/opsgenie) alert notifications to a [Telegram](https://telegram.org/) chat or channel via a webhook integration.

## How It Works

1. OpsGenie sends alert events (created, acknowledged, closed, etc.) to this service via a webhook.
2. The service receives the payload and forwards a formatted message to the configured Telegram chat.

## Prerequisites

- Docker
- A Telegram bot token (create one via [@BotFather](https://t.me/BotFather))
- A Telegram chat ID (group, channel, or user)
- OpsGenie account with webhook integration access

## Setup

### 1. Create a Telegram Bot

1. Open Telegram and start a chat with [@BotFather](https://t.me/BotFather).
2. Send `/newbot` and follow the prompts.
3. Save the bot token you receive.
4. Add the bot to the target chat/channel and get the chat ID.

### 2. Run the Service

```bash
docker build -t opsgenie-to-telegram .

docker run -d \
  --name opsgenie-to-telegram \
  -p 8080:8080 \
  -e TELEGRAM_BOT_TOKEN=<your-bot-token> \
  -e TELEGRAM_CHAT_ID=<your-chat-id> \
  opsgenie-to-telegram
```

### 3. Configure OpsGenie Webhook

1. In OpsGenie, go to **Settings → Integrations → Add Integration**.
2. Select **Webhook**.
3. Set the webhook URL to `http://<your-host>:8080/webhook`.
4. Enable the alert actions you want to forward (e.g., Create, Acknowledge, Close).
5. Save the integration.

## Docker Image

This repository builds on top of the base image:

```
kh0ma/opsgenie-to-telegram:1.7.1-main
```

To use a newer version, update the `FROM` line in the `Dockerfile`:

```dockerfile
FROM kh0ma/opsgenie-to-telegram:<version>
```

Available tags can be found on [Docker Hub](https://hub.docker.com/r/kh0ma/opsgenie-to-telegram/tags).

## Docker Compose Example

```yaml
services:
  opsgenie-to-telegram:
    build: .
    ports:
      - "8080:8080"
    environment:
      TELEGRAM_BOT_TOKEN: ${TELEGRAM_BOT_TOKEN}
      TELEGRAM_CHAT_ID: ${TELEGRAM_CHAT_ID}
    restart: unless-stopped
```

Run with:

```bash
docker compose up -d
```

## Environment Variables

| Variable             | Description                              | Required |
|----------------------|------------------------------------------|----------|
| `TELEGRAM_BOT_TOKEN` | Telegram bot API token from BotFather    | Yes      |
| `TELEGRAM_CHAT_ID`   | Target Telegram chat or channel ID       | Yes      |

## License

See [LICENSE](LICENSE) for details.
