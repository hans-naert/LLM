# NVIDIA GPU Setup for WSL2 with Docker

## Prerequisites
1. Windows 11 or Windows 10 (21H2 or later)
2. NVIDIA GPU with latest drivers installed on Windows host
3. WSL2 installed and configured
4. Docker Desktop for Windows with WSL2 backend enabled

## Setup Steps

### 1. Verify NVIDIA GPU is available in WSL2
```bash
nvidia-smi
```
You should see your NVIDIA GPU listed. If not, update your NVIDIA drivers on Windows.

### 2. Install NVIDIA Container Toolkit in WSL2
```bash
# Add the NVIDIA repository
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

# Update and install
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

### 3. Configure Docker to use NVIDIA runtime
```bash
sudo nvidia-ctk runtime configure --runtime=docker
```

### 4. Restart Docker Desktop
- Restart Docker Desktop from Windows (right-click system tray icon > Restart)
- Or close and reopen Docker Desktop application

### 5. Recreate containers with GPU support
```bash
cd /home/u0081767/LLM
docker-compose -f "docker-compose ollama open-webui.yml" down
docker-compose -f "docker-compose ollama open-webui.yml" up -d
```

### 6. Verify GPU is accessible in container
```bash
docker exec ollama nvidia-smi
```

## Notes
- The docker-compose file already has GPU configuration in the `deploy.resources.reservations.devices` section
- No special startup needed after initial setup - just use normal docker-compose commands
- GPU will be automatically available each time containers start

pkill -SIGHUP com.docker.backend || echo "Restart Docker Desktop manually from the application menu"