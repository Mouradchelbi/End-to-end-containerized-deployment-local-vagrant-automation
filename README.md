
# VProfile Containerized Application

A complete multi-tier Java web application deployed using Docker containers with automated VM provisioning using Vagrant.

## Architecture Overview

This project implements a modern containerized deployment of the VProfile application using:

- **Web Tier**: Nginx reverse proxy
- **Application Tier**: Apache Tomcat 10 with Java 21
- **Database Tier**: MySQL 8.0.33
- **Cache Layer**: Memcached
- **Message Queue**: RabbitMQ with Management UI
- **Automation**: Vagrant with VirtualBox for development environment

## Project Structure

```
vprofile-project/
├── Docker-files/
│   ├── app/
│   │   └── Dockerfile           # Multi-stage build for Java application
│   ├── db/
│   │   ├── Dockerfile           # MySQL database container
│   │   └── db_backup.sql        # Database initialization script
│   └── web/
│       ├── Dockerfile           # Nginx reverse proxy
│       └── nginxvproapp.conf    # Nginx configuration
├── src/                         # Java application source code
├── vagrant/
│   └── Vagrantfile             # VM automation and provisioning
├── compose.yaml                # Docker Compose orchestration
├── pom.xml                     # Maven build configuration
└── README.md
```

## Prerequisites

- **VirtualBox** 6.1+ 
- **Vagrant** 2.3+
- **Git**
- **Host machine with 8GB+ RAM** (4GB allocated to VM)

## Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd vprofile-project
```

### 2. Install Vagrant Plugin

```bash
vagrant plugin install vagrant-hostmanager
```

### 3. Launch the Environment

```bash
cd vagrant
vagrant up
```

This single command will:
- Create an Ubuntu 22.04 VM with 4GB RAM
- Install Docker Engine and Docker Compose
- Build all container images
- Start the complete application stack
- Configure networking and port forwarding

### 4. Access the Application

**From Host Machine:**
- **Web Application**: http://localhost:8080
- **Direct App Access**: http://localhost:8081
- **RabbitMQ Management**: http://localhost:15672 (guest/guest)

**From VM Network:**
- **Web Application**: http://192.168.56.10
- **Database**: 192.168.56.10:3306 (admin/admin123)
- **Memcached**: 192.168.56.10:11211

## Container Services

### Web Service (vproweb)
- **Base Image**: nginx:latest
- **Function**: Reverse proxy and load balancer
- **Port**: 80
- **Configuration**: Custom nginx.conf for application routing

### Application Service (vproapp)
- **Base Image**: tomcat:10-jdk21
- **Build**: Multi-stage Maven build
- **Port**: 8080
- **Features**: 
  - Automated source code cloning and compilation
  - Proper file permissions for Tomcat deployment
  - Jakarta EE 9 compatibility

### Database Service (vprodb)
- **Base Image**: mysql:8.0.33
- **Port**: 3306
- **Features**:
  - Automatic database initialization
  - Persistent data storage
  - Pre-configured with application schema

### Cache Service (vprocache01)
- **Base Image**: memcached:latest
- **Port**: 11211
- **Function**: Application-level caching for improved performance

### Message Queue Service (vpromq01)
- **Base Image**: rabbitmq:management
- **Ports**: 5672 (AMQP), 15672 (Management UI)
- **Credentials**: guest/guest

## Development Workflow

### Manual Container Management

```bash
# SSH into the VM
vagrant ssh

# Navigate to project directory
cd ~/vprofile-project

# View running containers
docker ps

# View logs
docker logs vproapp
docker logs vprodb

# Restart specific service
docker-compose -f compose.yaml restart vproapp

# Rebuild after code changes
docker-compose -f compose.yaml build --no-cache vproapp
docker-compose -f compose.yaml up -d vproapp
```

### VM Management

```bash
# Start the VM
vagrant up

# Stop the VM
vagrant halt

# Restart the VM with fresh provisioning
vagrant reload --provision

# Destroy the VM completely
vagrant destroy -f

# SSH into the VM
vagrant ssh
```

## Configuration Details

### Network Configuration
- **VM IP**: 192.168.56.10
- **Port Forwarding**:
  - 80 (nginx) → 8080 (host)
  - 8080 (tomcat) → 8081 (host)
  - 3306 (mysql) → 3306 (host)
  - 11211 (memcached) → 11211 (host)
  - 5672 (rabbitmq) → 5672 (host)
  - 15672 (rabbitmq-mgmt) → 15672 (host)

### Database Configuration
- **Database**: accounts
- **Username**: admin
- **Password**: admin123
- **Tables**: Automatically created from db_backup.sql

### Application Features
- User registration and authentication
- Profile management
- File upload functionality
- Elasticsearch integration
- RabbitMQ messaging
- Memcached caching

## Troubleshooting

### Common Issues

**Container Build Failures:**
```bash
# Clean build with no cache
docker-compose -f compose.yaml build --no-cache

# Check build logs
docker-compose -f compose.yaml logs
```

**Permission Issues:**
```bash
# Fix file permissions in VM
sudo chown -R vagrant:vagrant ~/vprofile-project
```

**Port Conflicts:**
```bash
# Check port usage on host
netstat -tlnp | grep 8080
lsof -i :8080
```

**Memory Issues:**
```bash
# Check VM memory usage
free -h
docker stats
```

**Health Check Failures:**
- Memcached health checks may fail due to missing `nc` command
- Remove health check dependencies if needed
- Containers will function properly despite health check warnings

### Log Locations

**VM Logs:**
```bash
# Vagrant provisioning logs
vagrant up --debug

# Docker daemon logs
sudo journalctl -u docker.service
```

**Container Logs:**
```bash
# Application logs
docker logs vproapp --tail 50

# Database logs
docker logs vprodb

# All service logs
docker-compose -f compose.yaml logs
```

## Performance Optimization

### VM Resources
- **RAM**: 4GB (adjustable in Vagrantfile)
- **CPU**: 2 cores
- **Storage**: Uses host machine storage

### Container Resources
- Containers use shared VM resources
- Memcached provides application-level caching
- Database uses persistent volumes for data retention

## Production Considerations

This setup is designed for development and learning. For production deployment:

1. **Security**: Change default passwords and credentials
2. **SSL/TLS**: Configure HTTPS certificates
3. **Monitoring**: Add monitoring and logging solutions
4. **Backup**: Implement database backup strategies
5. **Scaling**: Consider container orchestration (Kubernetes, Docker Swarm)
6. **Resource Limits**: Set appropriate CPU and memory limits

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the changes with `vagrant destroy -f && vagrant up`
5. Submit a pull request

## Technologies Used

- **Containerization**: Docker, Docker Compose
- **Virtualization**: VirtualBox, Vagrant
- **Web Server**: Nginx
- **Application Server**: Apache Tomcat 10
- **Database**: MySQL 8.0.33
- **Caching**: Memcached
- **Message Queue**: RabbitMQ
- **Build Tool**: Maven
- **Java Version**: OpenJDK 21
- **Operating System**: Ubuntu 22.04 LTS

## License

This project is for educational and development purposes.

## Support

For issues and questions:
1. Check the troubleshooting section above
2. Review container logs for specific error messages
3. Ensure all prerequisites are properly installed
4. Verify system resources are sufficient
=======
# End-to-end-containerized-deployment-local-vagrant-automation
>>>>>>> 4d845a995870cbfe6b941cb7d635c8e3d6b97030
