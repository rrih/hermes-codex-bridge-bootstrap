# Slack App Setup

Slack app と token は Slack 側で一度作ります。別 MacBook では同じ token を使い回せます。

公式ドキュメント:

- [Socket Mode](https://api.slack.com/apis/connections/socket)
- [`connections:write` scope](https://docs.slack.dev/reference/scopes/connections.write)
- [App manifest reference](https://docs.slack.dev/reference/app-manifest)
- [Locate your Slack URL or ID](https://slack.com/help/articles/221769328-Locate-your-Slack-URL)

## 必要な token

- `SLACK_BOT_TOKEN`: Bot User OAuth Token, `xoxb-...`
- `SLACK_APP_TOKEN`: App-Level Token, `xapp-...`, scope は `connections:write`

## Slack App を作る

1. [Slack API Apps](https://api.slack.com/apps) を開きます。
2. 既存 App を使う場合は対象 App を開きます。新規なら `Create New App` を押します。
3. 新規作成時は `From an app manifest` を選び、この repo の `templates/slack-manifest.json` を貼ると最低限の設定をまとめて入れられます。
4. 対象 workspace を選んで App を作成します。

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

## `SLACK_BOT_TOKEN` を取得する

1. Slack App 管理画面で `OAuth & Permissions` を開きます。
2. `Bot Token Scopes` に必要 scope が入っていることを確認します。
3. `Install to Workspace` または `Reinstall to Workspace` を押します。
4. `Bot User OAuth Token` をコピーします。
5. ワンライナーの `Slack Bot User OAuth Token:` に貼ります。

期待する形式は `xoxb-...` です。

## `SLACK_APP_TOKEN` を取得する

1. Slack App 管理画面で `Basic Information` を開きます。
2. `App-Level Tokens` までスクロールします。
3. `Generate Token and Scopes` を押します。
4. Token name は任意です。
5. Scope に `connections:write` を追加します。
6. 生成された token をコピーします。
7. ワンライナーの `Slack App-Level Token with connections:write:` に貼ります。

期待する形式は `xapp-...` です。

## Channel

対象 channel に Slack app を invite します。

```text
/invite @your-bot-name
```

Hermes 側では `SLACK_ALLOWED_CHANNELS` と `SLACK_ALLOWED_USERS` で応答範囲を絞ります。

## `SLACK_HOME_CHANNEL` / `SLACK_ALLOWED_CHANNELS` を取得する

1. Slack のブラウザ版で対象 channel を開きます。
2. URL を見ます。
3. `https://app.slack.com/client/T.../C...` の末尾の `C...` が channel ID です。
4. private channel でも同じく末尾の ID を使います。

複数 channel で許可したい場合は、`C...,C...` のようにカンマ区切りで入力します。

## `SLACK_ALLOWED_USERS` を取得する

1. Slack で許可したいユーザーのプロフィールを開きます。
2. `その他` / `More` メニューを開きます。
3. `Copy member ID` があればそれを押します。
4. コピーされた `U...` 形式の ID を入力します。

複数ユーザーに許可したい場合は、`U...,U...` のようにカンマ区切りで入力します。

## 入力値の対応表

| bootstrap の入力 | Slack で取得する場所 | 形式 |
| --- | --- | --- |
| `Slack Bot User OAuth Token` | `OAuth & Permissions` -> `Bot User OAuth Token` | `xoxb-...` |
| `Slack App-Level Token with connections:write` | `Basic Information` -> `App-Level Tokens` | `xapp-...` |
| `Slack home channel ID` | ブラウザ版 channel URL の末尾 | `C...` |
| `Slack allowed channel IDs` | 許可したい channel ID | `C...,C...` |
| `Slack allowed user IDs` | Profile -> `Copy member ID` | `U...,U...` |

## 注意

- `SLACK_BOT_TOKEN` と `SLACK_APP_TOKEN` は秘密情報です。GitHub、Slack、Discord、チャットに貼らないでください。
- token を再生成すると、古い token は使えなくなることがあります。その場合は各 MacBook で再入力してください。
- Bot を private channel で使う場合、対象 channel に App を invite してください。
