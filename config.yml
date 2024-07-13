#!/bin/bash

# Переменные
SERVICE_FILE="/etc/systemd/system/prometheus.service"
PROMETHEUS_USER="prometheus"
PROMETHEUS_GROUP="prometheus"
PROMETHEUS_BIN="/usr/bin/prometheus"
PROMETHEUS_CONFIG="/etc/prometheus/prometheus.yml"
PROMETHEUS_DATA="/var/lib/prometheus/data"

# Проверка, установлен ли Prometheus
if [ ! -f "$PROMETHEUS_BIN" ]; then
    echo "Prometheus binary not found. Please install Prometheus before running this script."
    exit 1
fi

# Создаем пользователя и группу prometheus, если они не существуют
if ! id -u $PROMETHEUS_USER >/dev/null 2>&1; then
    echo "Creating user and group $PROMETHEUS_USER..."
    sudo useradd -M -r -s /bin/false $PROMETHEUS_USER
fi

# Создаем необходимые директории и устанавливаем права доступа
sudo mkdir -p $(dirname $PROMETHEUS_CONFIG)
sudo mkdir -p $PROMETHEUS_DATA
sudo chown -R $PROMETHEUS_USER:$PROMETHEUS_GROUP $(dirname $PROMETHEUS_CONFIG) $PROMETHEUS_DATA

# Создаем файл службы
echo "Creating Prometheus service file..."
sudo tee $SERVICE_FILE > /dev/null <<EOF
[Unit]
Description=Prometheus server
After=network-online.target

[Service]
User=$PROMETHEUS_USER
Group=$PROMETHEUS_GROUP
Type=simple
Restart=on-failure
ExecStart=$PROMETHEUS_BIN \\
        --config.file=$PROMETHEUS_CONFIG \\
        --storage.tsdb.path=$PROMETHEUS_DATA

[Install]
WantedBy=multi-user.target
EOF

# Перезагружаем конфигурацию systemd
echo "Reloading systemd daemon..."
sudo systemctl daemon-reload

# Запускаем и включаем службу Prometheus
echo "Starting and enabling Prometheus service..."
sudo systemctl start prometheus
sudo systemctl enable prometheus

# Проверяем статус службы
sudo systemctl status prometheus
