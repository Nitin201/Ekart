# Use OpenJDK image
FROM openjdk:17-jdk

# Expose application port
EXPOSE 8070

# Set working directory
ENV APP_HOME=/usr/src/app
WORKDIR $APP_HOME

# Copy jar file into container
COPY target/shopping-cart-0.0.1-SNAPSHOT.jar app.jar

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
