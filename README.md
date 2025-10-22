# n8n Self-Hosted Deployment Guide

## Prerequisites

1. **Docker and Docker Compose** installed on your machine
   ```bash
   # Check if Docker is installed
   docker --version
   docker compose version
   ```

2. **Open required ports** on your firewall:
   - Port 5678

## Deployment Steps

### 1. Generate SSL Certificates

Create a `certs` directory and generate self-signed certificates:

```bash
mkdir certs
openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout certs/privkey.pem \
  -out certs/fullchain.pem \
  -subj "/CN=35.219.118.133"
```

**Note:** For production environments, consider using Let's Encrypt certificates instead of self-signed certificates.

### 2. Start n8n

The `n8n_data` volume will be automatically created by Docker Compose to persist your workflows and data.

```bash
docker compose up -d
```

### 3. Verify the Deployment

Check if the container is running:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f n8n
```

### 4. Access n8n

Open your browser and navigate to:
```
https://35.219.118.133:5678
```

**Note:** If using self-signed certificates, your browser will show a security warning. Click "Advanced" and proceed to continue.

## Managing the Service

### Stop n8n
```bash
docker compose down
```

### Stop and remove volumes (⚠️ deletes all data)
```bash
docker compose down -v
```

### Update n8n
```bash
docker compose pull
docker compose up -d
```

### Restart n8n
```bash
docker compose restart
```

### View logs
```bash
docker compose logs -f n8n
```

## Configuration

The n8n instance is configured with:
- **Timezone:** Asia/Jakarta
- **Protocol:** HTTPS
- **Host:** 35.219.118.133
- **Port:** 5678
- **Data persistence:** Docker volume `n8n_data`

## Troubleshooting

### Container won't start
- Ensure SSL certificates exist in `./certs/` directory
- Check if port 5678 is available: `sudo netstat -tuln | grep 5678`
- View logs: `docker compose logs n8n`

### Cannot access n8n
- Verify firewall allows port 5678
- Check container is running: `docker compose ps`
- Ensure you're using HTTPS: `https://35.219.118.133:5678`
- Accept the browser security warning if using self-signed certificates