# Multi-Service Proxy

A Docker-based reverse proxy setup using Nginx to route traffic to multiple backend services based on subdomain/hostname.

## Architecture Flow Diagram

```
Client Browser
      │
      │ http://app1.localhost:8080
      │ http://app2.localhost:8080
      │
      ▼
   Port 8080 (Host)
      │
      ▼
┌─────────────────────────┐
│   Nginx Gateway :80     │
│  (gateway-nginx)        │
│                         │
│  Routes by server_name: │
│  • app1.localhost       │
│  • app2.localhost       │
└──────┬──────────┬───────┘
       │          │
       │          │
   ┌───▼───┐  ┌──▼────┐
   │ app_1 │  │ app_2 │
   │ :8000 │  │ :8000 │
   └───────┘  └───────┘
```

**Request Flow:**
1. Client → `http://app1.localhost:8080`
2. Nginx checks `Host` header
3. Routes to `app_1:8000` or `app_2:8000`
4. Response sent back through Nginx

## Prerequisites
- Docker and Docker Compose installed
- Hosts file configured (for local development)

## Local Development Setup

Add to your `/etc/hosts` file:
```
127.0.0.1 app1.localhost
127.0.0.1 app2.localhost
```

## Stack Components

- **nginx**: Reverse proxy routing HTTP traffic on port 8080 to backend services
  - Configuration: `nginx/nginx.conf` and `nginx/conf.d/*.conf`
  - Container: `gateway-nginx`
  - Port mapping: `8080:80` (host:container)

- **app_1**: Demo HTTP service
  - Image: `hashicorp/http-echo:0.2.3`
  - Container: `app_1`
  - Internal port: 8000
  - Response: "Hello from APP 1"

- **app_2**: Demo HTTP service
  - Image: `hashicorp/http-echo:0.2.3`
  - Container: `app_2`
  - Internal port: 8000
  - Response: "Hello from APP 2"

## Usage

### Start Services
```bash
docker compose up -d --build
```

### Check Status
```bash
docker compose ps
```

### View Logs
```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f nginx
docker compose logs -f app_1
```

### Test Endpoints

Using curl:
```bash
# Test app1
curl http://app1.localhost:8080/

# Test app2
curl http://app2.localhost:8080/

# Test with explicit Host header
curl -H "Host: app1.localhost" http://localhost:8080/
```

Using browser:
- http://app1.localhost:8080
- http://app2.localhost:8080

### Stop Services
```bash
docker compose down
```

### Rebuild After Changes
```bash
docker compose down
docker compose up -d --build
```

## Project Structure
```
multi-service-proxy/
├── docker-compose.yml          # Service orchestration
├── README.md                   # This file
├── app_1/
│   └── Dockerfile             # App 1 container definition
├── app_2/
│   └── Dockerfile             # App 2 container definition
└── nginx/
    ├── nginx.conf             # Main nginx configuration
    └── conf.d/
        ├── app1.conf          # App 1 virtual host config
        └── app2.conf          # App 2 virtual host config
```

## Adding New Services

1. Create new app directory:
```bash
mkdir app_3
```

2. Add Dockerfile in `app_3/Dockerfile`

3. Add service to `docker-compose.yml`:
```yaml
  app_3:
    build: ./app_3
    container_name: app_3
    expose:
      - "8000"
    networks:
      - backend
```

4. Create nginx config `nginx/conf.d/app3.conf`:
```nginx
server {
    listen 80;
    server_name app3.localhost;

    location / {
        proxy_pass http://app_3:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

5. Add to nginx dependencies in `docker-compose.yml`

6. Rebuild and restart:
```bash
docker compose up -d --build
```

## Troubleshooting

### Cannot connect to app1.localhost:8080

1. Check containers are running:
```bash
docker compose ps
```

2. Check nginx logs:
```bash
docker compose logs nginx
```

3. Verify hosts file:
```bash
cat /etc/hosts | grep localhost
```

4. Test with curl:
```bash
curl -v http://app1.localhost:8080/
```

### Connection refused

- Ensure Docker containers are running
- Check port 8080 is not used by another service:
```bash
lsof -i :8080
```

### 502 Bad Gateway

- Backend service is not running
- Check backend service logs:
```bash
docker compose logs app_1
```

## Production Deployment

For production use:

1. **Use real domain names** instead of `.localhost`
2. **Add TLS/SSL certificates** (Let's Encrypt)
3. **Update nginx config** to listen on port 443
4. **Add security headers**
5. **Implement rate limiting**
6. **Add monitoring and logging**

Example with SSL:
```nginx
server {
    listen 443 ssl http2;
    server_name app1.example.com;

    ssl_certificate /etc/nginx/ssl/cert.pem;
    ssl_certificate_key /etc/nginx/ssl/key.pem;

    # ... rest of config
}
```

## License

MIT
