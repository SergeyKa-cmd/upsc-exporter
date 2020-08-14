## Prometheus upsc exporter
------------
Service for send UPS metrics to collectors (such as Prometheus)

## How to use:
------------
1. Clone this repostitory to your ```/opt/``` directory and not elsewhere:
```sudo git clone https://github.com/SergeyKa-cmd/upsc-exporter.git```

-----------
2. Ensure correct WorkingDirectory path in ```upsc-exporter@.service``` file to cloned repository in ```/opt/upsc-exporter```:
```
[Service]
Type=simple
ExecStart=/bin/bash ./http_wrapper.sh upsc_exporter.sh
WorkingDirectory=/opt/upsc-exporter
StandardInput=socket
StandardError=journal
TimeoutStopSec=5
RuntimeMaxSec=10
```
-----------
3. Move ```upsc-exporter@.service``` and ```upsc-exporter.socket``` files to ```/etc/systemd/system/``` directory with sudo

-----------
4. Edit ```upsc_exporter.sh``` file and add name of your UPS in ```/etc/nut/ups.conf```(for example **myserver**):
```#!/bin/bash

# default to ups@localhost
UPS_TARGET="${UPS_TARGET:-myserver@localhost}"

upsc "$UPS_TARGET" | ./upsc_to_prometheus.awk
```
-----------
5. Add nologin user to bind upsc process to 

!!**Please be careful with chown command in root directory**:
```
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus
sudo chown -R prometheus:prometheus /opt/upsc-exporter/
ls -la /opt/upsc-exporter
``` 
-----------
6. Reload systemctl demon services and start upsc-exporter:
```
sudo systemctl daemon-reload
sudo systemctl enable upsc-exporter.socket
``` 
-----------
7. Ensure that your service is properly running:

```
sudo systemctl enable upsc-exporter.socket
```
or via get curl:
```
curl http://localhost:9102/metrics
```
