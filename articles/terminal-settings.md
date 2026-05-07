---
title: "MacデフォルトのZSHをかっこよくしてみた！"
emoji: "🤓"
type: "tech"
topics: ["tech", "terminal"]
published: True
---
Mac標準の `zsh` を、**Oh My Zsh + Powerlevel10k** で見た目も使い勝手も良くする手順メモです。

## Before / After

- **Before**: macOS標準のターミナル（シンプル）
- **After**: アイコン・Gitブランチ・実行時間などが表示されるプロンプト

（ここにスクショを貼ると分かりやすいです）

## 前提

- macOS（標準シェルが `zsh`）
- 既に `zsh` を使っている（`echo $SHELL` が `/bin/zsh` ならOK）

## 1. Oh My Zsh をインストール

ターミナルを開いて、Oh My Zsh をインストールします。

- 公式: `https://ohmyz.sh/`

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 2. Powerlevel10k を入れる

Oh My Zsh のカスタムテーマとして Powerlevel10k をクローンします。

- 公式: `https://github.com/romkatv/powerlevel10k`

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

## 3. `.zshrc` でテーマを切り替える

`.zshrc` を開きます。

```bash
vi ~/.zshrc
```

`ZSH_THEME="..."` を探して、次のように変更します（元の行は `#` でコメントアウトしてOK）。

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

編集後に反映します。

```bash
source ~/.zshrc
```

## 4. フォント（重要）

Powerlevel10k は特殊文字（アイコン）を使うので、フォントが合っていないと文字化けします。

Powerlevel10k が推奨する **MesloLGS NF** を入れて、ターミナル側のフォント設定を変更してください。

- フォント: `https://github.com/romkatv/powerlevel10k#manual-font-installation`

（iTerm2 を使っている場合は、`Preferences > Profiles > Text > Font` で設定できます）

## 5. 初期設定ウィザード（p10k configure）

反映後に、見た目を対話形式でセットアップできます。

```bash
p10k configure
```

## よくあるつまずき

- **反映されない**: `source ~/.zshrc` を実行する（`.zshrc` の場所を間違えない）
- **文字化けする**: フォントが未設定。MesloLGS NF を入れてターミナルのフォントを変更する
- **`p10k configure` が見つからない**: テーマ切り替え（`ZSH_THEME=...`）が反映できていない可能性が高い

## おわりに

これだけで、macOS標準の `zsh` がかなり見やすく・気分よくなります。次はプラグイン（例: `zsh-autosuggestions` / `zsh-syntax-highlighting`）を入れると、さらに快適になります。