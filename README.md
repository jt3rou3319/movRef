# movRef

動画リファレンス管理ブラウザアプリ。OneDrive連携でどこからでもアクセス可能。

**デプロイ先**: `https://<your-github-username>.github.io/movref/`

---

## ファイル構成

```
movref/
├── index.html     # アプリ本体
├── callback.html  # OneDrive認証コールバック専用（必須）
└── README.md      # このファイル
```

> ⚠️ `index.html` と `callback.html` の **両方** をGitHubにアップロードしてください。

---

## セットアップ手順

### Step 1 — GitHubリポジトリを作成する

1. [github.com/new](https://github.com/new) を開く
2. Repository name: `movref`
3. **Public** を選択 → **Create repository**
4. `index.html`・`callback.html`・`README.md` の3ファイルをアップロード

```bash
# CLIでやる場合
git init
git add index.html callback.html README.md
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/<username>/movref.git
git push -u origin main
```

### Step 2 — GitHub Pages を有効にする

1. リポジトリの **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)` → **Save**
4. 数分後に `https://<username>.github.io/movref/` で公開される

---

### Step 3 — Azure App Registration（OneDrive連携用）

#### 3-1. Azure ポータルにアクセス

1. [portal.azure.com](https://portal.azure.com) を開く
2. Microsoftアカウント（OneDriveと同じアカウント）でサインイン

#### 3-2. アプリを登録する

1. 検索バーで「**アプリの登録**」を検索 → クリック
2. **「+ 新規登録」** をクリック
3. 以下を入力:

| 項目 | 値 |
|------|-----|
| 名前 | `movRef`（任意） |
| サポートされているアカウントの種類 | **「任意の組織のディレクトリ内のアカウントと個人のMicrosoftアカウント」** |
| リダイレクト URI（プラットフォーム） | **SPA（シングルページアプリケーション）** |
| リダイレクト URI（URL） | `https://<username>.github.io/movref/callback.html` |

4. **「登録」** をクリック

> ⚠️ プラットフォームは必ず **SPA** を選択してください（WebではなくSPA）。  
> リダイレクトURIは `callback.html` のURLです（`index.html` ではありません）。

#### 3-3. マニフェストを編集する（重要）

1. 左メニュー **「マニフェスト」** をクリック
2. JSON内の以下を変更:

```json
"requestedAccessTokenVersion": 2,
```

3. **「保存」**

> この設定をしないとサインイン時にエラーになります。

#### 3-4. Client ID をコピーする

登録後の概要画面で **「アプリケーション (クライアント) ID」** をコピーする。

例: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

#### 3-5. APIのアクセス許可を確認する

1. 左メニュー **「APIのアクセス許可」**
2. 以下が含まれていることを確認:
   - `Microsoft Graph` → `Files.ReadWrite.AppFolder`
   - `Microsoft Graph` → `Files.Read`
   - `Microsoft Graph` → `User.Read`
   - `Microsoft Graph` → `offline_access`
3. なければ **「アクセス許可の追加」** → Microsoft Graph → 委任されたアクセス許可 → 上記を追加

---

### Step 4 — アプリにClient IDを設定してサインインする

1. `https://<username>.github.io/movref/` を開く
2. トップバーの **「OneDrive」** ボタンをクリック
3. ダイアログが表示されたら **Azure Client ID** を貼り付けて Enter
4. Microsoftのサインインウィンドウが開く（ポップアップ許可が必要）
5. サインイン完了後、ウィンドウが自動で閉じる
6. ✅「OneDriveに接続しました」と表示されれば完了

> Client IDは `localStorage` に保存されるため、次回以降は自動でサインインします。  
> ポップアップがブロックされた場合はブラウザのアドレスバー右端の許可ボタンを押してください。

---

### Step 5 — 動画フォルダを読み込む

1. サインイン後、トップバーに **「📁 フォルダ読込」** ボタンが表示される
2. クリックするとOneDriveのルートフォルダ一覧が表示される
3. 番号または名前を入力して読み込むフォルダを選択
4. 動画のメタデータ（ファイル名・日付）が順次読み込まれる
5. カードをホバーしたときに初めて動画プレビューが読み込まれる（遅延ロード）

---

## 使い方

### movビュー（動画一覧）

- **カードホバー** → プレビュー再生（初回ホバー時に動画をロード）
- **コンテ表示** → 9コマ（3×3）サムネイル表示
- **サイズスライダー** → 1〜6列のグリッド表示切替
- **ソート** → 名前順 / 実施時期順 / お気に入り優先 / ランダム
- **タグフィルタ** → サイドバーのタグをクリックで絞り込み（再クリックで解除）

### モーダル（動画詳細）

- タイムラインでシーク、0.5×/1×/2× 倍速切替
- **[ IN / OUT ]** をマークして **＋Ref** でRefクリップ登録
- クライアント名・実施時期・タグ・メモを編集して保存

### Refビュー

- 登録したRefクリップをIN〜OUTでループ再生
- サムネイルを**右クリック** → **MP4としてダウンロード**（映像＋音声）
- ソート機能あり（名前順 / 実施時期順 / お気に入り優先 / ランダム）

### データの同期（OneDrive）

- タグ・メモ・Refクリップ情報は **OneDriveの隠し領域**（appDataFolder）に `movref_data.json` として自動保存
- OneDriveエクスプローラーには表示されない非公開領域
- 別のPCでも同じMicrosoftアカウントでサインインすればデータが復元される
- 動画のダウンロードURL（`@microsoft.graph.downloadUrl`）は数時間で期限切れになるため、起動時は自動クリアされ、ホバー/モーダル開封時に自動再取得される

---

## ローカルでテストする（オプション）

```bash
# Python 3
python3 -m http.server 8080
# → http://localhost:8080/index.html を開く
```

Azure App Registration のリダイレクトURIに `http://localhost:8080/callback.html` を追加してください。

---

## トラブルシューティング

| エラー | 原因 | 対処 |
|--------|------|------|
| `unauthorized_client` | アカウントの種類の設定ミス | 「任意の組織 + 個人」に変更 |
| `msal is not defined` | 古いバージョンのファイルが残っている | index.html を再アップロード |
| ポップアップが閉じる | ブラウザのポップアップブロック | ブラウザで許可設定 |
| 動画が再生されない | ダウンロードURLの期限切れ | 「📁 フォルダ読込」を再実行 |
| 195本しか読み込まれない | 古いバージョンのファイル | index.html v10以降を使用 |
