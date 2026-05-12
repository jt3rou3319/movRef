# movRef

動画リファレンス管理ブラウザアプリ。OneDrive連携でどこからでもアクセス可能。

**デプロイ先**: `https://<your-github-username>.github.io/movref/`

---

## セットアップ手順

### Step 1 — GitHubリポジトリを作成する

1. [github.com/new](https://github.com/new) を開く
2. Repository name: `movref`
3. **Public** を選択 → **Create repository**
4. `index.html`（このアプリのHTMLファイル）をアップロード

```bash
# ローカルでやる場合
git init
git add index.html README.md
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
| 名前 | `movRef` （任意） |
| サポートされているアカウントの種類 | **個人の Microsoft アカウントのみ** |
| リダイレクト URI（プラットフォーム） | **シングルページアプリケーション (SPA)** |
| リダイレクト URI（URL） | `https://<username>.github.io/movref/` |

4. **「登録」** をクリック

> ⚠️ ローカルでテストしたい場合は `http://localhost:8080` もリダイレクトURIに追加できます（後から追加可能）。

#### 3-3. Client ID をコピーする

登録後の画面で **「アプリケーション (クライアント) ID」** をコピーする。

例: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

#### 3-4. APIのアクセス許可を確認する

1. 左メニュー **「APIのアクセス許可」**
2. 以下が含まれていればOK（自動で追加されることが多い）:
   - `Microsoft Graph` → `Files.ReadWrite.AppFolder`
   - `Microsoft Graph` → `Files.Read`
   - `Microsoft Graph` → `User.Read`
3. なければ **「アクセス許可の追加」** → Microsoft Graph → 委任されたアクセス許可 → 上記を追加

---

### Step 4 — アプリにClient IDを設定する

1. `https://<username>.github.io/movref/` を開く
2. トップバーの **「OneDrive」** ボタンをクリック
3. ダイアログが表示されたら **Azure Client ID** を貼り付けて Enter
4. Microsoftのサインインポップアップが開くのでサインイン
5. ✅「OneDriveに接続しました」と表示されれば完了

> Client IDは`localStorage`に保存されるため、次回以降は自動でサインインを試みます。

---

## 使い方

### movビュー
- ローカルファイルをドラッグ or モーダルから読み込み
- OneDrive連携後は **「📁 フォルダ読込」** でフォルダ内動画を一括取得
- カードホバーでプレビュー再生
- **コンテ表示**ボタンで9コマ（3×3）サムネイル表示

### Refクリップ
- 動画モーダルで **[ IN** / **OUT ]** をマーク → **＋Ref** で登録
- Refビューでループ再生確認
- サムネイルを**右クリック** → MP4でダウンロード（映像＋音声）

### データの同期
- タグ・メモ・Refクリップ情報は **OneDriveの隠し領域**（appDataFolder）に自動保存
- 保存ファイル: `movref_data.json`（ユーザーのOneDriveエクスプローラーには表示されない）
- 別のPCでも同じMicrosoftアカウントでサインインすればデータが復元される

---

## ローカルでテストする（オプション）

GitHub Pagesにデプロイせず手元で試す場合:

```bash
# Python 3
python3 -m http.server 8080
# → http://localhost:8080 を開く
```

Azure App RegistrationにリダイレクトURIとして `http://localhost:8080` を追加してください。

---

## ファイル構成

```
movref/
├── index.html   # アプリ本体（単一ファイル）
└── README.md    # このファイル
```
