#!/bin/bash
# Instalador SSHORIZON Manual
# Autor: Luiz FF

set -e

echo "[+] Limpando regras do iptables..."
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

echo "[+] Instalando dependências..."
apt update
apt install -y screen unzip wget curl jq cron

echo "[+] Configurando timezone..."
timedatectl set-timezone America/Sao_Paulo

# Detecta arquitetura
ARCH=$(uname -m)
if [[ "$ARCH" == "x86_64" ]]; then
    ARCH="x86_64"
else
    ARCH="aarch64"
fi
echo "[+] Arquitetura detectada: $ARCH"

# Criar diretórios
mkdir -p /opt/sshorizon/limiter /opt/sshorizon

echo "[+] Baixando binários..."
curl -fsSL "https://install.sshorizon.shop/manager/$ARCH/manager?install" -o /usr/local/bin/menu
curl -fsSL "https://install.sshorizon.shop/manager/$ARCH/scv2?install" -o /usr/local/bin/scv2
curl -fsSL "https://install.sshorizon.shop/manager/$ARCH/bot?install" -o /opt/sshorizon/bot
curl -fsSL "https://install.sshorizon.shop/manager/$ARCH/sshorizon-limiter?install" -o /opt/sshorizon/limiter/sshorizon-limiter

chmod +x /usr/local/bin/menu /usr/local/bin/scv2 /opt/sshorizon/bot /opt/sshorizon/limiter/sshorizon-limiter

echo "[+] Configurando cron jobs..."
(crontab -l 2>/dev/null; echo "* * * * * /usr/local/bin/menu --checkAndRemoveExpiredV2RayUsers") | crontab -
(crontab -l 2>/dev/null; echo "0 * * * * rm -f /var/log/v2ray/*.log") | crontab -
(crontab -l 2>/dev/null; echo "0 0 * * * /usr/bin/journalctl --vacuum-time=1s") | crontab -

echo -e "\n[✔] Instalação concluída!"
echo "[i] Use o comando 'menu' para abrir o SSHORIZON."
