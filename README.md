# Multi-Service Proxy

Reverse proxy setup for routing `reflectly.myroslavrepin.com` to the `reflectly` backend service via Nginx in Docker Compose.

## Prerequisites
- Docker and Docker Compose installed
- DNS `A` record for `reflectly.myroslavrepin.com` pointing to the host running this stack

## Stack
- `nginx`: Routes HTTP traffic on port 80 to backend services using config from `nginx/conf.d/nginx.conf`.
- `reflectly`: Placeholder HTTP service listening on port 8080 (currently using `hashicorp/http-echo`). Replace with your actual app image as needed.

## Usage
```bash
# Start services
docker compose up -d

# Check logs
docker compose logs -f nginx

# Verify routing (from a machine that resolves the domain to this host)
curl -H "Host: reflectly.myroslavrepin.com" http://localhost:80/

# Stop and remove containers
docker compose down
```

## Customization
- Update `nginx/conf.d/nginx.conf` to add more domains/services.
- Swap the `reflectly` service image/command in `docker-compose.yml` with your application that listens on port 8080.
- This setup is HTTP-only. For TLS, add a certificate management flow (e.g., Let's Encrypt companion) and listen on 443.
