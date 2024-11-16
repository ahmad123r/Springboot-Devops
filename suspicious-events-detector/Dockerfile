# Use a Maven image to build the project
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /app

# Copy the pom.xml and source code
COPY pom.xml .
COPY src ./src

# Package the application
RUN mvn clean package -DskipTests

# Use a lightweight JDK image to run the application
FROM openjdk:17-jdk-slim
WORKDIR /app

# Copy the jar file from the build stage
COPY --from=build /app/target/*.jar app.jar

# Expose the application's port
EXPOSE 8080

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
