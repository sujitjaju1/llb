# Deployment Guide — Vyavsay

## Server Details

- **Domain:** https://vyavsayassist.app
- **IP:** 51.20.19.169
- **Provider:** AWS EC2 (t2.micro, eu-north-1)
- **OS:** Ubuntu 24.04
- **SSH Key:** vyavsay.pem
- **DNS:** Name.com (A records → 51.20.19.169)
- **SSL:** Let's Encrypt via Certbot (auto-renews)

## Deploy (One Command)

```bash
ssh -i "vyavsay.pem" ubuntu@51.20.19.169 "cd ~/Vyavsay_Baileys && git pull && docker compose down && docker compose up -d --build"
```

## SSH into Server

```bash
ssh -i "vyavsay.pem" ubuntu@51.20.19.169
```

## Useful Commands (run on server)

```bash
# Check running containers
docker ps

# View backend logs
docker compose logs -f backend

# View frontend logs
docker compose logs -f frontend

# Restart without rebuilding
docker compose restart

# Rebuild only backend
docker compose up -d --build backend

# Rebuild only frontend
docker compose up -d --build frontend

# Stop everything
docker compose down

# Check disk usage
df -h

# Check memory/swap
free -h
```

## Env Files (on server)

- Backend: `~/Vyavsay_Baileys/backend/.env`
- Frontend: `~/Vyavsay_Baileys/frontend/.env`

These are NOT in git. If you recreate the server, you need to create them again.

## Important Notes

- `.dockerignore` in frontend was modified on server to allow `.env` during build (Vite needs it at build time)
- Server has 1GB swap configured at `/swapfile`
- Elastic IP must stay associated to avoid charges
- AWS free tier expires after 12 months

## Nginx (on server)

- Config: `/etc/nginx/sites-available/vyavsay`
- Proxies `/ → localhost:8080` (frontend) and `/api/ → localhost:3005` (backend)
- SSL managed by Certbot, auto-renews via systemd timer

```bash
# Check nginx status
sudo systemctl status nginx

# Reload after config change
sudo nginx -t && sudo systemctl reload nginx

# Renew SSL manually (auto-renews, but just in case)
sudo certbot renew
```
