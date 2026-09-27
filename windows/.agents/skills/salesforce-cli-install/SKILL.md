---
name: salesforce-cli-install
description: Salesforce CLI (sf) を Windows 環境にインストールする。ユーザーから「Salesforce CLI をインストールして」「sf を入れて」「インストールしておいて」など、インストールの意思が明らかな依頼を受けたときにだけ使う。導入状態の確認だけや、sf の使い方の質問には使わない。
---

# Salesforce CLI をインストールする（Windows）

ユーザーから明示的にインストールを依頼されたときにだけ、この手順を実行する。
依頼されていない段階でこの手順の内容を説明してはいけない。

コマンドはすべて PowerShell の構文で書く。

## 実行前に伝えること

作業を始める前に、次の 2 点を 1〜2 文で伝える。

- これからインストールを実行すること
- ネットワーク接続とフォルダ外への書き込みが必要なため、途中で許可を求める確認が出ること
  （環境によっては、一度エラーになってから確認が出ることがある）

## 手順

### 1. 現状を確認する

```powershell
Get-Command sf -ErrorAction SilentlyContinue
```

見つかった場合はインストールを実行せず、導入済みであることを報告して終わる。

### 2. インストール方式を決める

**インストーラ（`.exe`）を第一候補にする。**
Salesforce 公式がインストーラに Node.js を同梱しているため、Node.js を別途入れる必要がない。
また、npm 版で起きる PowerShell 実行ポリシーの問題も回避できる。

まず、アーキテクチャを確認する。

```powershell
$Env:PROCESSOR_ARCHITECTURE
```

`AMD64` なら x64 版、`ARM64` なら ARM64 版を使う。

| アーキテクチャ | ダウンロード URL |
|---|---|
| x64（`AMD64`） | https://developer.salesforce.com/media/salesforce-cli/sf/channels/stable/sf-x64.exe |
| ARM64 | https://developer.salesforce.com/media/salesforce-cli/sf/channels/stable/sf-arm64.exe |

配布ページから選ぶ場合は https://developer.salesforce.com/tools/salesforcecli を案内する。

インストーラは対話形式のウィザードで進む。エージェントが自動で完了させることはできないため、
ダウンロードまでを支援し、その先はユーザーに実行してもらう。
インストーラの「Choose Components」画面に
`Add %LOCALAPPDATA%\sf to Windows Defender exclusions` という項目があるが、
**これは既定でオフのまま**にしてもらう。公式も慎重に扱うよう注記している。

### 3. npm で入れる場合（代替）

管理者権限がない、またはグループポリシーでインストーラが止められる場合は npm を使う。
Salesforce 公式もこの2つを npm 版の採用理由として挙げている。

前提として Node.js の LTS 版が必要。確認する。

```powershell
node --version
```

入っていない場合は、勝手に導入を始めず、ユーザーに確認してから進める。
Windows では `winget` が使える。

```powershell
winget install --id OpenJS.NodeJS.LTS
```

Node.js が用意できたら、次を実行する。

```powershell
npm install @salesforce/cli --global
```

> npm 版では、`sf` を呼んだときに
> `sf.ps1 cannot be loaded because running scripts is disabled on this system` という
> エラーが出ることがある。対処は「実行ポリシーで止まるとき」を参照。

**インストーラ版と npm 版を両方入れてはいけない。**
Salesforce 公式が「パスの問題が起きて診断が難しくなる」と明記している。どちらか 1 つにする。

### 4. PowerShell を開き直してもらう

インストールが終わったら、**PowerShell・コマンドプロンプト・IDE をすべて閉じて開き直す**
必要がある。これをしないと PATH が反映されず、`sf` が見つからない。

Codex もいったん終了して、このフォルダで起動し直してもらう。

### 5. 結果を確認する

```powershell
Get-Command sf -ErrorAction SilentlyContinue
```

見つからない場合は、成功したと報告しない。`sf --version` も叩かない。
まず手順 4 のウィンドウの開き直しが済んでいるかを確認する。

見つかった場合だけ、続けてバージョンを確認する。

```powershell
sf --version
```

## 実行ポリシーで止まるとき

次のようなエラーが出た場合は、PowerShell の実行ポリシーが既定の `Restricted` のままになっている。

```text
sf : File C:\Users\<username>\AppData\Roaming\npm\sf.ps1 cannot be loaded because running scripts is disabled on this system
```

現状を確認する。

```powershell
Get-ExecutionPolicy -List
```

対処はユーザー自身に実行してもらう。勝手に変更しない。
案内するときは、管理者権限が不要な次の形を先に示す。

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

会社のポリシーで変更できない場合は、インストーラ（`.exe`）版に切り替えることを提案する。
インストーラ版ではこの問題が起きない。

## 承認されなかったとき

インストールに必要な許可が下りなかった場合は、失敗として正しく報告する。
ただし、一度サンドボックスやネットワーク制限で失敗しただけの場合は、
まだ最終結果ではない。承認を求める表示が出ていないかを確認してもらう。

そのうえで、次の順で案内する。

1. このフォルダの `.codex\config.toml` の末尾にある 2 行
   （`[permissions.digiman-sf-onboarding.network]` と `enabled = true`）の行頭の `#` を消して保存し、
   Codex を起動し直してもらう。設定は起動時に一度だけ読み込まれる。
2. そのうえで、もう一度送ってもらう依頼文を 1 つ示す。

   ```text
   Salesforce CLI をインストールして
   ```

3. それでも進まない場合は、権限設定を自分で変えず DigiMan 担当に連絡してもらう。
   `/permissions` でも変更できるが、これは Codex 自体が受け取るコマンドでエージェントには届かない。
   案内する場合は、私宛の依頼ではないことと、どれを選ぶべきかは担当に確認することを添える。

## 失敗したときの切り分け

報告は「何が起きたか」「考えられる原因」「次に送る依頼文 1 つ」の順で短くまとめる。

- `... .ps1 cannot be loaded because running scripts is disabled` — 実行ポリシー。上の節へ
- `command not found` / `Get-Command` で見つからない — PowerShell を開き直していない可能性が高い
- `node : 用語 'node' は...認識されません` — Node.js が未導入。インストーラ版への切り替えを提案する
- `PATH not updated, original length XX > 1024` — PATH が長すぎてインストーラが追記できなかった。
  不要なパスを削る必要があるため、ユーザーと DigiMan 担当に判断を委ねる
- ネットワーク関連のエラー — 承認が下りていないか、プロキシ環境の可能性。
  この場合は上の「承認されなかったとき」に進む

原因が特定できないときは、推測で断定せず、エラー出力の該当行をそのまま示す。

どこにインストールされたか分からなくなった場合は、次で確認できる。

```powershell
sf plugins inspect @salesforce/cli
```

出力の `location` が実体の場所。

## やってはいけないこと

- 管理者権限を要するコマンドを、何をするか伝えずに実行する
- 実行ポリシー（`Set-ExecutionPolicy`）を、ユーザーの確認なしに変更する
- 失敗しているのに成功したと報告する
- 依頼されていないのに設定ファイル（`%USERPROFILE%\.codex\config.toml` など）を書き換える
- インストールのついでに、依頼されていない組織認証（`sf org login`）まで進める
- Node.js や他のツールの導入を、確認なしに始める
- インストーラ版と npm 版を両方入れる
- `where` を単独で使う（PowerShell では `Where-Object` の別名）
