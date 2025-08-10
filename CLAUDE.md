# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Laravel React スターターキットベースのモダンなウェブアプリケーション。Laravel バックエンド、React フロントエンド、Inertia.js による SPA 機能、Tailwind CSS による UI スタイリングで構成されている。

## 開発コマンド

### PHP/Laravel コマンド
- `composer dev` - 開発サーバー、キュー、ログ、Vite を並行実行（localhost用）
- `composer dev:docker` - Docker 環境用開発サーバー（`--host=0.0.0.0`対応）
- `composer dev:ssr` - SSR モード付きの開発サーバー起動
- `composer test` - PHPUnit テスト実行（設定クリア含む）
- `php artisan serve` - Laravel 開発サーバー単体起動
- `php artisan test` - PHPUnit テスト実行
- `php artisan migrate` - データベースマイグレーション
- `php artisan pint` - PHP コードフォーマット（Laravel Pint）

### JavaScript/React コマンド
- `npm run dev` - Vite 開発サーバー起動
- `npm run build` - プロダクションビルド
- `npm run build:ssr` - SSR ビルド
- `npm run lint` - ESLint による JavaScript/TypeScript ファイルのリント
- `npm run format` - Prettier によるコードフォーマット
- `npm run format:check` - コードフォーマットのチェック
- `npm run types` - TypeScript タイプチェック

## アーキテクチャ構造

### バックエンド（Laravel）
- **Controller**: `app/Http/Controllers/` - 認証、設定、メインロジック
- **Middleware**: `app/Http/Middleware/` - Inertia リクエスト処理、外観設定
- **Models**: `app/Models/` - Eloquent モデル（User）
- **Routes**: `routes/` - web.php, auth.php, settings.php

### フロントエンド（React + Inertia）
- **Pages**: `resources/js/pages/` - Inertia ページコンポーネント
- **Layouts**: `resources/js/layouts/` - アプリ、認証、設定レイアウト
- **Components**: `resources/js/components/` - 再利用可能コンポーネント
- **UI Components**: `resources/js/components/ui/` - Radix UI ベースコンポーネント
- **Hooks**: `resources/js/hooks/` - カスタム React フック

### 重要な技術スタック
- **Laravel 12** - PHP フレームワーク
- **Inertia.js** - Laravel-React 間の SPA ブリッジ
- **React 19** - フロントエンドライブラリ
- **TypeScript** - 型安全性
- **Tailwind CSS 4** - ユーティリティファーストCSS
- **Radix UI** - アクセシブルな UI プリミティブ
- **Ziggy** - Laravel ルートの JavaScript 利用

### データベース・テスト
- SQLite データベース（`database/database.sqlite`）
- PHPUnit テストスイート（Feature/Unit）
- マイグレーションファイル: `database/migrations/`

## 開発時の注意点

### ファイル構造の重要性
- PHP ファイルは PSR-4 オートローディング規約に従う
- React コンポーネントはページコンポーネント解決システムを利用
- レイアウトはネスト構造で階層的に構成

### Inertia.js の動作
- `HandleInertiaRequests.php` で共有データ管理
- ページコンポーネントは `resources/js/pages/` 配下で動的解決
- SSR 対応（`resources/js/ssr.tsx`）

### スタイリングシステム
- Tailwind CSS の設定は `vite.config.ts` で管理
- ダークモード対応済み（`use-appearance.tsx` フック）
- コンポーネントバリアント管理に `class-variance-authority` 使用

## Docker 環境

### プロダクション用（ECS対応）
- `Dockerfile` - マルチステージビルドでフロントエンドアセット生成
- Nginx + PHP-FPM + Supervisor 構成
- ポート80で起動

### ローカル開発用
- `docker-compose.yml` - 本番・開発環境の両方に対応
- `Dockerfile.dev` - 開発専用（ホットリロード対応）

### Docker コマンド
```bash
# 本番環境（ECS想定）- ポート8080でアクセス
docker-compose up app

# ローカル開発環境 - ポート8000でアクセス
docker-compose --profile dev up app-dev

# MySQL使用時
docker-compose --profile mysql --profile dev up
```

### アクセスURL
- **ローカル開発（Docker）**: `http://localhost:8000`
- **ローカル開発（直接）**: `http://localhost:8000` （`composer dev`実行時）
- **本番Docker**: `http://localhost:8080`
- **Vite開発サーバー**: `http://localhost:5173` （開発時のみ）

### 設定ファイル
- `docker/nginx.conf` - Nginx設定
- `docker/supervisord.conf` - プロセス管理設定
- `.dockerignore` - ビルド時除外ファイル
- `vite.config.ts` - Vite設定（Docker対応のHMR設定含む）

## 開発環境のトラブルシューティング

### Docker開発環境
- **ポート8000でアクセスできない**: Laravel サーバーが `--host=0.0.0.0` で起動しているか確認
- **CORSエラー（Vite）**: `vite.config.ts` の `server.host: '0.0.0.0'` 設定を確認
- **ホットリロードが動作しない**: ブラウザで `http://localhost:5173` に直接アクセスできるか確認

### 必要なPHP拡張機能
- `pcntl` - Laravel Pail（ログ監視）に必要
- `pdo_sqlite` - SQLiteデータベース接続用
- `gd` - 画像処理用（Freetype、JPEG対応）