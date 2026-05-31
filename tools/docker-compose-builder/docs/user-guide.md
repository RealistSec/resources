# Docker Compose Builder - User Guide

## Quick Start

1. **Load a Template**: Click one of the template buttons (LAMP Stack, MEAN Stack, etc.) to start with a pre-configured setup
2. **Add Services**: Fill in the service form and click "Add Service"
3. **Review Output**: The docker-compose.yml file is generated in real-time
4. **Validate**: Click "Validate Syntax" to check for issues
5. **Export**: Use "Copy to Clipboard" or "Download File" to save your configuration

## Adding Services

Each service requires:
- **Service Name**: Unique identifier (e.g., web, api, database)
- **Docker Image**: Image name and tag (e.g., nginx:alpine, postgres:15)

Optional fields:
- **Ports**: Map host to container ports (e.g., 8080:80)
- **Environment Variables**: One per line as KEY=value
- **Volumes**: Mount points (e.g., ./data:/var/lib/data)
- **Depends On**: Service dependencies (comma-separated)

## Templates

- **LAMP Stack**: Apache, PHP, MySQL
- **MEAN Stack**: MongoDB, Express/Node, Angular/Nginx
- **WordPress**: WordPress with MySQL database
- **Monitoring**: Prometheus, Grafana, Node Exporter

## Best Practices

- Use specific image tags (avoid :latest)
- Define restart policies for production
- Set resource limits
- Use named volumes for data persistence
- Configure health checks
