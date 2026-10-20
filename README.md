# CRM(Java版)

顧客情報と対応履歴を一元管理する顧客管理 Web システム。属人化を解消し、対応品質と業務効率を上げることを目的としています。

**URL**: https://crmregister.nanasinogonbei.com/

| ログイン情報 | |
|---|---|
| メールアドレス | `testusername@gmail.com` |
| パスワード | `e1IPfcaujDjlhFj4550-x9zp9tmH` |

> **開発状況**: 現在実装済みなのは認証(ログイン / JWT)とユーザー管理です。顧客管理・問い合わせ管理などの機能は [docs/](docs/) の設計に沿って今後実装します。

## 技術スタック

| 層 | 技術 |
|---|---|
| フロントエンド | React 19 / TypeScript / Vite / React Router / Tailwind CSS / react-hook-form |
| バックエンド | Java 25 / Spring Boot 4.1 (Web, Data JPA, Security) / Flyway |
| データベース | MySQL |
| 認証 | JWT (HS512、有効期限 24 時間) |
| テスト | JUnit / ArchUnit (backend)、Vitest / Testing Library (frontend) |
| CI | GitHub Actions (バックエンドテスト、シークレットスキャン) |

## ディレクトリ構成

```
.
├── backend/    Spring Boot アプリケーション (Maven)
├── frontend/   React アプリケーション (Vite)
├── docs/       要件定義書(RDD)・基本設計書(BD)・影響分析(CIA)・メモ
└── .github/    CI ワークフロー
```

## 必要なもの

- JDK 25
- Node.js (npm)
- MySQL (ローカルの `localhost:3306` に `mydb` データベースを用意)

## セットアップ

### 1. バックエンド

```bash
cd backend
cp .env.example .env   # 各値を実値に書き換える
./mvnw spring-boot:run
```

`.env` は Git の追跡対象外です。主な設定項目は以下です。

| 変数 | 必須 | 説明 |
|---|---|---|
| `DB_USERNAME` / `DB_PASSWORD` | 必須 | DB 接続情報 |
| `JWT_SECRET` | 必須 | 64 バイト以上のランダム文字列 (`openssl rand -base64 48` で生成) |
| `ADMIN_USERNAME` / `ADMIN_EMAIL` / `ADMIN_PASSWORD` | 任意 | 3 つ揃えると起動時に初期 ADMIN を作成 (ADMIN が既にいればスキップ) |
| `DB_URL` | 任意 | 既定は `jdbc:mysql://localhost:3306/mydb` |

詳細は [backend/.env.example](backend/.env.example) のコメントを参照してください。DB のスキーマは Flyway ([backend/src/main/resources/db/migration/](backend/src/main/resources/db/migration/)) で管理されます。

バックエンドは `http://localhost:8080` で起動します。

### 2. フロントエンド

```bash
cd frontend
npm install
npm run dev
```

`http://localhost:5173` で起動します。`/api` 配下のリクエストは開発サーバーがバックエンド (`localhost:8080`) へ転送します (転送時に `/api` は外れます)。

## API

| メソッド | パス | 説明 |
|---|---|---|
| POST | `/auth/login` | ログイン (JWT を返す) |
| GET | `/users` | ユーザー一覧 |
| POST | `/users` | ユーザー作成 (ADMIN 限定) |
| GET | `/users/{id}` | ユーザー取得 |
| PUT | `/users/{id}` | ユーザー更新 |
| DELETE | `/users/{id}` | ユーザー削除 |

フロントエンドの API 型定義は、バックエンド起動中に `npm run gen:api` で OpenAPI から再生成できます。

## テスト

```bash
# バックエンド
cd backend && ./mvnw verify

# フロントエンド
cd frontend && npm run test:run
```

## ドキュメント

- [要件定義書](docs/RDD.md)
- [基本設計書](docs/BD.md)
- [変更要望 影響分析レポート](docs/CIA.md)
