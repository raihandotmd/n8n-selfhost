# Generate and use the self-signed cert:
```
mkdir certs
openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout certs/privkey.pem \
  -out certs/fullchain.pem \
  -subj "/CN=35.219.118.133"
```

# then start with:
```
docker-compose up -d
```