# immich-docker

[Immich](https://immich.app) photo & video library for Helsinki, served at **https://images.jwowk.net**.

Deployed as a Portainer GitOps stack from `docker-compose.yml` on `master`, with auto-update polling every 5 minutes.

## Portainer stack settings

| Setting | Value |
|---|---|
| Build method | Repository |
| Compose path | `docker-compose.yml` |
| Reference | `refs/heads/master` |
| GitOps updates | Polling, 5m |
| Env: `DB_PASSWORD` | random, `A-Za-z0-9` only |

Optional env overrides (defaults are in the compose file): `TZ`, `UPLOAD_LOCATION`, `DB_DATA_LOCATION`, `MODEL_CACHE_LOCATION`, `IMMICH_VERSION`.

## Data on Helsinki

| Path | Contents |
|---|---|
| `/docker/immich/library` | originals, thumbnails, encoded video, nightly DB dumps (`backups/`) |
| `/docker/immich/postgres` | Postgres data (must stay on local disk) |
| `/docker/immich/model-cache` | ML models (safe to delete, re-downloads) |

## Nginx Proxy Manager

Proxy host `images.jwowk.net` -> `http://immich_server:2283`, websockets on, Force SSL, HTTP/2, Let's Encrypt cert. Advanced config:

```nginx
client_max_body_size 50000M;
proxy_read_timeout 600s;
proxy_send_timeout 600s;
send_timeout 600s;
```

Without the larger body size and timeouts, big video uploads from the mobile app fail.

## Mobile

Android app: [Google Play](https://play.google.com/store/apps/details?id=app.alextran.immich) or [F-Droid](https://f-droid.org/packages/app.alextran.immich/). Server URL: `https://images.jwowk.net`.

## Upgrading

1. Read the [release notes](https://github.com/immich-app/immich/releases) for breaking changes.
2. Diff this compose file against the new release's `docker-compose.yml` (postgres/valkey digests change occasionally).
3. Bump `IMMICH_VERSION` in both image lines and push. Portainer redeploys within 5 minutes.
