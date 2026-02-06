# Docker Usage Guide

## Building the Docker Image

### Basic Build

```bash
docker build -t vmw-migration-calc .
```

### Build with Custom Tag

```bash
docker build -t vmw-migration-calc:1.0.0 .
```

### Build with Registry

```bash
docker build -t myregistry.azurecr.io/vmw-migration-calc:latest .
```

## Running the Container

### Basic Execution

```bash
docker run -p 8080:80 vmw-migration-calc
```

Access the calculator at: `http://localhost:8080`

### Run in Background (Detached)

```bash
docker run -d -p 8080:80 --name migration-calc vmw-migration-calc
```

### Run with Volume Mounts

```bash
docker run -d -p 8080:80 \
  -v /var/log/nginx:/var/log/nginx \
  --name migration-calc vmw-migration-calc
```

### Run with Environment Variables

```bash
docker run -d -p 8080:80 \
  -e TZ=UTC \
  --name migration-calc vmw-migration-calc
```

## Container Management

### View Running Containers

```bash
docker ps
```

### View Logs

```bash
docker logs migration-calc
docker logs -f migration-calc  # Follow logs
```

### Stop Container

```bash
docker stop migration-calc
```

### Remove Container

```bash
docker rm migration-calc
```

### Remove Image

```bash
docker rmi vmw-migration-calc
```

## Image Information

- **Base Image**: nginx:alpine
- **Port**: 80
- **User**: nginx (UID: 101)
- **Entrypoint**: nginx -g daemon off;
- **Size**: ~50MB (alpine-based)

## Performance Tips

1. **Use Alpine Linux**: Already configured for small footprint
2. **Cache static assets**: Browser cache configured for 1 year
3. **Enable Gzip**: Compression enabled in nginx config
4. **Resource Limits**: Consider adding limits:

```bash
docker run -d -p 8080:80 \
  --memory=256m \
  --cpus=0.5 \
  --name migration-calc vmw-migration-calc
```

## Troubleshooting

### Port Already in Use

```bash
# Use different port
docker run -p 9000:80 vmw-migration-calc

# Or find what's using the port
lsof -i :8080
```

### Container Won't Start

```bash
# Check logs
docker logs <container-id>

# Run with interactive shell
docker run -it vmw-migration-calc sh
```

### Memory Issues

```bash
# Check docker resource usage
docker stats migration-calc

# Increase memory limit
docker run -d -p 8080:80 --memory=512m vmw-migration-calc
```

## Docker Compose

Create a `docker-compose.yml` for easier management:

```yaml
version: '3.8'

services:
  migration-calculator:
    build: .
    ports:
      - "8080:80"
    container_name: migration-calc
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    resources:
      limits:
        cpus: '0.5'
        memory: 256M
      reservations:
        cpus: '0.25'
        memory: 128M
```

Then run with:

```bash
docker-compose up -d
docker-compose down
```

## Publishing to Registry

### Docker Hub

```bash
docker tag vmw-migration-calc:latest myusername/vmw-migration-calc:latest
docker login
docker push myusername/vmw-migration-calc:latest
```

### Azure Container Registry (ACR)

```bash
docker tag vmw-migration-calc:latest myregistry.azurecr.io/vmw-migration-calc:latest
az acr login --name myregistry
docker push myregistry.azurecr.io/vmw-migration-calc:latest
```

## Security Considerations

The container includes security best practices:
- Runs as non-root user (nginx)
- Read-only root filesystem (in K8s deployment)
- No privileged capabilities
- Alpine base minimizes attack surface

For enhanced security, consider:
- Using a private registry
- Implementing network policies
- Adding authentication/authorization (nginx basic auth or OAuth proxy)
- Regular image scanning for vulnerabilities
