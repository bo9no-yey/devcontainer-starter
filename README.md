# devcontainer-starter

Dev Container と Claude Code で始めるプロジェクトのひな形です。

## 中身

| 項目 | 内容 |
| --- | --- |
| ベースイメージ | `mcr.microsoft.com/devcontainers/base:ubuntu26.04`（[Dockerfile](.devcontainer/Dockerfile) の `FROM` で指定） |
| ユーザー | `vscode`（非 root。`--dangerously-skip-permissions` を使えるようにするため） |
| features | Node.js (LTS)、Claude Code、GitHub CLI (`gh`) |
| Claude Code の設定 | `~/.claude` をプロジェクトごとの名前付きボリュームに保存。再ビルドしてもログイン状態が残る |

必要なもの: Docker（Docker Desktop や OrbStack など）と、VS Code の Dev Containers 拡張機能。

---

## A. 新しいリポジトリをこのテンプレートから始める

### 1. リポジトリを作る

**ブラウザで作る場合**

1. このリポジトリのページで **Use this template → Create a new repository** を押す
2. 名前と公開範囲を決めて作る

**`gh` で作る場合**

```bash
gh repo create <新しいリポジトリ名> --private --template bo9no-yey/devcontainer-starter --clone
```

できあがるリポジトリには、このテンプレートのファイルだけが入ります。テンプレートのコミット履歴は入りません。

### 2. コンテナで開く

次のどちらかで開きます。

- **手元にクローンする場合**: クローンしたフォルダを VS Code で開き、**Dev Containers: Reopen in Container** を実行する
- **ボリュームにクローンする場合**: VS Code で **Dev Containers: Clone Repository in Container Volume** を実行し、リポジトリの URL を指定する

### 3. 最初にやること

1. `.devcontainer/devcontainer.json` の `"name"` をプロジェクト名に書き換え、**Dev Containers: Rebuild Container** を実行する
2. ターミナルで `claude` を実行してログインする（プロジェクトごとに最初の1回だけ必要）
3. この README.md をプロジェクト用の内容に書き換える
4. コミットして push する

---

## B. 既存のリポジトリにこのテンプレートを入れる

**Use this template** は新しいリポジトリを作る機能なので、既存のリポジトリには使えません。代わりに、git でテンプレートの `.devcontainer/` だけを取り込みます。

### 1. 作業する場所を決める

- **手元にクローンしてある場合**: Mac などのホスト側のターミナルで、リポジトリのフォルダに移動する
- **ボリュームにクローンしてある場合**: VS Code でそのコンテナを開き、VS Code のターミナルで作業する

### 2. いまある `.devcontainer/` を確認する

```bash
git status --short
ls -a .devcontainer 2>/dev/null
```

- 何も出ない場合は、そのまま次へ進む
- `?? .devcontainer/` と出る場合は、VS Code がボリュームにクローンしたときに作った仮の設定なので、`rm -rf .devcontainer` で消す
- 自分で作った `.devcontainer/` がすでにコミットされている場合は、次の手順で上書きされる。残したい設定がないか先に確認する

### 3. テンプレートから `.devcontainer/` を取り込む

```bash
git remote add template https://github.com/bo9no-yey/devcontainer-starter.git
git fetch template
git checkout template/main -- .devcontainer
```

これで、テンプレートのファイルが作業ツリーにコピーされ、ステージにも入ります。

> **`git merge template/main` は使わない**
> 既存のリポジトリとテンプレートは別々の履歴なので、マージすると `refusing to merge unrelated histories` で止まります。無理に通すと、テンプレートの履歴が混ざります。ファイルだけを持ってくるには、上の `checkout ... -- .devcontainer` を使います。

### 4. 名前を変えて再ビルドする

1. `.devcontainer/devcontainer.json` の `"name"` をプロジェクト名に書き換える
2. コンテナで開く
   - 手元にクローンしてある場合は **Dev Containers: Reopen in Container**
   - ボリュームにクローンしてある場合は **Dev Containers: Rebuild Container**

### 5. 確かめてコミットする

```bash
gh --version && claude --version   # 両方動けば OK
claude                              # 初回ログイン
git add .devcontainer
git commit -m "Add dev container from devcontainer-starter"
git push
```

---

## テンプレートを直したあと、ほかのプロジェクトに反映する

テンプレートを直しても、そこから作ったリポジトリや取り込んだリポジトリには自動では反映されません。反映したいプロジェクトで、次を実行します。

```bash
# A の方法で作ったリポジトリには template リモートがないので、最初に1回だけ登録する
git remote add template https://github.com/bo9no-yey/devcontainer-starter.git

git fetch template
git checkout template/main -- .devcontainer
git diff --cached                   # 変更内容を確認する
```

`"name"` などプロジェクト側で書き換えた部分も、テンプレートの内容で上書きされます。残したい変更は書き戻してから、再ビルドしてコミットしてください。

---

## このテンプレート自体をメンテナンスする

- **Ubuntu のバージョンを上げる**: [Dockerfile](.devcontainer/Dockerfile) の `FROM` 行を書き換えて再ビルドする
- **features を足す・最新にする**: [devcontainer.json](.devcontainer/devcontainer.json) の `features` を編集して再ビルドする。最新版に上げたいだけのときは、`devcontainer-lock.json` を消して再ビルドすると作り直される。どちらの場合も、更新された lock ファイルを一緒にコミットする
- **`~/.claude` のマウント先**: Dockerfile で `/home/vscode/.claude` を先に作り、所有者を `vscode` にしている。これがないと、名前付きボリュームが root の所有で作られ、Claude Code が設定を書き込めない。消さないこと
