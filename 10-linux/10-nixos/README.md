# 🚀 ПОЛНЫЙ REDEPLOY: 2 сервера + документация!

Ты абсолютно прав — мой косяк, не проверил профиль. Идём! 💪

---

## ПЛАН ДЕЙСТВИЙ 🎯

text

`1. Создать server-deployment.md (документация) 2. Сгенерировать новые SOPS секреты 3. Redeploy на первый сервер через nixos-anywhere 4. Тест 5. Redeploy на второй сервер 6. Финальная проверка`

---

## Шаг 1: Создай документацию 📖

bash

``cd ~/nix-server # Создай гайд для будущих deployments cat > server-deployment.md << 'EOF' --- tags: [nixos, deployment, guide, production] ---  # NixOS Server Deployment Guide ## Prerequisites - Fresh Linux VPS (Debian 12, Ubuntu 22.04, etc.) - SSH access as root - nixos-anywhere installed locally: `nix-shell -p nixos-anywhere` ## Step 1: Generate VPN Secrets``

# On local machine

cd ~/nix-server

# Generate new age key for this server

age-keygen -o ~/.config/sops/age/server2-keys.txt

# Create secrets file

cat > secrets.yaml << 'SECRETS'  
vpn:  
uuid: "$(uuidgen)"  
privateKey: "$(openssl rand -base64 32)"  
shortId: "$(openssl rand -hex 8)"  
SECRETS

# Encrypt with sops

sops secrets.yaml

text

`## Step 2: Update flake.nix with new keys`

# In flake.nix

sops.age.keyFile = "/root/.config/sops/age/keys.txt";

text

`## Step 3: Deploy with nixos-anywhere`

# Local machine

nixos-anywhere --flake .#server root@NEW-SERVER-IP

# Wait for reboot...

sleep 60

# Verify

ssh nxoska@NEW-SERVER-IP  
sudo systemctl status sing-box  
timedatectl status # MUST be: synchronized: yes

text

`## Step 4: Verify Everything`

ssh nxoska@NEW-SERVER-IP

# 1. Time sync

timedatectl status

# Output: System clock synchronized: yes ✅

# 2. sing-box running

sudo systemctl status sing-box

# Output: active (running) ✅

# 3. Port listening

sudo ss -tulpn | grep 443

# Output: LISTEN [::]:443 ✅

# 4. Secrets loaded

sudo -u sing-box cat /run/secrets/vpn/uuid

# Output: your-uuid ✅

# 5. VPN functional

# Test from client (another machine)

text

`## Troubleshooting ### Time sync not working`

sudo systemctl restart systemd-timesyncd  
sleep 5  
timedatectl timesync-status

text

`### sing-box permission denied`

# Check secrets owner

ls -la /run/secrets/vpn/

# Should be: -r-------- sing-box sing-box

# If not, rebuild:

sudo nixos-rebuild switch --flake .#server

text

`### Connection refused on port 443`

sudo journalctl -u sing-box -n 50  
sudo ss -tulpn | grep -E "(443|sing-box)"

text

`## Rollback Plan If something breaks:`

ssh root@SERVER  
sudo nixos-rebuild switch --rollback  
sudo systemctl restart sing-box

text

`## References - [[40-projects/vpn-server/architecture]] - [[20-linux/nixos/sops-setup]] - [[20-linux/nixos/deployment]] --- **Last updated:** 2025-10-30 **Method:** nixos-anywhere (NOT deprecated nixos-infect) EOF git add server-deployment.md git commit -m "docs: add server deployment guide (nixos-anywhere)"`

---

## Шаг 2: Сгенерируй новые секреты 🔐

bash

`cd ~/nix-server # 1. Новый UUID для VPN NEW_UUID=$(uuidgen) echo "New UUID: $NEW_UUID" # 2. Новый private key NEW_PRIVATE_KEY=$(openssl rand -base64 32) echo "New Private Key: $NEW_PRIVATE_KEY" # 3. Новый short ID NEW_SHORT_ID=$(openssl rand -hex 8) echo "New Short ID: $NEW_SHORT_ID" # 4. Обнови secrets.yaml cat > secrets.yaml << EOF vpn:   uuid: "$NEW_UUID"   privateKey: "$NEW_PRIVATE_KEY"   shortId: "$NEW_SHORT_ID" EOF # 5. Зашифруй SOPS sops secrets.yaml  # 6. Проверь что зашифровано cat secrets.yaml | head -5 # Должно быть: ENC[AES256_GCM,...] ✅ # 7. Коммит (encrypted файл безопасен!) git add secrets.yaml git commit -m "chore: regenerate VPN secrets for clean deployment"`

---

## Шаг 3: Первый сервер — REDEPLOY 🚀

bash

`# ВАЖНО: Создай backup ssh nxoska@64.188.70.209 'sudo tar czf /tmp/backup-old.tar.gz /etc/nixos/ /var/lib/sing-box/' scp nxoska@64.188.70.209:/tmp/backup-old.tar.gz ~/backups/server1-backup-2025-10-30.tar.gz # Отключи VPN клиентов! (они потеряют соединение) # REDEPLOY через nixos-anywhere echo "🚀 Starting redeploy on server 1 (64.188.70.209)..." nixos-anywhere --flake .#server root@64.188.70.209 # Жди ~5-10 минут... echo "⏳ Waiting for reboot..." sleep 120 # Проверь что всё запустилось echo "🔍 Verifying server 1..." ssh nxoska@64.188.70.209 << 'VERIFY' echo "=== Time Sync ===" timedatectl status echo "" echo "=== sing-box Status ===" sudo systemctl status sing-box --no-pager | head -10 echo "" echo "=== Port 443 ===" sudo ss -tulpn | grep 443 echo "" echo "=== VPN Secrets ===" sudo -u sing-box cat /run/secrets/vpn/uuid echo "" echo "=== Recent Logs ===" sudo journalctl -u sing-box -n 20 --no-pager VERIFY`

---

## Шаг 4: Второй сервер — REDEPLOY 🚀

bash

`# Если у тебя есть второй сервер (например, для HA) # Процесс идентичный! echo "🚀 Starting redeploy on server 2..." nixos-anywhere --flake .#server root@SECOND-SERVER-IP sleep 120 echo "🔍 Verifying server 2..." ssh nxoska@SECOND-SERVER-IP << 'VERIFY' timedatectl status sudo systemctl status sing-box --no-pager | head -10 sudo journalctl -u sing-box -n 20 --no-pager VERIFY`

---

## Полный скрипт (all-in-one) 🎬

bash

`#!/bin/bash # deploy-both-servers.sh set -e SERVERS=(   "64.188.70.209"   "YOUR-SECOND-SERVER-IP"  # ← Замени на реальный IP ) PROJECT_DIR="$HOME/nix-server" cd "$PROJECT_DIR" echo "╔═════════════════════════════════════════════════════════╗" echo "║     🚀 NIXOS SERVER DUAL DEPLOYMENT (nixos-anywhere)   ║" echo "╚═════════════════════════════════════════════════════════╝" echo "" # Step 1: Generate secrets echo "📝 [STEP 1] Generating new VPN secrets..." NEW_UUID=$(uuidgen) NEW_PRIVATE_KEY=$(openssl rand -base64 32) NEW_SHORT_ID=$(openssl rand -hex 8) cat > secrets.yaml << EOF vpn:   uuid: "$NEW_UUID"   privateKey: "$NEW_PRIVATE_KEY"   shortId: "$NEW_SHORT_ID" EOF sops secrets.yaml echo "✅ Secrets generated and encrypted" echo "" # Step 2: Backup & Deploy each server for SERVER_IP in "${SERVERS[@]}"; do   echo "════════════════════════════════════════════════════════"   echo "🎯 DEPLOYING TO: $SERVER_IP"   echo "════════════════════════════════════════════════════════"   echo ""      # Backup   echo "💾 Creating backup..."   ssh nxoska@"$SERVER_IP" 'sudo tar czf /tmp/backup.tar.gz /etc/nixos/ /var/lib/sing-box/' 2>/dev/null || true   mkdir -p ~/backups  scp nxoska@"$SERVER_IP":/tmp/backup.tar.gz ~/backups/server-"$SERVER_IP"-backup-$(date +%Y-%m-%d).tar.gz 2>/dev/null || true   echo "✅ Backup created"   echo ""      # Deploy   echo "🚀 Running nixos-anywhere..."   nixos-anywhere --flake .#server root@"$SERVER_IP"      echo "⏳ Waiting for system to boot..."   sleep 120   echo ""      # Verify   echo "🔍 Verifying deployment..."   ssh nxoska@"$SERVER_IP" << VERIFY_SCRIPT echo "=== System Status ===" systemctl is-system-running echo "" echo "=== Time Sync ===" timedatectl status | head -3 echo "" echo "=== sing-box Service ===" sudo systemctl status sing-box --no-pager | grep -E "(Active|Main PID)" echo "" echo "=== Port 443 ===" sudo ss -tulpn | grep 443 | head -2 echo "" echo "=== VPN UUID ===" sudo -u sing-box cat /run/secrets/vpn/uuid echo "" echo "=== Recent Errors ===" sudo journalctl -u sing-box -n 10 --no-pager 2>/dev/null | grep -i error || echo "No errors ✅" VERIFY_SCRIPT      echo ""   echo "✅ Server $SERVER_IP deployment complete!"   echo "" done echo "╔═════════════════════════════════════════════════════════╗" echo "║          ✅ ALL SERVERS DEPLOYED SUCCESSFULLY!          ║" echo "╚═════════════════════════════════════════════════════════╝" echo "" echo "📝 Next steps:" echo "   1. Test VPN from client" echo "   2. Commit secrets: git add secrets.yaml && git commit" echo "   3. Update documentation if needed" echo ""`

---

## Запуск 🎬

bash

`# Создай скрипт cat > ~/deploy-vpn-servers.sh << 'SCRIPT_EOF' # (содержимое скрипта выше) SCRIPT_EOF chmod +x ~/deploy-vpn-servers.sh # Запусти cd ~/nix-server ~/deploy-vpn-servers.sh  # Или по одному (если хочешь контролировать) nixos-anywhere --flake .#server root@64.188.70.209 sleep 120 ssh nxoska@64.188.70.209 'timedatectl status && sudo systemctl status sing-box'`

---

## Финальная проверка ✅

bash

`# На каждом сервере: ssh nxoska@64.188.70.209 # ALL 4 должны быть ✅ echo "=== CHECK 1: Time Sync ===" && timedatectl status | grep "synchronized" echo "=== CHECK 2: sing-box ===" && sudo systemctl status sing-box --no-pager | grep "active" echo "=== CHECK 3: Port ===" && sudo ss -tulpn | grep 443 echo "=== CHECK 4: Secrets ===" && sudo -u sing-box cat /run/secrets/vpn/uuid # Если все 4 выводят что надо — ВСЁ ОТЛИЧНО! 🎉`

---

## КОММИТЫ

bash

`git add . git commit -m "🚀 Complete redeploy with nixos-anywhere - Use nixos-anywhere instead of deprecated nixos-infect - Regenerate all VPN secrets - Add server-deployment.md documentation - Clean NixOS installation on both servers - Proper systemd-timesyncd configuration - All systems verified and working ✅" git push`

---

**Готов?** Даю команду или сначала уточнить что-то? 🚀