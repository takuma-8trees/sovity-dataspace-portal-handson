# Dataspace Portal (localhost demo)

このフォルダは **検証環境（`/home/yagi/sovity_edc`）を一切変更せず**、Dataspace Portal を localhost で体験するための最小セットです。

## 1) 前提

- Docker / Docker Compose が利用可能
- `127.0.0.1` にバインドされたポートをブラウザから開ける

本セットは、ブラウザとコンテナの両方から同一URLで Keycloak に到達できる必要があるため、ローカルにホスト名を追加します。

## 2) hosts を追加

ホストOSの `/etc/hosts` に以下を追加してください。

```text
127.0.0.1 portal.local
127.0.0.1 auth.local
```

## 3) .env を作成

`.env.example` を `.env` にコピーして、値を埋めます。

```bash
cp .env.example .env
```

最低限、以下の `change-me` をすべて置き換えてください。

- `KEYCLOAK_DB_PASSWORD`
- `KEYCLOAK_ADMIN_PASSWORD`
- `PORTAL_DB_PASSWORD`
- `PORTAL_ADMIN_CLIENT_SECRET`
- `PORTAL_CONFIG_API_KEY`
- `PORTAL_OAUTH2_CLIENT_SECRET`
- `PORTAL_COOKIE_SECRET`

### 例: 秘密値の生成

```bash
# 32 bytes base64 (cookie secretに使いやすい)
openssl rand -base64 32

# API key/secret用途
openssl rand -hex 32
```

## 4) 起動

```bash
docker compose --env-file .env -f docker-compose.portal-local.yaml up -d
```

ログを見る場合:

```bash
docker compose -f docker-compose.portal-local.yaml logs -f --tail=200
```

## 5) アクセス

- Portal: `http://portal.local:28080`
- Keycloak: `http://auth.local:28081` (admin / `KEYCLOAK_ADMIN_PASSWORD`)
- Mailpit: `http://localhost:28025`

## 6) 停止/破棄

```bash
docker compose -f docker-compose.portal-local.yaml down
```

DB も含めて完全削除:

```bash
docker compose -f docker-compose.portal-local.yaml down -v
```

## トラブルシュート

### Portal がログイン画面に飛べない / Keycloak に飛べない

- `/etc/hosts` の `portal.local` / `auth.local` を確認
- `KEYCLOAK_PUBLIC_URL` と `PORTAL_URL` が `.env` の値と一致しているか確認
- ポート競合がないか確認（`28080`, `28081`）

### 初回起動が遅い

初回はイメージpullやDB初期化が走るため、数分かかることがあります。

