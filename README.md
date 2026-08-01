# 🐉 Pterodactyl Installer

Installer **Pterodactyl Panel + Wings** yang bersih untuk Ubuntu 20.04+ / Debian 11+.
Dua mode: **Manual** (kontrol penuh) dan **Auto** (one-shot kecuali domain).

## ✨ Fitur
- 🟦 **Mode Manual** — tanya location, username admin, email, DB host/port/name/user, timezone, dll.
- 🟩 **Mode Auto** — semua otomatis, cuma tanya **FQDN (domain)**. Password & token digenerate acak & aman.
- 🔧 Auto-install: nginx, PHP-FPM, MariaDB, Redis, Docker, Certbot (SSL otomatis), Wings daemon.
- 🧱 Systemd service untuk panel (cron) + wings (auto-restart).
- 🔒 Password admin & root DB ditulis ke `/root/pterodactyl_admin_password.txt`.

## 🚀 Pakai lewat npx (npm)
```bash
npx pterodactyl-installer --auto      # otomatis (tanya domain)
npx pterodactyl-installer --manual    # detail lengkap
```

## 🚀 Pakai langsung (curl)
```bash
curl -fsSL https://raw.githubusercontent.com/your-username/pterodactyl-installer/main/install.sh | sudo bash -s -- --auto
```

## 🚀 Clone & jalanin
```bash
git clone https://github.com/your-username/pterodactyl-installer.git
cd pterodactyl-installer
sudo bash install.sh --auto
```

## 📋 Prasyarat
- VPS Ubuntu 20.04+ / Debian 11+ (minimal 2 GB RAM direkomendasikan).
- Akses root (`sudo -i`).
- **Domain** dengan A record mengarah ke IP VPS (wajib untuk SSL & mode auto).
- Port terbuka: 22, 80, 443, 8080, 2022.

## ⚙️ Opsi
| Argumen | Arti |
|---|---|
| `--auto` / `-a` | Mode otomatis (cuma tanya domain) |
| `--manual` / `-m` | Mode manual (konfigurasi detail) |
| (tanpa arg) | Tanya pilih mode 1/2 |

## 🔐 Output di akhir
```
Panel    : https://panel.example.com
Admin    : admin@panel.example.com
Admin PW : <random>
DB root  : <random>
Node IP  : <ip>
```

## ⚠️ Catatan
- Script ini mengubah sistem (install paket, buat user DB, systemd). Jalankan di VPS fresh.
- SSL butuh DNS A record aktif & port 80 terbuka SEBELUM script jalan.
- Bukan affiliated resmi Pterodactyl. Credits: https://pterodactyl.io

## 📦 Publish ke npm
```bash
npm login
npm publish --access public
```
Lalu orang bisa `npx pterodactyl-installer`.

## 📦 Publish ke GitHub
```bash
git remote add origin https://github.com/your-username/pterodactyl-installer.git
git add -A && git commit -m "initial release" && git push -u origin main
```

MIT © your-name
