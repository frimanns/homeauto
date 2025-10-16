🏠 Home Automation Setup on Windows 11 with Docker 

📁 Folder Structure

```bash
homeauto/
├── docker-compose.yml
├── homeassistant/
└── mqtt/
    ├── config/
    │   └── mosquitto.conf
    ├── data/
    └── log/
```

⚙️ docker-compose.yml

```yaml
version: '3.9'

services:
  mqtt:
    image: eclipse-mosquitto:latest
    container_name: mqtt
    restart: unless-stopped
    ports:
      - "1883:1883"
    volumes:
      - ./mqtt/config:/mosquitto/config
      - ./mqtt/data:/mosquitto/data
      - ./mqtt/log:/mosquitto/log
    networks:
      - homeauto-net

  homeassistant:
    image: ghcr.io/home-assistant/home-assistant:stable
    container_name: homeassistant
    restart: unless-stopped
    ports:
      - "8123:8123"
    volumes:
      - ./homeassistant:/config
    environment:
      - TZ=Europe/Copenhagen
    networks:
      - homeauto-net

networks:
  homeauto-net:
    driver: bridge

```

🛰️ mosquitto.conf  
Located at mqtt/config/mosquitto.conf:

```bash
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
listener 1883
allow_anonymous true
```


🚀 Startup Commands

```bash
cd ~/homeauto
docker compose up -d
```

Check container status:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f homeassistant
```


🌐 Access URLs

- Home Assistant UI: http://localhost:8123
- MQTT Broker: localhost:1883

🧪 Home Assistant MQTT Integration

In the Home Assistant UI:

- Go to Settings → Devices & Services → Add Integration → MQTT
- If not auto-discovered, use:


- Broker: mqtt
- Port: 1883
- No username/password (anonymous access)

🧯 Troubleshooting Tips

- If Docker Desktop won’t launch:
- Kill background tasks in Task Manager
- Launch from C:\Program Files\Docker\Docker\Docker Desktop.exe
- If Home Assistant page fails:
- Try Incognito mode
- Restart stack: docker compose down && docker compose up -d

🗃️ Versioning Notes
- Archive this file alongside your Compose setup
- Consider adding .env support for ports and timezone
- Use Git to track changes and reuse across machines

