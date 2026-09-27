# digiman-sf-onboarding-codex

株式会社DigiMan が配布する、**OpenAI Codex 向け** Salesforce CLI (`sf`) オンボーディング・フォルダです。

受け取ったフォルダを Codex の作業フォルダとして開くと、エージェント自身が
`sf` の導入状況を確認し、次に何を依頼すればよいかを案内します。

## ダウンロード

OS ごとに分かれています。[Releases](../../releases/latest) から、お使いの OS の zip を取得してください。

| OS | ファイル |
|---|---|
| macOS | `digiman-sf-onboarding-codex-mac.zip` |
| Windows | `digiman-sf-onboarding-codex-windows.zip` |

解凍したフォルダの `README.md` に、セットアップ手順が書いてあります。

## OS で何が違うのか

振る舞いの仕様（自己紹介する、依頼されるまでインストールしない、など）は同じです。
違うのは次の 3 点だけです。

| | macOS | Windows |
|---|---|---|
| 導入確認 | `command -v sf` | `Get-Command sf -ErrorAction SilentlyContinue` |
| `sf` の入れ方 | npm（Node.js が必要） | `.exe` インストーラ（Node.js 不要） |
| 固有の注意 | PATH を手動で通す場合がある | PowerShell の実行ポリシー |

Windows は **WSL2 不要**で、PowerShell のまま動きます。

## 構成

```
mac/        macOS 版（zsh 前提）
windows/    Windows 版（PowerShell 前提）
```

各フォルダの中身は共通で、次の 4 ファイルです。

```
AGENTS.md                              エージェントの正の指示
README.md                              人間向けのセットアップ手順
.codex/config.toml                     このフォルダでの推奨設定
.agents/skills/salesforce-cli-install/
  └─ SKILL.md                          インストールを依頼されたときの手順
```

`AGENTS.md` は [agents.md](https://agents.md) のオープン規格に沿っています。
Codex のほか、Cursor や GitHub Copilot など他のエージェントでも読み込まれます。

## お問い合わせ

業務に合わせたフォルダをご希望の場合は [DigiMan](https://digi-man.co.jp/contact/) までご相談ください。
