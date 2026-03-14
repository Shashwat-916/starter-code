# 🚀 Scribble Game Deployment Guide

Follow these steps to redeploy your application with the latest changes (Touch Support & Notification Fixes).

## Step 1: Build New Docker Images (Local Machine)

From your terminal in the root of the project (`d:\Game`), run these commands to build the latest version:

```powershell
# Build Backend
docker build -t scribblegame-backend ./backend

# Build Frontend
docker build -t scribblegame-frontend ./frontend
```

## Step 2: Tag and Push to Docker Hub

You need to push these to your Docker Hub account (`shashwat916`) so the server can pull them.

```powershell
# Login to Docker (if not already)
docker login

# Tag Images
docker tag scribblegame-backend shashwat916/scribblegame:backend
docker tag scribblegame-frontend shashwat916/scribblegame:frontend

# Push to Docker Hub
docker push shashwat916/scribblegame:backend
docker push shashwat916/scribblegame:frontend
```

## Step 3: Redeploy on the Server

On your production server (where the app is running), navigate to the directory containing `docker-compose.prod.yml` and run:

```bash
# Pull the latest images from Docker Hub
docker compose -f docker-compose.prod.yml pull

# Restart the containers with the new images
docker compose -f docker-compose.prod.yml up -d
```

---

### 💡 Troubleshooting

*   **Port Conflicts**: If the deployment fails due to a port being in use, check if another instance is running with `docker ps`.
*   **Environment Variables**: Ensure any `.env` files required by your backend are present on the production server.
*   **Network**: The `docker-compose.yml` uses a network called `scribble-net`. If you encounter network issues, try `docker network prune` and restart.

### 🧪 Local Testing (Before Pushing)
If you want to test the production build locally before pushing it:
```powershell
docker compose up --build
```
This will build and run everything in one step using the local code.
