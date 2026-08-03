# Workstation Setup Assignment

## 1. Project Overview
Custom Nginx web server environment set up using Docker, bind mounts, and volumes.

## 2. Key Configurations
- **Base Image**: Nginx (latest)
- **Port Mapping**: `8080:80`
- **Bind Mount**: Live reload setup for web assets
- **Data Persistence**: Docker volume attached for log/data retention

## 3. Environment
- OS / Subsystem: Windows 11 + WSL2 (Ubuntu)
- Container Engine: Docker / OrbStack
- Editor & Version Control: VSCode, Git & GitHub

## 4. Verification
- Verified local and container responses using `curl` and browser access at `http://localhost:8080`.
