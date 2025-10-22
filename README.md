# n8n Self-Hosted Deployment Guide

## Prerequisites

1. **Docker and Docker Compose** installed on your machine
   ```bash
   # Check if Docker is installed
   docker --version
   docker compose version
   ```

2. **Open required ports** on your firewall:
   - Port 80 (HTTP - for redirect to HTTPS)
   - Port 443 (HTTPS)

## Deployment Steps

### 1. Generate SSL Certificates

Create a `certs` directory and generate self-signed certificates:

```bash
mkdir -p certs
openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout certs/privkey.pem \
  -out certs/fullchain.pem \
  -subj "/CN=35.219.118.133"
chmod 644 certs/*.pem
```

**Note:** For production environments, consider using Let's Encrypt certificates instead of self-signed certificates.

### 2. Start n8n with Nginx

The setup includes:
- **Nginx** as a reverse proxy handling HTTPS
- **n8n** running behind Nginx
- Automatic HTTP to HTTPS redirect
- The `n8n_data` volume for persisting workflows and data

```bash
docker compose up -d
```

### 3. Verify the Deployment

Check if containers are running:

```bash
docker compose ps
```

View logs:

```bash
# View all logs
docker compose logs -f

# View n8n logs only
docker compose logs -f n8n

# View nginx logs only
docker compose logs -f nginx
```

### 4. Access n8n

Open your browser and navigate to:
```
https://35.219.118.133
```

Or use HTTP (will automatically redirect to HTTPS):
```
http://35.219.118.133
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

### Restart services
```bash
# Restart all services
docker compose restart

# Restart specific service
docker compose restart n8n
docker compose restart nginx
```

### View logs
```bash
docker compose logs -f n8n
```

## Configuration

The n8n instance is configured with:
- **Timezone:** Asia/Jakarta
- **Protocol:** HTTPS (handled by Nginx reverse proxy)
- **Host:** 35.219.118.133
- **Port:** 443 (external), 5678 (internal)
- **Data persistence:** Docker volume `n8n_data`
- **Reverse Proxy:** Nginx with SSL termination

## Architecture

```
Browser → Nginx (Port 80/443) → n8n (Port 5678)
          [SSL/TLS handled here]
```

## Troubleshooting

### Containers won't start
- Ensure SSL certificates exist in `./certs/` directory
- Check if ports 80 and 443 are available: `sudo netstat -tuln | grep -E '80|443'`
- View logs: `docker compose logs`

### Cannot access n8n
- Verify firewall allows ports 80 and 443
- Check containers are running: `docker compose ps`
- Ensure you're using HTTPS: `https://35.219.118.133`
- Accept the browser security warning if using self-signed certificates

### Nginx errors
- Check nginx configuration: `docker compose exec nginx nginx -t`
- View nginx logs: `docker compose logs nginx`

### n8n connection issues
- Verify n8n is accessible from nginx: `docker compose exec nginx wget -O- http://n8n:5678`
- Check n8n logs: `docker compose logs n8n`