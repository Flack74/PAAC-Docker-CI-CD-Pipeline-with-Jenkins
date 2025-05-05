# ========================= Base Build Stage =========================
FROM maven:3.9.9-eclipse-temurin-21-jammy AS BUILD_IMAGE

# Clone repository, checkout docker branch, and build the app
WORKDIR /app
RUN git clone https://github.com/hkhcoder/vprofile-project.git
WORKDIR /app/vprofile-project
RUN git checkout docker && mvn install -DskipTests

# ========================= Production Stage =========================
FROM tomcat:10-jdk21-slim

# Remove default webapps in Tomcat to clean up unnecessary files
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the WAR file from the build image to Tomcat's webapps directory
COPY --from=BUILD_IMAGE /app/vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Expose the port that Tomcat listens on
EXPOSE 8080

# Set the default command to run Tomcat
CMD ["catalina.sh", "run"]
