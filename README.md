# Hermes Codex Bridge Bootstrap

macOS で [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) を入れ、Slack と Discord からローカル Mac の Codex に話しかけられる gateway を作るための最小ブートストラップです。

このリポジトリは小さく保ちます。Hermes 本体のインストールは公式インストーラへ委譲し、この repo は以下だけを担当します。

- Hermes のインストール確認
- OpenAI Codex provider のログイン確認
- Slack / Discord token と許可 channel / user の設定
- 任意で Computer Use 用の `cua-driver` インストールと確認
- `~/.hermes/.env` と `~/.hermes/config.yaml` の更新
- macOS `launchd` gateway の install / restart

## ワンライナー

```sh
/bin/zsh -c 'set -e; repo="${HERMES_BRIDGE_DIR:-$HOME/.local/share/hermes-codex-bridge-bootstrap}"; if [ -d "$repo/.git" ]; then git -C "$repo" pull --ff-only; else git clone --depth 1 https://github.com/rrih/hermes-codex-bridge-bootstrap.git "$repo"; fi; "$repo/bin/bootstrap"'
```

対話入力なしで流す場合は、必要な値を環境変数で渡します。

```sh
SLACK_BOT_TOKEN='xoxb-...' \
SLACK_APP_TOKEN='xapp-...' \
SLACK_HOME_CHANNEL='C...' \
SLACK_ALLOWED_USERS='U...' \
DISCORD_BOT_TOKEN='...' \
DISCORD_HOME_CHANNEL='123456789012345678' \
DISCORD_ALLOWED_USERS='123456789012345678' \
ENABLE_COMPUTER_USE='yes' \
/bin/zsh -c 'set -e; repo="${HERMES_BRIDGE_DIR:-$HOME/.local/share/hermes-codex-bridge-bootstrap}"; if [ -d "$repo/.git" ]; then git -C "$repo" pull --ff-only; else git clone --depth 1 https://github.com/rrih/hermes-codex-bridge-bootstrap.git "$repo"; fi; "$repo/bin/bootstrap"'
```

## 入力するもの

ワンライナー実行中に聞かれる値は、各サービスの管理画面で作った Bot の token と、Bot に反応させたい channel / user の ID です。token は GitHub、Slack、Discord、チャットには貼らず、その Mac の入力プロンプトか環境変数だけで渡してください。

### Slack

- Bot User OAuth Token: `xoxb-...`
- App-Level Token: `xapp-...`
- Bot を常駐させる channel ID: `C...` または private channel の ID
- Bot と話してよい Slack user ID: `U...`

詳しい取得手順: [Slack app setup](docs/slack-app.md)

### Discord

- Discord Bot Token
- Bot を常駐させる channel ID: 数字だけの長い ID
- Bot と話してよい Discord user ID: 数字だけの長い ID

詳しい取得手順: [Discord app setup](docs/discord-app.md)

channel / user はカンマ区切りで複数指定できます。
既存の同じ Bot を別 MacBook でも使う場合は、token と ID は同じものを入力できます。

### Computer Use

スマホから Discord 経由でローカル Mac を操作したい場合は、bootstrap の質問 `Install/repair Computer Use for local Mac control?` に `yes` と答えます。非対話実行では `ENABLE_COMPUTER_USE=yes` を渡します。

この設定では Hermes の `computer_use` toolset と `cua-driver` を使います。導入後、macOS の許可が必要です。

```sh
open -n -g -a CuaDriver --args serve
cua-driver check_permissions
```

`Accessibility` と `Screen Recording` が未許可なら、System Settings > Privacy & Security で `CuaDriver.app` を許可してください。

## 事前準備

Slack と Discord のアプリ自体は、各サービスの管理画面で token を発行する必要があります。最小手順は以下です。

- [Slack app setup](docs/slack-app.md)
- [Discord app setup](docs/discord-app.md)

## 生成される設定

主な設定は以下です。

- Slack / Discord ともに mention 必須
- Discord は auto thread 無効
- Discord の `@everyone` / role mention は禁止
- channel allowlist を設定
- user allowlist を設定
- `launchd` で Hermes gateway を常駐
- model は既定で `openai-codex` / `gpt-5.5`
- `ENABLE_COMPUTER_USE=yes` の場合は `cua-driver` を導入し、既存の明示的な platform toolset 設定に `computer_use` を追加

## 確認

```sh
~/.local/bin/hermes status
~/.local/bin/hermes logs --since 15m
tail -f ~/.hermes/logs/gateway.log
```

Slack / Discord の対象チャンネルで bot に mention して `ping` を送ると、gateway ログに `inbound message` と `Sending response` が出ます。

Computer Use を入れた場合、まずは読み取りだけを試してください。

```text
@bot Computer UseでMacのアプリ一覧を確認して。クリックや入力はしないで。
```

次に、画面取得だけを試します。

```text
@bot Computer UseでSafariをcaptureして、画面に見えている要素だけ説明して。クリックや入力はしないで。
```

クリック、入力、送信、削除、購入、設定変更などはローカルPCへの実操作です。Discord は private channel にして、`DISCORD_ALLOWED_USERS` と `DISCORD_ALLOWED_CHANNELS` を必ず絞ってください。

## Dry Run

設定ファイル生成だけを確認したい場合:

```sh
HERMES_BRIDGE_DRY_RUN=1 HERMES_HOME="$(mktemp -d)" VERIFY_TOKENS=0 ./bin/bootstrap
```
