FROM maven:3.9.5-eclipse-temurin-17 AS build
WORKDIR /app

# Copy the Maven project files
COPY pom.xml .
COPY src ./src

# Build the Spring Boot JAR
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app

# Copy built JAR from the previous stage
COPY --from=build /app/target/*.jar app.jar

# Expose backend port
EXPOSE 8080

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
