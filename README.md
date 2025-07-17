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

## 技術スタック（段階的強化戦略）

### バックエンド（しっかり構築）
- **Node.js 18+ + TypeScript**: 型安全性とスケーラビリティを確保
- **Express.js**: RESTful API + WebSocket
- **Socket.io**: リアルタイム双方向通信
- **Prisma + SQLite**: 型安全なORM、本番はPostgreSQL対応
- **minecraft-server-util**: Minecraftサーバーとの通信
- **RCON接続**: コマンド実行、TPS取得
- **JWT認証**: 管理機能のセキュリティ

### フロントエンド（段階的強化）

#### Phase 1: 最低限（学習コスト最小）
- **バニラ JavaScript + HTML/CSS**: すぐ始められる
- **Bootstrap CDN**: 見た目はそこそこ
- **Socket.io-client**: リアルタイム通信

#### Phase 2: 中間強化
- **TypeScript**: バックエンドの型定義を共有
- **Vite**: 高速ビルドツール
- **Chart.js**: グラフ機能追加

#### Phase 3: 本格強化
- **React**: コンポーネント化
- **Material-UI**: プロフェッショナルなUI
- **PWA**: モバイル対応

## 開発環境構築（バックエンド重視）

### 前提条件
- **Node.js** (v18以上) - [公式サイト](https://nodejs.org/)
- **VS Code** (推奨) - 開発エディタ

### プロジェクト初期化
```bash
# プロジェクト作成
mkdir minecraft-monitor
cd minecraft-monitor

# バックエンド（しっかり構築）
mkdir server
cd server
npm init -y

# バックエンド依存関係（本格的）
npm install express socket.io minecraft-server-util prisma @prisma/client
npm install cors dotenv jsonwebtoken bcryptjs rcon
npm install -D typescript @types/node @types/express @types/jsonwebtoken
npm install -D nodemon ts-node @types/cors prisma

# TypeScript設定
npx tsc --init

# Prisma初期化
npx prisma init

# フロントエンド（最初は簡単）
cd ..
mkdir public
```

### ディレクトリ構造（バックエンド重視）
```
minecraft-monitor/
├── server/                    # バックエンド（しっかり構築）
│   ├── src/
│   │   ├── controllers/       # API コントローラー
│   │   │   ├── serverController.ts
│   │   │   ├── playerController.ts
│   │   │   └── maintenanceController.ts
│   │   ├── services/          # ビジネスロジック
│   │   │   ├── minecraftService.ts
│   │   │   ├── rconService.ts
│   │   │   └── monitoringService.ts
│   │   ├── models/            # データモデル
│   │   ├── middleware/        # 認証・バリデーション
│   │   ├── routes/           # ルーティング
│   │   ├── socket/           # WebSocket イベント
│   │   ├── types/            # TypeScript 型定義
│   │   └── app.ts           # メインアプリケーション
│   ├── prisma/
│   │   └── schema.prisma    # データベーススキーマ
│   ├── dist/                # TypeScript ビルド出力
│   ├── package.json
│   ├── tsconfig.json
│   └── .env
├── public/                   # フロントエンド（段階的強化）
│   ├── index.html           # Phase 1: バニラHTML
│   ├── style.css
│   ├── app.js
│   └── (将来的にReactに移行)
└── shared/                  # 共通型定義
    └── types.ts
```

## 設定

### バックエンド環境変数 (server/.env)
```env
# サーバー設定
PORT=5000
NODE_ENV=development

# Minecraftサーバー接続情報
MINECRAFT_HOST=localhost
MINECRAFT_PORT=25565
MINECRAFT_RCON_HOST=localhost
MINECRAFT_RCON_PORT=25575
MINECRAFT_RCON_PASSWORD=your_rcon_password

# データベース
DATABASE_URL="file:./dev.db"

# JWT認証
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRES_IN=24h

# CORS
CORS_ORIGIN=http://localhost:3000

# 監視設定
MONITOR_INTERVAL=5000
TPS_ALERT_THRESHOLD=15.0
```

### Prisma設定 (server/prisma/schema.prisma)
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

model Server {
  id        Int      @id @default(autoincrement())
  name      String
  host      String
  port      Int
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Player {
  id       Int      @id @default(autoincrement())
  uuid     String   @unique
  name     String
  firstSeen DateTime @default(now())
  lastSeen  DateTime @default(now())
}

model ServerStatus {
  id           Int      @id @default(autoincrement())
  online       Boolean
  playerCount  Int
  maxPlayers   Int
  tps          Float?
  cpu          Float?
  memory       Float?
  timestamp    DateTime @default(now())
}

model Maintenance {
  id          Int      @id @default(autoincrement())
  title       String
  description String?
  startTime   DateTime
  endTime     DateTime?
  isActive    Boolean  @default(false)
  createdAt   DateTime @default(now())
}
```

### 開発サーバーの起動
```bash
# バックエンド起動（開発モード）
cd server
npm run dev

# フロントエンド起動（シンプル版）
cd public
npx serve . -p 3000
```

## API仕様（本格的なRESTful API）

### RESTful API エンドポイント
```typescript
// サーバー状態
GET    /api/servers                    # サーバー一覧
GET    /api/servers/:id                # 特定サーバー情報
GET    /api/servers/:id/status         # サーバー状態
GET    /api/servers/:id/tps            # TPS履歴

// プレイヤー管理
GET    /api/players                    # プレイヤー一覧
GET    /api/players/:uuid              # 特定プレイヤー情報
GET    /api/players/online             # オンラインプレイヤー

// メンテナンス
GET    /api/maintenance                # メンテナンス一覧
POST   /api/maintenance                # メンテナンス作成
PUT    /api/maintenance/:id            # メンテナンス更新
DELETE /api/maintenance/:id            # メンテナンス削除

// 統計・履歴
GET    /api/stats/daily                # 日次統計
GET    /api/stats/players              # プレイヤー統計
GET    /api/history/tps                # TPS履歴
```

### WebSocket イベント（型定義あり）
```typescript
// server/src/types/socket.ts
interface ServerToClientEvents {
  'server-status': (data: ServerStatusData) => void;
  'tps-update': (data: TPSData) => void;
  'player-join': (player: PlayerData) => void;
  'player-leave': (player: PlayerData) => void;
  'alert': (alert: AlertData) => void;
}

interface ClientToServerEvents {
  'subscribe-server': (serverId: string) => void;
  'unsubscribe-server': (serverId: string) => void;
  'admin-command': (command: AdminCommand) => void;
}

interface ServerStatusData {
  serverId: string;
  online: boolean;
  playerCount: number;
  maxPlayers: number;
  tps: number;
  cpu?: number;
  memory?: number;
  uptime: number;
  timestamp: Date;
}
```

### フロントエンド用API（段階的）

#### Phase 1: 基本HTML/JS
```javascript
// public/app.js - シンプル版
const API_BASE = 'http://localhost:5000/api';

async function fetchServerStatus() {
  const response = await fetch(`${API_BASE}/servers/1/status`);
  return response.json();
}

// WebSocket接続
const socket = io('http://localhost:5000');
socket.on('server-status', updateUI);
```

#### Phase 2: TypeScript強化
```typescript
// 型定義を共有
import { ServerStatusData } from '../shared/types';

class MinecraftMonitor {
  private socket: Socket;
  
  constructor() {
    this.socket = io();
    this.setupEventListeners();
  }
  
  private setupEventListeners() {
    this.socket.on('server-status', this.handleServerStatus);
  }
  
  private handleServerStatus(data: ServerStatusData) {
    // 型安全な処理
  }
}
```

## デプロイ（シンプル構成）

### 本番環境（Ubuntu Server - 直接アクセス）
```bash
# Node.js インストール
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# プロジェクトアップロード
scp -r minecraft-monitor user@server:/home/user/

# サーバーで起動
cd minecraft-monitor/server
npm install
npm run build

# PM2でプロセス管理
npm install -g pm2
pm2 start dist/app.js --name minecraft-monitor

# ポート開放（HTTPSは後で）
sudo ufw allow 5000

# 自動起動設定
pm2 startup
pm2 save
```

### Cloudflare DNS設定（プロキシなし）
```
# Cloudflareダッシュボードで設定
Type: A
Name: minecraft-monitor (または好きなサブドメイン)
IPv4 address: your-server-ip
Proxy status: DNS only (灰色の雲) ← プロキシOFF
TTL: Auto
```

### アクセス方法
- **開発**: http://localhost:5000
- **本番**: http://minecraft-monitor.yourdomain.com:5000
- **IP直接**: http://your-server-ip:5000

### HTTPS化（後で必要なら）
```bash
# Let's Encrypt証明書取得
sudo apt install certbot
sudo certbot certonly --standalone -d minecraft-monitor.yourdomain.com

# Express でHTTPS設定
# server/src/app.ts に追加
import https from 'https';
import fs from 'fs';

if (process.env.NODE_ENV === 'production') {
  const options = {
    key: fs.readFileSync('/etc/letsencrypt/live/minecraft-monitor.yourdomain.com/privkey.pem'),
    cert: fs.readFileSync('/etc/letsencrypt/live/minecraft-monitor.yourdomain.com/fullchain.pem')
  };
  
  https.createServer(options, app).listen(443, () => {
    console.log('HTTPS Server running on port 443');
  });
}
```

### 本番環境変数設定
```bash
# サーバーで環境変数設定
cd minecraft-monitor/server
cp .env.example .env
nano .env

# .env ファイル内容
PORT=5000
NODE_ENV=production
MINECRAFT_HOST=localhost
MINECRAFT_PORT=25565
DATABASE_URL="file:./production.db"
CORS_ORIGIN=http://minecraft-monitor.yourdomain.com:5000
```

### セキュリティ設定（最低限）
```bash
# UFW設定
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 5000

# 不要なサービス停止
sudo systemctl disable apache2 nginx

# 定期的な自動更新
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
```

### Cloudflare設定のメリット
- **DDoS保護**: プロキシOFFでも基本保護は有効
- **DNS管理**: 簡単にドメイン管理
- **SSL証明書**: 必要なら後でプロキシONにするだけ
- **アナリティクス**: 基本的な統計情報

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

## 開発状況（段階的強化戦略）

### Phase 1: バックエンド基盤 + 最低限フロント（1週間）
**バックエンド（しっかり）**
- [ ] TypeScript + Express セットアップ
- [ ] Prisma + SQLite セットアップ
- [ ] minecraft-server-util 統合
- [ ] WebSocket基盤（Socket.io）
- [ ] RESTful API 基本構造

**フロントエンド（最低限）**
- [ ] 基本HTML/CSS/JS
- [ ] Bootstrap CDN
- [ ] WebSocket接続
- [ ] 基本的なデータ表示

### Phase 2: Minecraft連携強化（1週間）
**バックエンド**
- [ ] RCON接続実装
- [ ] TPS監視機能
- [ ] プレイヤー管理
- [ ] データベース永続化
- [ ] エラーハンドリング強化

**フロントエンド**
- [ ] リアルタイム更新
- [ ] Chart.js でグラフ追加
- [ ] レスポンシブ対応

### Phase 3: 高度な機能（2週間）
**バックエンド**
- [ ] JWT認証システム
- [ ] 管理者機能
- [ ] 統計・履歴機能
- [ ] アラート機能
- [ ] API ドキュメント

**フロントエンド**
- [ ] TypeScript移行
- [ ] 管理画面
- [ ] 高度なグラフ機能

### Phase 4: React移行（1-2週間）
**フロントエンド全面刷新**
- [ ] React + TypeScript セットアップ
- [ ] Material-UI 導入
- [ ] コンポーネント設計
- [ ] 状態管理（Redux Toolkit）
- [ ] PWA対応

### Phase 5: 本番対応（1週間）
- [ ] Docker化
- [ ] CI/CD
- [ ] セキュリティ強化
- [ ] パフォーマンス最適化
- [ ] モニタリング

---

## 開発チュートリアル（段階的強化版）

### Phase 1: バックエンド基盤構築

#### 1. プロジェクト初期化
```bash
mkdir minecraft-monitor && cd minecraft-monitor
mkdir server && cd server
npm init -y
npm install express socket.io minecraft-server-util prisma @prisma/client cors dotenv
npm install -D typescript @types/node @types/express nodemon ts-node
npx tsc --init
npx prisma init
```

#### 2. バックエンド基本構造（server/src/app.ts）
```typescript
import express from 'express';
import { createServer } from 'http';
import { Server } from 'socket.io';
import cors from 'cors';
import { PrismaClient } from '@prisma/client';
import { status } from 'minecraft-server-util';

const app = express();
const server = createServer(app);
const io = new Server(server, {
  cors: { origin: process.env.CORS_ORIGIN || "*" }
});
const prisma = new PrismaClient();

app.use(cors());
app.use(express.json());
app.use(express.static('../public'));

// APIルート
app.get('/api/servers/:id/status', async (req, res) => {
  try {
    const result = await status(
      process.env.MINECRAFT_HOST || 'localhost',
      parseInt(process.env.MINECRAFT_PORT || '25565')
    );
    
    const statusData = {
      online: true,
      playerCount: result.players.online,
      maxPlayers: result.players.max,
      version: result.version.name,
      timestamp: new Date()
    };
    
    // データベースに保存
    await prisma.serverStatus.create({
      data: {
        online: statusData.online,
        playerCount: statusData.playerCount,
        maxPlayers: statusData.maxPlayers,
        timestamp: statusData.timestamp
      }
    });
    
    res.json(statusData);
  } catch (error) {
    res.json({ online: false, playerCount: 0, timestamp: new Date() });
  }
});

// WebSocket監視
class ServerMonitor {
  private monitoringInterval: NodeJS.Timeout | null = null;
  
  start() {
    this.monitoringInterval = setInterval(async () => {
      try {
        const result = await status(
          process.env.MINECRAFT_HOST || 'localhost',
          parseInt(process.env.MINECRAFT_PORT || '25565')
        );
        
        const data = {
          online: true,
          playerCount: result.players.online,
          maxPlayers: result.players.max,
          tps: 20.0, // 後でRCONから取得
          timestamp: new Date()
        };
        
        io.emit('server-status', data);
      } catch (error) {
        io.emit('server-status', { 
          online: false, 
          playerCount: 0, 
          tps: 0,
          timestamp: new Date()
        });
      }
    }, parseInt(process.env.MONITOR_INTERVAL || '5000'));
  }
  
  stop() {
    if (this.monitoringInterval) {
      clearInterval(this.monitoringInterval);
    }
  }
}

const monitor = new ServerMonitor();
monitor.start();

server.listen(process.env.PORT || 5000, () => {
  console.log(`Server running on port ${process.env.PORT || 5000}`);
});
```

#### 3. package.json scripts追加
```json
{
  "scripts": {
    "dev": "nodemon --exec ts-node src/app.ts",
    "build": "tsc",
    "start": "node dist/app.js"
  }
}
```

### Phase 1: フロントエンド（最低限版）

#### HTML（public/index.html）
```html
<!DOCTYPE html>
<html>
<head>
    <title>Minecraft サーバー監視</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.socket.io/4.5.0/socket.io.min.js"></script>
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    <div class="container-fluid mt-3">
        <h1 class="mb-4">Minecraft サーバー監視</h1>
        
        <div class="row g-3">
            <div class="col-md-3">
                <div class="card h-100">
                    <div class="card-body">
                        <h5 class="card-title">サーバー状態</h5>
                        <p id="status" class="card-text fs-4">確認中...</p>
                        <small id="last-update" class="text-muted"></small>
                    </div>
                </div>
            </div>
            
            <div class="col-md-3">
                <div class="card h-100">
                    <div class="card-body">
                        <h5 class="card-title">プレイヤー数</h5>
                        <p id="players" class="card-text fs-4">確認中...</p>
                        <div class="progress">
                            <div id="player-progress" class="progress-bar" style="width: 0%"></div>
                        </div>
                    </div>
                </div>
            </div>
            
            <div class="col-md-3">
                <div class="card h-100">
                    <div class="card-body">
                        <h5 class="card-title">TPS</h5>
                        <p id="tps" class="card-text fs-4">確認中...</p>
                        <div class="progress">
                            <div id="tps-progress" class="progress-bar" style="width: 0%"></div>
                        </div>
                    </div>
                </div>
            </div>
            
            <div class="col-md-3">
                <div class="card h-100">
                    <div class="card-body">
                        <h5 class="card-title">接続テスト</h5>
                        <button id="test-btn" class="btn btn-primary">接続確認</button>
                        <p id="test-result" class="mt-2"></p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const socket = io();
        
        socket.on('server-status', function(data) {
            // サーバー状態
            const statusElement = document.getElementById('status');
            statusElement.innerHTML = data.online 
                ? '<span class="text-success">●</span> オンライン' 
                : '<span class="text-danger">●</span> オフライン';
            
            // プレイヤー数
            document.getElementById('players').innerHTML = `${data.playerCount}/${data.maxPlayers || '?'}人`;
            
            // プレイヤー数プログレスバー
            const playerPercent = data.maxPlayers ? (data.playerCount / data.maxPlayers) * 100 : 0;
            document.getElementById('player-progress').style.width = playerPercent + '%';
            
            // TPS
            const tpsColor = data.tps > 18 ? 'success' : data.tps > 15 ? 'warning' : 'danger';
            document.getElementById('tps').innerHTML = `<span class="text-${tpsColor}">TPS: ${data.tps}</span>`;
            
            // TPS プログレスバー
            const tpsPercent = (data.tps / 20) * 100;
            const tpsProgressClass = data.tps > 18 ? 'bg-success' : data.tps > 15 ? 'bg-warning' : 'bg-danger';
            const tpsProgress = document.getElementById('tps-progress');
            tpsProgress.style.width = tpsPercent + '%';
            tpsProgress.className = `progress-bar ${tpsProgressClass}`;
            
            // 最終更新時刻
            document.getElementById('last-update').textContent = 
                `最終更新: ${new Date(data.timestamp).toLocaleTimeString()}`;
        });
        
        // 手動接続テスト
        document.getElementById('test-btn').addEventListener('click', async () => {
            const result = document.getElementById('test-result');
            result.textContent = '確認中...';
            
            try {
                const response = await fetch('/api/servers/1/status');
                const data = await response.json();
                result.innerHTML = data.online 
                    ? '<span class="text-success">接続成功</span>' 
                    : '<span class="text-danger">接続失敗</span>';
            } catch (error) {
                result.innerHTML = '<span class="text-danger">エラー</span>';
            }
        });
    </script>
</body>
</html>
```

#### 4. 起動と確認
```bash
# データベース初期化
npx prisma db push

# サーバー起動
npm run dev

# ブラウザで http://localhost:5000 にアクセス
```

これで **バックエンドはしっかり、フロントエンドは最低限** のスタートができます！

最終更新: 2025年7月17日
