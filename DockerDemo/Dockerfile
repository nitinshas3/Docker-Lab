# Use OpenJDK 22 as base image
FROM openjdk:22-jdk

# Set working directory inside container
WORKDIR /app

# Add packaged JAR into container
ADD target/DockerDemo.jar app.jar

# Define startup command with ENTRYPOINT
ENTRYPOINT ["java", "-jar", "app.jar"]
