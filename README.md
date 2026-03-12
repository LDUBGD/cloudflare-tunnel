# cloudflare-tunnel

Окремий Docker-стек для Cloudflare Tunnel (`cloudflared`).  
Виокремлено з [DSpace-docker](../DSpace-docker).

## Архітектура

```
Інтернет → Cloudflare → cloudflared (tunnel) → proxy-net → Traefik → DSpace
```

Контейнер підключається до зовнішньої мережі `proxy-net`, яку **створює DSpace-stack**.  
Запускай DSpace-stack першим.

## Швидкий старт

```bash
# 1. Скопіюй env
cp .env.example .env

# 2. Встав реальний TUNNEL_TOKEN
#    Zero Trust → Networks → Tunnels → <твій тунель> → Configure → Install connector
nano .env

# 3. Переконайся, що DSpace-stack вже запущено і мережа proxy-net існує
docker network ls | grep proxy-net

# 4. Запустити тунель
docker compose up -d

# 5. Перевірка
docker compose ps
docker compose logs -f tunnel
```

## Змінні середовища

| Змінна | Дефолт | Опис |
|--------|--------|------|
| `TUNNEL_TOKEN` | _обов'язково_ | Токен тунелю з Cloudflare Dashboard |
| `CLOUDFLARE_TUNNEL_VERSION` | `2026.2.0` | Версія образу `cloudflared` |
| `PROXY_NET_NAME` | `proxy-net` | Ім'я зовнішньої Docker-мережі |
| `COMPOSE_PROJECT_NAME` | `cf-tunnel` | Ім'я проєкту Compose |

## Оновлення версії cloudflared

```bash
# Змінити CLOUDFLARE_TUNNEL_VERSION у .env, потім:
docker compose pull && docker compose up -d
```

## Залежності

- **DSpace-stack** (`../DSpace-docker`) — повинен бути запущений першим, щоб мережа `proxy-net` існувала.
- **Cloudflare Dashboard** — налаштування ingress-правил (до якого upstream скеровувати трафік: `http://traefik:80`).

> У майбутньому, коли Traefik виноситиметься в окремий стек, `proxy-net` мігрує туди,  
> а DSpace-stack відмітить її як `external: true`. Конфіг cloudflare-tunnel при цьому не зміниться.
