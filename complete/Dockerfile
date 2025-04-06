# Dockerfile for gs-spring-boot

# ---- Stage 1: Build ----
# Use a JDK image that matches your project's Java version (e.g., 17)
FROM eclipse-temurin:17-jdk-jammy as builder

# Set working directory
WORKDIR /app

# Copy Maven wrapper files
COPY .mvn/ .mvn
COPY mvnw pom.xml ./

# Download Maven dependencies (caches layers)
# Using 'dependency:go-offline' is faster if it works, otherwise 'dependency:resolve'
RUN ./mvnw dependency:go-offline -Dmaven.repo.local=./.m2/repository
# If go-offline fails, try: RUN ./mvnw dependency:resolve

# Copy the source code
COPY src ./src

# Build the application JAR, skipping tests
# The JAR will be in /app/target/
RUN ./mvnw package -DskipTests -Dmaven.repo.local=./.m2/repository


# ---- Stage 2: Runtime ----
# Use a smaller JRE image for the final container
FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

# Copy only the built JAR from the builder stage
COPY --from=builder /app/target/*.jar app.jar

# Expose the port the application runs on
EXPOSE 8080

# Command to run the application
ENTRYPOINT ["java", "-jar", "/app/app.jar"]