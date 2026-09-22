# Deploy — deviwax.com

เว็บ static HTML — serve ด้วย `nginx:alpine` + mount repo (ไม่มี build step)

## Flow แก้ไขเว็บ

```powershell
# Local (Windows) — แก้ไฟล์ใน httpdocs/ แล้ว
cd D:\git\deviwax.com
git add -A; git commit -m "update"; git push
```

```bash
# VPS (ssh supawet@119.59.113.70 → sudo su -)
cd /data/deviwax.com/repo && git pull
# เว็บอัปเดตทันที ไม่ต้อง restart (nginx อ่านไฟล์จาก mount สดทุก request)
```

## โครงสร้างบน VPS

```
/data/deviwax.com/
├── repo/                  # git clone ของ repo นี้
│   └── httpdocs/          # → mount เข้า /usr/share/nginx/html
└── docker-compose.yml     # nginx:alpine, port 9094
```

- Container: `deviwaxcom-web` publish `9094:80`
- NPM proxy: `deviwax.com`, `www.deviwax.com` → `http://119.59.113.70:9094` + SSL Let's Encrypt
- DNS: GoDaddy A record → `119.59.113.70`, CNAME `www` → `deviwax.com`

## ตั้งใหม่จากศูนย์

```bash
mkdir -p /data/deviwax.com
git clone https://github.com/altiron/deviwax.com.git /data/deviwax.com/repo
# copy docker-compose.yml จาก repo ไปไว้ /data/deviwax.com/ แล้วแก้ volume path
cd /data/deviwax.com && docker compose up -d
# แล้วเพิ่ม Proxy Host ใน NPM + ขอ SSL cert
```
