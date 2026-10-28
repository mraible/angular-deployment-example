FROM eclipse-temurin:17-jdk AS build
WORKDIR /workspace
COPY . .
RUN ./gradlew bootJar -Pprod --no-daemon

FROM eclipse-temurin:17-jre
COPY --from=build /workspace/build/libs/notes-api-0.0.1-SNAPSHOT.jar /app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
