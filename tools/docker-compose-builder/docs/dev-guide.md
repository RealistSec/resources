# Docker Compose Builder - Developer Guide

## Architecture

Single-file HTML application with vanilla JavaScript. No external dependencies.

## Key Functions

### Service Management
- `addService()`: Adds a new service to the configuration
- `removeService(name)`: Removes a service
- `render()`: Updates UI and YAML output

### YAML Generation
- `generateYAML()`: Converts services object to YAML format
- Handles ports, environment variables, volumes, and dependencies
- Automatically detects and defines named volumes

### Validation
- `validateOutput(yaml)`: Checks for common issues
- Detects missing dependencies
- Warns about :latest tags
- Provides production readiness checklist

### Templates
- Pre-defined service configurations
- Easy to extend in `templates` object
- Covers common application stacks

## Extending

Add new templates by extending the `templates` object:

```javascript
templates.mystack = [
  {name: 'service1', image: 'image:tag', ports: '8080:80'},
  {name: 'service2', image: 'image:tag', dependsOn: 'service1'}
];
```

## Features Added

1. **Template System**: Four pre-built stack templates
2. **Visual Service List**: See all added services at a glance
3. **Dependency Validation**: Checks if dependent services exist
4. **Production Checklist**: Best practice recommendations
5. **Multiple Export Options**: Copy, download, or validate
6. **Named Volume Detection**: Automatically defines Docker volumes
