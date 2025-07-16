# msmapp
minecraft_server_monitoring_app

## 概要
Ubuntu Server上で動作するMinecraft Forge 1.20.1サーバーの状態をWebブラウザで監視できるツールです。

## 機能要件

### 主要機能
- **サーバーTPS監視**: サーバー全体のTPS（Ticks Per Second）をリアルタイムで表示
- **プレイヤー情報**: 現在のオンライン人数とプレイヤー一覧
- **メンテナンス情報**: サーバーメンテナンスの予定や状況を表示
- **サーバー状態**: サーバーの稼働状況（オンライン/オフライン）

### 技術仕様
- **サーバー環境**: Ubuntu Server
- **Minecraft**: Forge 1.20.1
- **対象ブラウザ**: モダンブラウザ（Chrome、Firefox、Safari、Edge）

## システム構成

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Webブラウザ   │◄──►│  Webアプリ      │◄──►│ Minecraftサーバー│
│  (フロントエンド) │    │ (バックエンド)   │    │   (Forge 1.20.1) │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 機能詳細

### 1. TPS監視
- リアルタイムでサーバーのTPSを表示
- グラフ形式での履歴表示
- TPS低下時のアラート機能
- 目標TPS: 20.0（理想値）

### 2. プレイヤー管理
- 現在のオンライン人数
- プレイヤー名一覧
- 接続/切断ログ
- 最大同時接続数の記録

### 3. メンテナンス情報
- 定期メンテナンスの予定表示
- 緊急メンテナンスのお知らせ
- メンテナンス履歴
- 管理者向けメンテナンス管理画面

### 4. サーバー状態監視
- サーバーの稼働状況
- サーバー起動時間（アップタイム）
- システムリソース使用率（CPU、メモリ）
- ディスク使用量

## 想定技術スタック

### フロントエンド
- HTML5/CSS3/JavaScript
- フレームワーク: React/Vue.js（要検討）
- リアルタイム通信: WebSocket

### バックエンド
- 言語: Node.js/Python/Java（要検討）
- データベース: SQLite/PostgreSQL/MySQL（要検討）
- API: RESTful API + WebSocket

### インフラ
- サーバー: Ubuntu Server
- Webサーバー: Nginx/Apache（要検討）
- プロセス管理: PM2/systemd

## 開発環境構築

### 前提条件
- Node.js (推奨: v18以上)
- npm または yarn
- Git

### セットアップ手順
```bash
# リポジトリのクローン
git clone [リポジトリURL]
cd マイクラサーバー監視ツール

# 依存関係のインストール
npm install

# 環境設定ファイルの作成
cp .env.example .env

# 開発サーバーの起動
npm run dev
```

## 設定

### 環境変数
```env
# サーバー設定
PORT=3000
NODE_ENV=development

# Minecraftサーバー接続情報
MINECRAFT_SERVER_HOST=localhost
MINECRAFT_SERVER_PORT=25565
MINECRAFT_RCON_PORT=25575
MINECRAFT_RCON_PASSWORD=your_rcon_password

# データベース設定
DATABASE_URL=sqlite://./data/server_monitor.db

# その他
REFRESH_INTERVAL=5000  # データ更新間隔（ミリ秒）
```

## API仕様

### エンドポイント一覧
- `GET /api/server/status` - サーバー状態取得
- `GET /api/server/tps` - TPS情報取得
- `GET /api/players` - プレイヤー情報取得
- `GET /api/maintenance` - メンテナンス情報取得
- `POST /api/maintenance` - メンテナンス情報登録（管理者のみ）

### WebSocket
- チャンネル: `/ws/server-status`
- リアルタイムでTPS、プレイヤー情報を配信

## デプロイ

### 本番環境構築
```bash
# アプリケーションのビルド
npm run build

# 本番環境での起動
npm run start

# または PM2を使用
pm2 start ecosystem.config.js
```

### Nginx設定例
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## セキュリティ

### 実装予定の対策
- HTTPS通信の強制
- CORS設定
- レート制限
- 管理機能への認証・認可
- RCONパスワードの適切な管理

## 監視・ログ

### ログ出力
- アクセスログ
- エラーログ
- Minecraftサーバー接続ログ
- パフォーマンスログ

### アラート機能
- サーバーダウン検知
- TPS低下検知
- 異常なプレイヤー数の変動

## 今後の拡張予定

- [ ] モバイル対応
- [ ] 複数サーバー対応
- [ ] プラグイン情報の表示
- [ ] チャットログの表示
- [ ] 統計情報とレポート機能
- [ ] Discord/Slack通知連携

## 開発状況

- [ ] プロジェクト初期設定
- [ ] フロントエンド実装
- [ ] バックエンドAPI実装
- [ ] Minecraftサーバー連携
- [ ] リアルタイム通信実装
- [ ] UI/UXデザイン
- [ ] テスト実装
- [ ] デプロイ環境構築

---

最終更新: 2025年7月16日
