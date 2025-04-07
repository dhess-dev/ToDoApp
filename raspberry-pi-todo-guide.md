# 📝 Raspberry Pi + Docker + .NET ToDo App — Complete Setup & Deployment Guide

## ✅ What we accomplished

### 🔧 1. Set up the Raspberry Pi
- Installed Raspberry Pi OS
- Enabled SSH for remote access
- Ensured the Pi was reachable via IP or `raspberrypi.local`

### 🐳 2. Installed Docker on the Pi
- Used the official install script:
  ```bash
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  ```
- Added your user to the Docker group:
  ```bash
  sudo usermod -aG docker dhess
  sudo reboot
  ```

### 🛠 3. Built your .NET app
- Used `dotnet publish` to build the app
- Created a Dockerfile in `src/ToDoApp/Dockerfile`
- Built a Docker image on the Pi:
  ```bash
  docker build -f src/ToDoApp/Dockerfile -t todoapp:local .
  ```

### 🚀 4. Ran the app using Docker
- Found the correct internal port using:
  ```bash
  docker logs todoapp
  ```
- Mapped correct port:
  ```bash
  docker run -d -p 8080:8080 --name todoapp todoapp:local
  ```
- Accessed the app at:
  ```
  http://raspberrypi.local/add-task-form
  ```

### 📦 5. Pushed to GitHub Container Registry
- Logged in:
  ```bash
  docker login ghcr.io
  ```
- Set up GitHub Actions workflow to:
  - Build a multi-arch Docker image
  - Push to `ghcr.io/dhess-dev/todoapp:latest`

### 🔐 6. Discussed Security
- Safe on trusted LAN
- Avahi is fine on home network
- No need for extra protection unless exposed to public internet

---

## 🔁 Manual Deployment & Update Guide (Current Setup)

### 📦 Minimal Compose Setup on Pi

Create a directory:
```bash
mkdir -p ~/todo-deploy
cd ~/todo-deploy
```

Create `docker-compose.yml`:

```yaml
services:
  todoapp:
    image: ghcr.io/dhess-dev/todoapp:latest
    ports:
      - "8080:8080"
    restart: unless-stopped
```

### 🚀 Start the App

```bash
docker compose up -d
```

### 🔄 Update Workflow (every time you push new code)

On your Pi:

```bash
cd ~/todo-deploy
docker compose pull && docker compose up -d
```

This will:
- Pull the latest image from GitHub
- Restart the container with the new image

### 🧹 Optional Cleanup

Free up space from unused images:

```bash
docker image prune -f
```

---

## ✅ Pro Tips

- Use `.local` hostname if Avahi is installed (`raspberrypi.local`)
- No need to clone the app on the Pi
- `docker compose` handles the update flow — no need to `docker stop` or `docker rm` manually
