# Discord App Setup

Discord app と token は Discord Developer Portal で一度作ります。別 MacBook では同じ token を使い回せます。

## Bot 設定

Developer Portal で Bot を作り、以下を有効にします。

- Message Content Intent

Bot token を `DISCORD_BOT_TOKEN` として使います。

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

## Private Channel

private channel を作り、見せたいユーザーと Bot だけを追加します。

Hermes 側ではさらに `DISCORD_ALLOWED_CHANNELS` と `DISCORD_ALLOWED_USERS` で応答範囲を絞ります。

