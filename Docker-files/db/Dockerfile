# MySQL Database Dockerfile for VProfile Application

FROM mysql:8.0.33

# Metadata labels
LABEL "Project"="Vprofile"
LABEL "Author"="Imran"

# Set MySQL environment variables
ENV MYSQL_ROOT_PASSWORD="vprodbpass"
ENV MYSQL_DATABASE="accounts"

# Copy database initialization script
ADD db_backup.sql /docker-entrypoint-initdb.d/db_backup.sql