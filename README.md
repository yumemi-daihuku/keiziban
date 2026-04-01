# クラス掲示板 セットアップガイド

## ファイル構成

```
anon-board/
├── index.html          # 利用規約 & 同意ページ（入口）
├── board.html          # スレッド一覧
├── thread.html         # スレッド詳細・返信・リアクション
├── firestore.rules     # Firestoreセキュリティルール
├── storage.rules       # Storageセキュリティルール
└── firebase-config.js  # 設定ファイル（参考用）
```

---

## セットアップ手順

### 1. Firebaseプロジェクトを作成

1. [Firebase Console](https://console.firebase.google.com/) にアクセス
2. 「プロジェクトを追加」→ プロジェクト名を入力（例: `class-board-2026`）
3. Google アナリティクスは任意（OFFでOK）

---

### 2. Firestoreを有効化

1. 左メニュー「Firestore Database」→「データベースを作成」
2. **本番環境モード** で開始（後でルールを上書きする）
3. ロケーション: `asia-northeast1`（東京）推奨

---

### 3. Firebase Storage を有効化

1. 左メニュー「Storage」→「開始する」
2. 本番モードで開始
3. ロケーション: Firestoreと同じ `asia-northeast1`

---

### 4. ウェブアプリを登録してConfig取得

1. プロジェクト設定（⚙️）→「マイアプリ」→「ウェブ」（`</>`アイコン）
2. アプリ名を入力（例: `class-board-web`）→ 登録
3. 表示された `firebaseConfig` をコピー

---

### 5. HTMLファイルに設定を貼り付け

`board.html` と `thread.html` の2箇所に `FIREBASE_CONFIG` を記述する箇所があります。
**両方に** 同じ設定を貼り付けてください。

```javascript
const FIREBASE_CONFIG = {
  apiKey:            "AIza...",          // ← ここを書き換え
  authDomain:        "xxx.firebaseapp.com",
  projectId:         "xxx",
  storageBucket:     "xxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123...:web:abc..."
};
```

**管理者パスワードも変更してください（2ファイル共通）:**

```javascript
const ADMIN_PASSWORD = "your_admin_password_here"; // 好きなパスワードに変更
```

---

### 6. セキュリティルールを設定

#### Firestoreルール
1. Firebase Console → Firestore → 「ルール」タブ
2. `firestore.rules` の内容を貼り付けて「公開」

#### Storageルール
1. Firebase Console → Storage → 「ルール」タブ
2. `storage.rules` の内容を貼り付けて「公開」

---

### 7. CORS設定（Storageの画像が表示されない場合）

Firebase CLI で以下を実行:

```bash
npm install -g firebase-tools
firebase login
firebase init storage
```

`cors.json` を作成:
```json
[
  {
    "origin": ["*"],
    "method": ["GET"],
    "maxAgeSeconds": 3600
  }
]
```

```bash
gsutil cors set cors.json gs://YOUR_PROJECT.appspot.com
```

---

### 8. デプロイ

**GitHub Pages（無料・簡単）:**
1. GitHubリポジトリを作成
2. 4つのHTMLファイルをpush
3. Settings → Pages → `main` ブランチを選択

**Firebase Hosting:**
```bash
firebase init hosting
# public ディレクトリをHTMLファイルのある場所に設定
firebase deploy
```

---

## 使い方

| URL | 説明 |
|-----|------|
| `index.html` | 利用規約ページ（入口） |
| `board.html` | スレッド一覧 |
| `thread.html?id=THREAD_ID` | スレッド詳細 |
| `board.html?admin=パスワード` | 管理者モード（全投稿削除可能） |

---

## 機能一覧

- ✅ 利用規約同意（セッション単位）
- ✅ 匿名スレッド作成・一覧表示
- ✅ 匿名返信（リアルタイム更新）
- ✅ 絵文字リアクション（スレッド・返信両対応）
- ✅ 画像投稿（JPG/PNG/GIF・5MB以下）
- ✅ 自分の投稿の削除（localStorage識別）
- ✅ 管理者モード（?admin=パスワード）
- ✅ XSS対策（escapeHtml処理）
- ✅ NGワードフィルター（リスト追加可能）
