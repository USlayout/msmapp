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

## 技術スタック

### フロントエンド
- **React 18**: コンポーネントベース開発、リアルタイム更新に最適
- **TypeScript**: 型安全性の確保、開発効率向上
- **Socket.io-client**: WebSocketによるリアルタイム通信
- **Recharts**: TPS監視グラフの描画
- **Material-UI (MUI)**: モダンなUIコンポーネント

### バックエンド
- **Node.js 18+**: 軽量で高速、豊富なMinecraft連携ライブラリ
- **Express.js**: シンプルで拡張しやすいWebフレームワーク
- **Socket.io**: リアルタイム双方向通信
- **minecraft-server-util**: Minecraftサーバーとの通信
- **Prisma + SQLite**: 型安全なORM、開発時は軽量DB

### インフラ
- **サーバー**: Ubuntu Server
- **Webサーバー**: Nginx（リバースプロキシ）
- **プロセス管理**: PM2
- **本番DB**: PostgreSQL

## 開発環境構築

### 前提条件
- **Node.js** (v18以上) - [公式サイト](https://nodejs.org/)からダウンロード
- **npm** または **yarn** - Node.js と一緒にインストールされます
- **Git** - バージョン管理
- **VS Code** (推奨) - 開発エディタ

### プロジェクト初期化
```bash
# プロジェクトディレクトリの作成
mkdir minecraft-server-monitor
cd minecraft-server-monitor

# バックエンドのセットアップ
mkdir server
cd server
npm init -y
npm install express socket.io minecraft-server-util prisma @prisma/client cors dotenv
npm install -D typescript @types/node @types/express nodemon ts-node

# フロントエンドのセットアップ
cd ..
npx create-react-app client --template typescript
cd client
npm install socket.io-client @mui/material @emotion/react @emotion/styled
npm install recharts @types/recharts

# 開発用の同時起動ツール
cd ..
npm install -D concurrently
```

### ディレクトリ構造
```
minecraft-server-monitor/
├── server/                 # バックエンド
│   ├── src/
│   │   ├── controllers/    # API ロジック
│   │   ├── services/       # Minecraft サーバー連携
│   │   ├── models/         # データベースモデル
│   │   ├── routes/         # ルーティング
│   │   ├── socket/         # WebSocket 処理
│   │   └── app.ts         # メインアプリケーション
│   ├── prisma/
│   │   └── schema.prisma  # データベーススキーマ
│   ├── package.json
│   └── .env
├── client/                 # フロントエンド
│   ├── src/
│   │   ├── components/     # React コンポーネント
│   │   ├── pages/          # ページコンポーネント
│   │   ├── hooks/          # カスタムフック
│   │   ├── services/       # API 通信
│   │   ├── types/          # TypeScript 型定義
│   │   └── App.tsx
│   └── package.json
├── package.json            # ルートパッケージ（開発用）
└── README.md
```

## 設定

### サーバー側環境変数 (server/.env)
```env
# サーバー設定
PORT=5000
NODE_ENV=development

# Minecraftサーバー接続情報
MINECRAFT_SERVER_HOST=localhost
MINECRAFT_SERVER_PORT=25565
MINECRAFT_RCON_PORT=25575
MINECRAFT_RCON_PASSWORD=your_rcon_password

# データベース設定
DATABASE_URL="file:./dev.db"

# CORS設定
CORS_ORIGIN=http://localhost:3000

# その他
REFRESH_INTERVAL=5000  # データ更新間隔（ミリ秒）
```

### クライアント側環境変数 (client/.env)
```env
# API エンドポイント
REACT_APP_API_URL=http://localhost:5000
REACT_APP_SOCKET_URL=http://localhost:5000
```

### 開発サーバーの起動
```bash
# ルートディレクトリで（サーバーとクライアント同時起動）
npm run dev

# または個別起動
# サーバー起動
cd server && npm run dev

# クライアント起動（別ターミナル）
cd client && npm start
```

## API仕様

### RESTful API エンドポイント
- `GET /api/server/status` - サーバー状態取得
- `GET /api/server/tps` - TPS情報取得
- `GET /api/players` - プレイヤー情報取得
- `GET /api/maintenance` - メンテナンス情報取得
- `POST /api/maintenance` - メンテナンス情報登録（管理者のみ）

### WebSocket イベント
```typescript
// サーバー → クライアント
interface ServerEvents {
  'server-status': (data: ServerStatus) => void;
  'tps-update': (tps: number) => void;
  'player-update': (players: Player[]) => void;
  'alert': (alert: Alert) => void;
}

// クライアント → サーバー
interface ClientEvents {
  'subscribe-server': () => void;
  'unsubscribe-server': () => void;
}
```

### データ型定義
```typescript
interface ServerStatus {
  online: boolean;
  uptime: number;
  playerCount: number;
  maxPlayers: number;
  tps: number;
  cpu: number;
  memory: number;
}

interface Player {
  name: string;
  uuid: string;
  joinTime: Date;
}

interface Alert {
  type: 'warning' | 'error' | 'info';
  message: string;
  timestamp: Date;
}
```

## デプロイ

### 本番環境構築

#### 1. サーバー側のビルドとデプロイ
```bash
# サーバーディレクトリで
cd server

# TypeScript のビルド
npm run build

# 本番用データベースの準備
npx prisma migrate deploy
npx prisma generate

# PM2 でプロセス管理
npm install -g pm2
pm2 start ecosystem.config.js
```

#### 2. クライアント側のビルドとデプロイ
```bash
# クライアントディレクトリで
cd client

# 本番ビルド
npm run build

# ビルドされたファイルを Nginx で配信
sudo cp -r build/* /var/www/html/
```

#### 3. PM2 設定ファイル (server/ecosystem.config.js)
```javascript
module.exports = {
  apps: [{
    name: 'minecraft-monitor-server',
    script: './dist/app.js',
    instances: 1,
    env: {
      NODE_ENV: 'development'
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 5000
    }
  }]
};
```

### Nginx設定例
```nginx
# Frontend (React) の配信
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/html;
    index index.html;

    # React Router 対応
    location / {
        try_files $uri $uri/ /index.html;
    }

    # API プロキシ設定
    location /api/ {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # WebSocket プロキシ設定
    location /socket.io/ {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
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

### Phase 1: プロジェクト基盤構築
- [ ] プロジェクト初期設定（Node.js + React + TypeScript）
- [ ] データベース設計（Prisma + SQLite）
- [ ] 基本的なAPI構造の構築
- [ ] WebSocket通信の基盤実装

### Phase 2: Minecraftサーバー連携
- [ ] minecraft-server-util を使用したサーバー接続
- [ ] RCON接続によるコマンド実行
- [ ] プレイヤー情報取得機能
- [ ] サーバー状態監視機能

### Phase 3: リアルタイム監視機能
- [ ] TPS監視とグラフ表示（Recharts）
- [ ] リアルタイムプレイヤー情報更新
- [ ] アラート機能の実装
- [ ] WebSocketによるリアルタイム通信

### Phase 4: フロントエンド実装
- [ ] React + Material-UI による UI 構築
- [ ] ダッシュボード画面の作成
- [ ] TPS監視グラフの実装
- [ ] プレイヤー一覧表示
- [ ] メンテナンス情報管理画面

### Phase 5: 高度な機能とUI/UX
- [ ] レスポンシブデザイン対応
- [ ] ダークモード対応
- [ ] 通知機能（ブラウザ通知）
- [ ] 統計情報とレポート機能

### Phase 6: テスト・デプロイ
- [ ] ユニットテスト実装（Jest）
- [ ] E2Eテスト実装（Playwright）
- [ ] Docker化
- [ ] CI/CD パイプライン構築
- [ ] 本番環境デプロイ

---

## 開発チュートリアル

### 1. 基本プロジェクトの作成
```bash
# プロジェクトのクローンまたは作成
git clone https://github.com/USlayout/msmapp.git
cd msmapp

# 依存関係のインストール
npm install
```

### 2. 最初のコンポーネント作成
```typescript
// client/src/components/ServerStatus.tsx
import React, { useState, useEffect } from 'react';
import { Card, CardContent, Typography } from '@mui/material';
import { io } from 'socket.io-client';

const ServerStatus: React.FC = () => {
  const [isOnline, setIsOnline] = useState(false);
  const [playerCount, setPlayerCount] = useState(0);

  useEffect(() => {
    const socket = io('http://localhost:5000');
    
    socket.on('server-status', (data) => {
      setIsOnline(data.online);
      setPlayerCount(data.playerCount);
    });

    return () => socket.disconnect();
  }, []);

  return (
    <Card>
      <CardContent>
        <Typography variant="h5">
          サーバー状態: {isOnline ? 'オンライン' : 'オフライン'}
        </Typography>
        <Typography variant="body1">
          プレイヤー数: {playerCount}
        </Typography>
      </CardContent>
    </Card>
  );
};

export default ServerStatus;
```

### 3. サーバーサイドの基本実装
```typescript
// server/src/app.ts
import express from 'express';
import { createServer } from 'http';
import { Server } from 'socket.io';
import cors from 'cors';

const app = express();
const server = createServer(app);
const io = new Server(server, {
  cors: { origin: "http://localhost:3000" }
});

app.use(cors());
app.use(express.json());

// Minecraft サーバー監視のメインロジック
setInterval(async () => {
  // minecraft-server-util を使用してサーバー状態を取得
  // 取得したデータをクライアントにブロードキャスト
  io.emit('server-status', {
    online: true,
    playerCount: 3,
    tps: 19.8
  });
}, 5000);

server.listen(5000, () => {
  console.log('Server running on port 5000');
});
```

最終更新: 2025年7月17日
