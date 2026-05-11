# Discord App Setup

Discord app と token は Discord Developer Portal で一度作ります。別 MacBook では同じ token を使い回せます。

公式ドキュメント:

- [OAuth2 and Permissions](https://docs.discord.com/developers/platform/oauth2-and-permissions)
- [Where can I find my User/Server/Message ID?](https://support.discord.com/hc/en-us/articles/206346498)

## Bot 設定

Developer Portal で Bot を作り、以下を有効にします。

- Message Content Intent

Bot token を `DISCORD_BOT_TOKEN` として使います。

## Discord App / Bot を作る

1. [Discord Developer Portal](https://discord.com/developers/applications) を開きます。
2. 既存 Application を使う場合は対象を開きます。新規なら `New Application` を押します。
3. 左メニューの `Bot` を開きます。
4. `Reset Token` または `View Token` から token をコピーします。
5. `Privileged Gateway Intents` の `Message Content Intent` を ON にして保存します。
6. ワンライナーの `Discord Bot Token:` に token を貼ります。

token は秘密情報です。GitHub、Slack、Discord、チャットには貼らないでください。

## Invite URL

`CLIENT_ID` を Discord application / bot の client ID に置き換えます。

```text
https://discord.com/oauth2/authorize?client_id=CLIENT_ID&permissions=117824&integration_type=0&scope=bot
```

`117824` は以下の最小寄り権限です。

- View Channels
- Send Messages
- Read Message History
- Embed Links
- Attach Files
- Add Reactions

Bot を server に追加済みなら、この手順は不要です。

## Private Channel

private channel を作り、見せたいユーザーと Bot だけを追加します。

Hermes 側ではさらに `DISCORD_ALLOWED_CHANNELS` と `DISCORD_ALLOWED_USERS` で応答範囲を絞ります。

## `DISCORD_HOME_CHANNEL` / `DISCORD_ALLOWED_CHANNELS` を取得する

1. Discord Desktop App で `User Settings` を開きます。
2. `Advanced` を開きます。
3. `Developer Mode` を ON にします。
4. Bot を置く channel を右クリックします。
5. `Copy Channel ID` を押します。
6. コピーされた数字だけの長い ID を入力します。

複数 channel で許可したい場合は、`123...,456...` のようにカンマ区切りで入力します。

## `DISCORD_ALLOWED_USERS` を取得する

1. `Developer Mode` が ON になっていることを確認します。
2. 許可したいユーザーを右クリックします。
3. `Copy User ID` を押します。
4. コピーされた数字だけの長い ID を入力します。

複数ユーザーに許可したい場合は、`123...,456...` のようにカンマ区切りで入力します。

## 入力値の対応表

| bootstrap の入力 | Discord で取得する場所 | 形式 |
| --- | --- | --- |
| `Discord Bot Token` | Developer Portal -> Application -> `Bot` -> token | 長い token 文字列 |
| `Discord home channel ID` | Discord App -> channel 右クリック -> `Copy Channel ID` | 数字だけの長い ID |
| `Discord allowed channel IDs` | 許可したい channel ID | `123...,456...` |
| `Discord allowed user IDs` | user 右クリック -> `Copy User ID` | `123...,456...` |

## 注意

- token を `Reset Token` すると古い token は無効になります。その場合は各 MacBook で再入力してください。
- private channel で使う場合、Discord 側でも Bot と許可ユーザーだけが見えるように channel 権限を設定してください。
- `Message Content Intent` が OFF のままだと、Bot がメッセージ本文を読めず応答できないことがあります。
