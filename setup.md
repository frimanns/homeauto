🏠 Home Automation Setup on Windows 11 with Docker 


🧱 Install Docker Desktop for Windows
- Download from Docker's official site
- Enable WSL2 backend during installation
- Make sure Ubuntu is installed and set as the default WSL2 distro  


🔐 Why Docker Desktop Asks for Login  

Docker Desktop now requires users to sign in with a Docker Hub account to use it fully — especially for features like:
- Pulling images from Docker Hub
- Using Docker extensions
- Accessing Docker Desktop settings

But here's the good news:

✅ You Don’t Need a Password Yet  
Just Create an Account  
If you don’t already have a Docker Hub account:

- Go to https://hub.docker.com/signup
- Create a free account (you can skip organization setup)
- Once done, return to Docker Desktop and log in with your email/username + new password

This unlocks the full Docker Desktop experience — no need to pay for personal use.

🧭 After Login: Enable WSL2 Integration

Once you're in:
- Go to Settings → Resources → WSL Integration
- Enable Docker for your Ubuntu distro
- Then in Ubuntu WSL2, test with:

```bash
docker version
docker run hello-world
```

📁 Folder Structure

```bash
mkdir -p ~/homeauto/{mqtt/config,mqtt/data,mqtt/log,homeassistant}
cd ~/homeauto
```


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

🧭 Setup  MQTT Explorer

- A desktop client for monitoring MQTT topics and payloads in real time
- Shows a tree view of all topics
- Lets you publish, subscribe, and inspect messages
- Great for testing Home Assistant integrations and sensor data

💻 Install MQTT Explorer
- Go to mqtt-explorer.com
- Download the Windows installer (.exe)
- Install and launch the app

🔌 Connect to Your Local Broker

Use these settings:
- Host: localhost
- Port: 1883
- Protocol: mqtt://
- Username/Password: leave blank (you allowed anonymous access)
Then click Connect.

🧪 Try Publishing a Test Message
Once connected:
- Go to Publish
- Topic: test/topic
- Payload: {"hello": "world"}
- Click Publish
You’ll see it appear in the tree view instantly.

🧠 Bonus: Use with Home Assistant
- Home Assistant publishes to topics like homeassistant/sensor/...
- You can watch automations trigger in real time
- Great for debugging MQTT sensors or devices
