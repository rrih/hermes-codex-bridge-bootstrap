# Slack App Setup

Slack app と token は Slack 側で一度作ります。別 MacBook では同じ token を使い回せます。

## 必要な token

- `SLACK_BOT_TOKEN`: Bot User OAuth Token, `xoxb-...`
- `SLACK_APP_TOKEN`: App-Level Token, `xapp-...`, scope は `connections:write`

## 推奨 Bot scopes

最小構成:

- `app_mentions:read`
- `channels:history`
- `channels:read`
- `chat:write`
- `groups:history`
- `groups:read`
- `im:history`
- `im:read`
- `im:write`

添付ファイルも扱うなら追加:

- `files:read`
- `files:write`

## Event Subscriptions

Socket Mode を有効にし、bot events に以下を入れます。

- `app_mention`
- `message.channels`
- `message.groups`
- `message.im`

## Channel

対象 channel に Slack app を invite します。

```text
/invite @your-bot-name
```

Hermes 側では `SLACK_ALLOWED_CHANNELS` と `SLACK_ALLOWED_USERS` で応答範囲を絞ります。

