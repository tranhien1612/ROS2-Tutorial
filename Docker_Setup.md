# Setup Docker

## Install linux on window with WSL
Open `Windows PowerShell` with `Administrator` and run command
```
wsl --install
```
After install wsl, restart system, then run command:
```
wsl --list --online
wsl --install Ubuntu-22.04
```
Run a specific wsl distribution from within PowerShell
```
wsl --distribution Ubuntu-22.04
```

After install Ubuntu, install Docker in Ubuntu:
```
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
newgrp docker
```

Create `Dockerfile` and `docker-compose.yaml`:
```
FROM osrf/ros:humble-desktop-full

# Install packages
RUN apt update && apt install -y \
    python3-colcon-common-extensions \
    && rm -rf /var/lib/apt/lists/*

# Create workspace
WORKDIR /home/ros2_ws
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

```
version: '3.8'

services:
  ros2:
    build: .
    container_name: ros2_dev
    network_mode: host
    stdin_open: true
    tty: true
    volumes:
      - ~/ros2_ws:~/ros2_ws
    environment:
      - DISPLAY=${DISPLAY}
      - QT_X11_NO_MITSHM=1
```

Run docker compose 
```
docker compose up -d
```

Enter docker:
```
docker exec -it ros2_dev bash
```
