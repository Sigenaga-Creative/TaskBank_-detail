# TaskBank

タスクをこなすと「お金」が貯まり、貯めたお金で「やりたいこと（Wish）」を叶える——
ゲーム感覚の報酬型ToDoアプリです。

フレームワークやサーバーを使わず、主に**HTML / CSS / JavaScript **で作られています。

※ 現在開発中であり、至らない点もあるかと思います。ご了承ください。

---
<img width="1237" height="632" alt="スクリーンショット 2026-06-27 101141" src="https://github.com/user-attachments/assets/ba606d7a-1983-42ec-872a-a4d14e03a737" />

## ✨ 主な機能

### 🏠 ホーム
- 残高（貯めたお金）の表示
- 「目標」を1つ設定して、達成すると残高を消費
- 「日課」を1つ設定（月1回まで無料で変更、月内に変えると1000円消費する）
- お気に入りタスクをホームに最大4つピン留め

### ✅ タスク
- タスクを3種類のカテゴリで管理
  | カテゴリ | 説明 |
  |---|---|
  | 習慣 (habit) | 何度でも達成OK。達成するたびに報酬 |
  | 1日1回 (daily) | 1日1回だけ達成可能。翌日0時にリセット |
  | タスク (task) | 1回きりで達成したら消える |
- グループ（タグ）で分類・フィルター・検索
- ドラッグ＆ドロップで並び替え（PC・スマホ両対応）

### 🎁 Wish（やりたいことリスト）
- 「目標」（達成したら消える・お金を使う）と「ご褒美」（何度でも達成可能・お金を使う）に分類
- 各項目ごとに「削除するか」「お金を使うか」を個別カスタマイズ可能

### 📊 履歴・統計
- タスク／Wishの達成履歴（取り消しも可能）
- 月別の収入・支出を棒グラフで確認

### ⚙️ 設定
- ダークモード／効果音オン・オフと音量調整
- データの全消去・Googleアカウントのログアウト
- 通知関連の設定

---

## 🛠 使用技術・API

| 技術 / API | 用途 |
|---|---|
| **Vanilla JavaScript (ES6+)** | フレームワーク無しでロジックを実装 |
| **HTML5 / CSS3** | 画面構成・ダークモード対応のスタイリング |
| **Web Storage API (`localStorage`)** | ゲストモードでのデータ永続化 |
| **Google Identity Services (GIS)** | Googleアカウントでのログイン（OAuth 2.0） |
| **Google Drive API v3** | ログインユーザーのデータをクラウド保存（`appDataFolder`を利用し、ユーザーの通常のDrive画面には表示されない隠し領域に保存） |
| **Web Audio (`<audio>`)** | タスク達成時などの効果音再生 |
| **Drag and Drop ** | マウス／タッチ両対応のリストの並び替え |
| **PWA (Progressive Web App)** | ホーム画面に追加してアプリのように使える設計（`manifest.json` + アイコン） |
| **Vercel** | 静的ホスティング・デプロイ先 |

> サーバーを使わず、フロントエンドだけでGoogleログイン＋クラウド保存を実現しているのがこのアプリの特徴です。デプロイ先にはAWS S3 + CloudFrontを採用し、Cloudflareで独自ドメイン（taskbank.dev）を取得しました。バックエンドが無いので、Googleの仕様上「アクセストークンは約1時間で切れる」「リフレッシュトークンは発行されない」という制約があり、長時間アプリを閉じると再ログインが必要になります。今後LambdaなどでAWSバックエンドを構築し、この制約を改善する予定です。

---

## 📱 PWA対応について

スマホのホーム画面に追加すると、ネイティブアプリのような見た目・操作感で使えます。

- `manifest.json` でアプリ名・アイコン・テーマカラーなどを定義
- アプリアイコン（192px / 512px）を用意
- スタンドアロン表示（ブラウザのアドレスバー無し）を想定した設計

実装方法：(iOS 26.5.1での実装)

<img width="200" height="200" alt="TaskBank_アプリQRコード" src="https://github.com/user-attachments/assets/c394050c-6b3e-444a-8aa6-9ae320f52a74" />


1. QRコード、もしくは[URL](https://taskbank.dev)からWeb上にアプリを開きます。
2. その他ボタン(...)をクリックし、「共有」を押します。
3. 少し下にスライドして、ホーム画面に追加を押します。
4. 右上に表示される追加を押すと、ホーム画面にアプリアイコンが表示されます。

---

## 💾 データの持ち方

| モード | 保存先 | 特徴 |
|---|---|---|
| ゲストモード | ブラウザの`localStorage`のみ | 手軽だが端末を変えるとデータは引っ越せない |
| Googleログイン | Google Driveの`appDataFolder` | 複数端末で同じデータを共有できる。アプリ専用の非表示領域なので、Driveの容量表示にも出てこない |

起動するたびにローカルとDriveのデータを比較し、新しい方を自動的に採用する仕組みになっています（端末間でデータがズレないようにするため）。

また、このアプリに必要な容量はKB単位でとても軽量です。
タスク数が100個を超えるヘビーユーザーでもせいぜい100～200KBになると予想しています。

---

## 📁 ファイル構成

```
├── index.html       # 画面のHTML
├── style.css        # スタイル定義
├── app.js           # アプリのロジック全般
├── data.js          # 初期データ・定数
├── icons.css        # アイコン(data-URI埋め込み、CDN不要)
├── icons_list.js    # アイコンピッカー用リスト
├── googleAuth.js    # Google OAuth + Drive連携
├── manifest.json    # PWA設定
├── notification.js  # 通知設定(現在調整中)
├── sw.js            # 簡易Service Worker
└── img/ , sound/  # 画像・効果音
```

---

 ## 🔭 現在開発中...

- カレンダー通知連携（`.ics`ファイル出力でGoogle/Apple/カレンダーに追加）
- キャラクターによるナビ機能（固定セリフ or AI連携 もしくは両方）

---

 ##  発表当日に使用したPowerPoint

 <img width="1197" height="671" alt="スクリーンショット 2026-06-27 113329" src="https://github.com/user-attachments/assets/84952b19-e9c6-4a04-ac39-dcfbcd9d7471" />

[発表で工夫していた点などをメモに記載しています。よければご覧ください！](https://kobedenshi078-my.sharepoint.com/:p:/g/personal/kd1331363_kobedenshi078_onmicrosoft_com/IQDSbQaqqplCTafiMcodLiLOAfyLUaFA-6PZa4WfUWKWe5E?e=ZARr6S&nav=eyJzSWQiOjI1NiwiY0lkIjoyNDUzODU4MTQzfQ)](https://kobedenshi078-my.sharepoint.com/:p:/g/personal/kd1331363_kobedenshi078_onmicrosoft_com/IQDSbQaqqplCTafiMcodLiLOAfyLUaFA-6PZa4WfUWKWe5E?e=eapwi0&nav=eyJzSWQiOjI1NiwiY0lkIjoyNDUzODU4MTQzfQ)

※  PC推奨



 


