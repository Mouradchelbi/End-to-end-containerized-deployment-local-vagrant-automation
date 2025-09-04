# Multi-stage Dockerfile for VProfile Application

# Build stage
FROM maven:3.9.9-eclipse-temurin-21-jammy AS BUILD_IMAGE

# Clone the project and build
RUN git clone https://github.com/hkhcoder/vprofile-project.git
RUN cd vprofile-project && git checkout containers && mvn install

# Runtime stage  
FROM tomcat:10-jdk21

# Remove default Tomcat applications
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the built WAR file from build stage
COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Set proper permissions for webapps directory
RUN chmod 755 /usr/local/tomcat/webapps/ && \
    chown -R root:root /usr/local/tomcat/webapps/

# Expose port 8080
EXPOSE 8080

# Start Tomcat
CMD ["catalina.sh", "run"]