#!/usr/bin/env bash
# ==============================================================================
#  Pterodactyl Installer — action based (install | panel | wings | remove*)
#  Usage:
#    bash <(curl -s https://pterodactyl-installer.se) [ACTION] [MODE]
#  Actions: install (default) | panel | wings | remove | remove-panel
#           remove-wings | remove-all | help
#  Modes : --auto / -a   (otomatis, cuma tanya domain)
#          --manual / -m (detail lengkap)
# ==============================================================================
set -uo pipefail

SCRIPT_VERSION="1.1.0"
PANEL_VER="1.11.8"
WINGS_VER="1.11.11"
MYSQL_ROOT=""
ADMIN_PW=""
NODE_TOKEN=""
CFG_FQDN="panel.localhost"; CFG_EMAIL=""; CFG_USER="admin"; CFG_DB_HOST="127.0.0.1"
CFG_DB_PORT="3306"; CFG_DB_NAME="panel"; CFG_DB_USER="pterodactyl"; CFG_LOCATION="Default"
CFG_WINGS_IP=""; CFG_TIMEZONE="$(cat /etc/timezone 2>/dev/null || echo UTC)"

# ---------- colors ----------
if [[ -t 1 ]]; then
  C_R=$'\033[0;31m'; C_G=$'\033[0;32m'; C_Y=$'\033[1;33m'; C_B=$'\033[0;34m'; C_C=$'\033[0;36m'; C_N=$'\033[0m'
else C_R="";C_G="";C_Y="";C_B="";C_C="";C_N=""; fi
log(){ printf "%s[•]%s %s\n" "$C_B" "$C_N" "$1"; }
ok(){  printf "%s[✓]%s %s\n" "$C_G" "$C_N" "$1"; }
warn(){ printf "%s[!]%s %s\n" "$C_Y" "$C_N" "$1"; }
err(){ printf "%s[✗]%s %s\n" "$C_R" "$C_N" "$1"; exit 1; }
spin(){ local pid=$1 msg="$2" marks="⠋⠙⠹⠸⠼⠴⠦⠧⠇⠏"
  while kill -0 "$pid" 2>/dev/null; do for m in ${marks}; do printf "\r%s%s%s %s" "$C_C" "$m" "$C_N" "$msg"; sleep 0.08; done; done
  printf "\r%b[✓]%b %s\n" "$C_G" "$C_N" "$msg"; }

[[ $EUID -eq 0 ]] || err "Jalankan sebagai root (sudo -i)."

# ---------- OS ----------
detect_os(){ . /etc/os-release 2>/dev/null || err "Gagal baca os-release"
  case "$ID" in ubuntu|debian) ;; *) err "OS tidak didukung: $ID (perlu Ubuntu/Debian)";; esac
  log "OS: $PRETTY_NAME"; }
pkg_update(){ log "Update apt…"; apt-get update -yqq >/dev/null 2>&1 || true; }
open_port(){ log "Pastikan port $1 terbuka (firewall/provider)."; }

# ---------- prompt ----------
prompt_line(){ local var="$1" txt="$2" def="$3"
  printf "%s%s%s [%s%s%s]: " "$C_Y" "$txt" "$C_N" "$C_C" "$def" "$C_N"; read -r val
  printf -v "$var" '%s' "${val:-$def}"; }

manual_config(){
  echo -e "${C_B}=== MODE MANUAL ===${C_N}"
  prompt_line CFG_FQDN "FQDN panel (domain)" "$CFG_FQDN"
  prompt_line CFG_EMAIL "Email admin" "admin@${CFG_FQDN}"
  prompt_line CFG_USER "Username admin" "$CFG_USER"
  prompt_line CFG_DB_HOST "Database host" "$CFG_DB_HOST"
  prompt_line CFG_DB_PORT "Database port" "$CFG_DB_PORT"
  prompt_line CFG_DB_NAME "Database name" "$CFG_DB_NAME"
  prompt_line CFG_DB_USER "Database user" "$CFG_DB_USER"
  prompt_line CFG_LOCATION "Nama location node" "$CFG_LOCATION"
  prompt_line CFG_TIMEZONE "Timezone" "$CFG_TIMEZONE"
  CFG_WINGS_IP="$(curl -s4 ifconfig.me 2>/dev/null || hostname -I | awk '{print $1}')"
  warn "Password DB & admin digenerate acak (aman)."
  echo
}
auto_config(){
  echo -e "${C_B}=== MODE AUTO ===${C_N}"
  warn "Otomatis, KECUALI FQDN (domain)."
  prompt_line CFG_FQDN "FQDN panel (domain WAJIB)" "panel.example.com"
  CFG_EMAIL="admin@${CFG_FQDN}"
  CFG_WINGS_IP="$(curl -s4 ifconfig.me 2>/dev/null || hostname -I | awk '{print $1}')"
  warn "Arahkan A record $CFG_FQDN → $CFG_WINGS_IP"
}

# ---------- install steps ----------
install_deps(){
  log "Install dependensi…"
  DEBIAN_FRONTEND=noninteractive apt-get install -yqq nginx mariadb-server redis-server \
    php-cli php-common php-bcmath php-gd php-mbstring php-mysql php-xml php-zip php-curl php-fpm \
    php-intl php-bz2 php-gmp certbot python3-certbot-nginx curl tar unzip git jq ca-certificates \
    gnupg lsb-release >/dev/null 2>&1 &
  spin $! "Install paket sistem"
  if ! command -v docker >/dev/null 2>&1; then curl -fsSL https://get.docker.com | bash >/dev/null 2>&1 & spin $! "Install Docker"; fi
  systemctl enable --now docker nginx mariadb redis-server >/dev/null 2>&1 || true
}
setup_mysql(){
  log "Setup MariaDB…"; systemctl start mariadb 2>/dev/null || true
  mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED VIA mysql_native_password USING PASSWORD('');" 2>/dev/null || true
  MYSQL_ROOT="$(openssl rand -hex 24)"
  mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '$MYSQL_ROOT';" 2>/dev/null || true
  mysql -e "CREATE DATABASE IF NOT EXISTS \`$CFG_DB_NAME\`;" 2>/dev/null || true
  mysql -e "CREATE USER IF NOT EXISTS '$CFG_DB_USER'@'$CFG_DB_HOST' IDENTIFIED BY '$MYSQL_ROOT';" 2>/dev/null || true
  mysql -e "GRANT ALL PRIVILEGES ON \`$CFG_DB_NAME\`.* TO '$CFG_DB_USER'@'$CFG_DB_HOST';" 2>/dev/null || true
  mysql -e "FLUSH PRIVILEGES;" 2>/dev/null || true
}
install_panel(){
  local dir="/var/www/pterodactyl"; log "Download Panel v$PANEL_VER…"
  mkdir -p "$dir" && cd "$dir"
  curl -fsSL "https://github.com/pterodactyl/panel/releases/download/v$PANEL_VER/panel.tar.gz" -o panel.tar.gz
  tar -xzf panel.tar.gz && rm panel.tar.gz
  curl -fsSL https://getcomposer.org/installer | php >/dev/null 2>&1
  php composer.phar install --no-dev --optimize-autoloader >/dev/null 2>&1 || true
  cp .env.example .env 2>/dev/null || true
}
configure_panel(){
  cd /var/www/pterodactyl
  chown -R www-data:www-data /var/www/pterodactyl
  php artisan key:generate --force >/dev/null 2>&1 || true
  APP_URL="https://$CFG_FQDN" php artisan p:environment:setup --author="$CFG_EMAIL" --url="https://$CFG_FQDN" \
    --timezone="$CFG_TIMEZONE" --cache="redis" --session="redis" --queue="redis" \
    --redis-host="127.0.0.1" --redis-pass="" --redis-port="6379" >/dev/null 2>&1 || true
  php artisan p:environment:database --host="$CFG_DB_HOST" --port="$CFG_DB_PORT" --database="$CFG_DB_NAME" \
    --username="$CFG_DB_USER" --password="$MYSQL_ROOT" >/dev/null 2>&1 || true
  php artisan migrate --force >/dev/null 2>&1 || true
  ADMIN_PW="$(openssl rand -hex 12)"
  php artisan p:user:make --admin=1 --name="$CFG_USER" --email="$CFG_EMAIL" --password="$ADMIN_PW" >/dev/null 2>&1 || true
  echo "$ADMIN_PW" > /root/pterodactyl_admin_password.txt
  (crontab -l 2>/dev/null; echo "* * * * * php /var/www/pterodactyl/artisan schedule:run >> /dev/null 2>&1") | crontab -
}
setup_nginx(){
  cat > /etc/nginx/sites-available/pterodactyl <<EOF
server {
    listen 80; server_name $CFG_FQDN; root /var/www/pterodactyl/public; index index.php;
    location / { try_files \$uri \$uri/ /index.php?\$query_string; }
    location ~ \.php\$ { include snippets/fastcgi-php.conf; fastcgi_pass unix:/run/php/php-fpm.sock; }
    location ~ /\.(?!well-known).* { deny all; }
}
EOF
  ln -sf /etc/nginx/sites-available/pterodactyl /etc/nginx/sites-enabled/pterodactyl
  rm -f /etc/nginx/sites-enabled/default
  nginx -t >/dev/null 2>&1 && systemctl reload nginx || warn "nginx -t gagal"
  if certbot --nginx -d "$CFG_FQDN" --non-interactive --agree-tos -m "$CFG_EMAIL" >/dev/null 2>&1; then ok "SSL aktif";
  else warn "Certbot gagal — pastikan DNS A record & port 80 terbuka."; fi
}
install_wings(){
  log "Install Wings v$WINGS_VER…"; mkdir -p /etc/pterodactyl
  curl -fsSL "https://github.com/pterodactyl/wings/releases/download/v$WINGS_VER/wings-linux-$(uname -m | sed 's/x86_64/amd64/;s/aarch64/arm64/')" -o /usr/local/bin/wings
  chmod +x /usr/local/bin/wings
  NODE_TOKEN="$(openssl rand -hex 32)"
  if [[ -d /var/www/pterodactyl ]]; then
    cd /var/www/pterodactyl
    local t; t="$(php artisan p:node:create "$CFG_LOCATION" --fqdn="$CFG_WINGS_IP" --public --no-tls \
      --daemonBase=/var/lib/pterodactyl/volumes --max-memory=0 --max-disk=0 --max-io=500 --location-id=1 \
      2>/dev/null | grep -oE 'node[0-9]+_token_[A-Za-z0-9]+' | head -1 || true)"
    [[ -n "$t" ]] && NODE_TOKEN="$t"
  else warn "Panel lokal tidak ditemukan — generate token acak. Tambah node manual di panel."; fi
}
write_wings_config(){
  cat > /etc/pterodactyl/config.yml <<EOF
debug: false
uuid: "$(openssl rand -hex 8)"
token_id: "$(openssl rand -hex 8)"
token_key: "$(openssl rand -hex 24)"
api: { host: "0.0.0.0", port: 8080, ssl: { enabled: false } }
system:
  data: "/var/lib/pterodactyl"
  sftp: { bind: "0.0.0.0", port: 2022, keys: { path: "/etc/pterodactyl/keys", generated: "ssh_host_ed25519_key", public: "ssh_host_ed25519_key.pub" } }
  docker: { network: "pterodactyl_nw", registries: {} }
nodes:
  - name: "$CFG_LOCATION"
    token: "$NODE_TOKEN"
    fqdn: "$CFG_WINGS_IP"
    daemon_base: "/var/lib/pterodactyl/volumes"
EOF
}
start_wings(){
  cat > /etc/systemd/system/wings.service <<'EOF'
[Unit]
Description=Pterodactyl Wings Daemon
After=docker.service
Requires=docker.service
[Service]
User=root
WorkingDirectory=/etc/pterodactyl
ExecStart=/usr/local/bin/wings
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
  systemctl daemon-reload && systemctl enable --now wings >/dev/null 2>&1 || warn "Gagal start wings — cek 'journalctl -u wings'"
  open_port 8080; open_port 2022
}

# ---------- flow ----------
do_deps_mysql(){ detect_os; pkg_update; install_deps; setup_mysql; }
do_panel(){ install_panel; configure_panel; setup_nginx; }
do_wings(){ install_wings; write_wings_config; start_wings; }
finale(){
  echo -e "\n${C_G}========================================${C_N}"
  echo -e "${C_G}  Pterodactyl terpasang! 🎉${C_N}"
  echo -e "${C_G}========================================${C_N}"
  echo -e "  Panel    : ${C_C}https://$CFG_FQDN${C_N}"
  echo -e "  Admin    : ${C_Y}$CFG_EMAIL${C_N}"
  echo -e "  Admin PW : ${C_Y}${ADMIN_PW:-<lihat /root/pterodactyl_admin_password.txt>}${C_N}"
  echo -e "  DB root  : ${C_Y}$MYSQL_ROOT${C_N}"
  echo -e "  Node IP  : ${C_Y}$CFG_WINGS_IP${C_N}"
  echo -e "${C_G}========================================${C_N}"
}

# ---------- removal ----------
remove_panel(){
  log "Hapus Panel…"
  rm -f /etc/nginx/sites-enabled/pterodactyl /etc/nginx/sites-available/pterodactyl
  rm -rf /var/www/pterodactyl
  crontab -l 2>/dev/null | grep -v "pterodactyl/artisan schedule:run" | crontab - || true
  mysql -e "DROP DATABASE IF EXISTS \`$CFG_DB_NAME\`;" 2>/dev/null || true
  mysql -e "DROP USER IF EXISTS '$CFG_DB_USER'@'$CFG_DB_HOST';" 2>/dev/null || true
  rm -f /root/pterodactyl_admin_password.txt
  systemctl reload nginx 2>/dev/null || true
  ok "Panel dihapus."
}
remove_wings(){
  log "Hapus Wings…"
  systemctl stop wings 2>/dev/null || true
  systemctl disable wings 2>/dev/null || true
  rm -f /usr/local/bin/wings /etc/systemd/system/wings.service
  rm -rf /etc/pterodactyl /var/lib/pterodactyl
  systemctl daemon-reload 2>/dev/null || true
  ok "Wings dihapus (volume docker tidak dihapus)."
}

# ---------- UI ----------
show_help(){
  cat <<EOF
${C_B}Pterodactyl Installer v$SCRIPT_VERSION${C_N}
${C_Y}Penggunaan:${C_N}
  bash <(curl -s https://pterodactyl-installer.vlyzer.app)            # menu interaktif
  bash <(curl -s https://pterodactyl-installer.vlyzer.app) [AKSI] [MODE]

${C_Y}Aksi:${C_N}
  install / both   Panel + Wings
  panel            HANYA Panel
  wings            HANYA Wings (auto-link ke panel lokal jika ada)
  remove-panel     Hapus Panel saja
  remove-wings     Hapus Wings saja
  remove-all       Hapus keduanya
  help             Bantuan ini

${C_Y}Mode:${C_N}
  --auto  / -a     Otomatis (cuma tanya domain)
  --manual / -m    Manual (tanya detail lengkap)

${C_Y}Menu interaktif (tanpa argumen):${C_N}
  Tiap aksi punya opsi [AUTO] & [MANUAL] — gak perlu pakai flag -a/-m.

${C_Y}Contoh:${C_N}
  bash <(curl -s https://pterodactyl-installer.vlyzer.app)
  bash <(curl -s https://pterodactyl-installer.vlyzer.app) wings --manual

${C_C}made with ❤ for self-hosters${C_N}
EOF
}

# menu interaktif — auto & manual jadi pilihan langsung
show_menu(){
  local options=(
    "Install the panel [AUTO]"
    "Install the panel [MANUAL]"
    "Install Wings [AUTO]"
    "Install Wings [MANUAL]"
    "Install panel + Wings [AUTO] (same machine)"
    "Install panel + Wings [MANUAL] (same machine)"
    "Uninstall the panel"
    "Uninstall Wings"
    "Uninstall panel + Wings"
  )
  local actions=(
    "panel auto"
    "panel manual"
    "wings auto"
    "wings manual"
    "both auto"
    "both manual"
    "remove-panel"
    "remove-wings"
    "remove-all"
  )
  local n=${#options[@]}
  echo -e "\n${C_B}Pterodactyl Installer v$SCRIPT_VERSION${C_N}"
  for i in "${!options[@]}"; do echo -e "  ${C_Y}[$i]${C_N} ${options[$i]}"; done
  echo -n -e "${C_B}* Input 0-$((n-1)): ${C_N}"; read -r idx
  [[ -z "$idx" ]] && err "Input diperlukan"
  if ! [[ "$idx" =~ ^[0-9]+$ ]] || (( idx < 0 || idx >= n )); then err "Opsi tidak valid: $idx"; fi
  IFS=" " read -r MENU_ACTION MENU_MODE <<<"${actions[$idx]}"
}

# ---------- main ----------
main(){
  local action="" mode=""
  for a in "$@"; do
    case "$a" in
      install|both|panel|wings|remove|remove-all|remove-panel|remove-wings|help|--help|-h) action="$a" ;;
      --auto|-a)  mode="auto" ;;
      --manual|-m) mode="manual" ;;
    esac
  done
  [[ "$action" == "help" ]] && { show_help; exit 0; }
  [[ "$action" == "remove" ]] && action="remove-all"
  [[ "$action" == "install" ]] && action="both"

  # menu interaktif kalau gak ada action (flag mode tanpa action -> default both)
  if [[ -z "$action" ]]; then
    show_menu; action="$MENU_ACTION"; mode="$MENU_MODE"
  fi
  # kalau action lewat flag tapi mode kosong -> default auto
  [[ -n "$action" && -z "$mode" ]] && mode="auto"

  # removal (tanpa prompt config)
  case "$action" in
    remove-panel) detect_os; remove_panel; exit 0 ;;
    remove-wings) detect_os; remove_wings; exit 0 ;;
    remove-all)   detect_os; remove_panel; remove_wings; ok "Semua Pterodactyl dihapus."; exit 0 ;;
  esac

  # install: prompt config sesuai mode
  [[ "$mode" == "auto" ]] && auto_config || manual_config

  case "$action" in
    panel)
      do_deps_mysql; do_panel
      echo -e "\n${C_G}✓ Panel terpasang: https://$CFG_FQDN${C_N}" ;;
    wings)
      install_deps; detect_os
      install_wings; write_wings_config; start_wings
      echo -e "\n${C_G}✓ Wings jalan di $CFG_WINGS_IP:8080${C_N}"
      [[ ! -d /var/www/pterodactyl ]] && warn "Tambah node manual di panel (token: ${NODE_TOKEN:0:16}…)" ;;
    both)
      do_deps_mysql; do_panel; do_wings; finale ;;
  esac
}
main "$@"
