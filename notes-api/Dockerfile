FROM eclipse-temurin:17-jdk AS build
WORKDIR /workspace
COPY . .
RUN ./gradlew bootJar -Pprod --no-daemon
RUN java -Djarmode=tools -jar build/libs/notes-api-0.0.1-SNAPSHOT.jar extract --destination /application

FROM eclipse-temurin:17-jre
WORKDIR /application
COPY --from=build /application ./
# Training run: start the app until the context refreshes, then save the loaded classes to a CDS archive
RUN java -XX:ArchiveClassesAtExit=app.jsa -Dspring.context.exit=onRefresh \
    -Dokta.oauth2.issuer=https://example.auth0.com/ -Dokta.oauth2.audience=training -Dokta.oauth2.client-id=training -Dokta.oauth2.client-secret=training \
    -Dspring.jpa.properties.hibernate.boot.allow_jdbc_metadata_access=false \
    -Dspring.jpa.hibernate.ddl-auto=none -Dspring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect \
    -jar notes-api-0.0.1-SNAPSHOT.jar
# Use only the C1 compiler: much faster startup on a small CPU, at some cost to peak throughput
ENTRYPOINT ["java", "-XX:SharedArchiveFile=app.jsa", "-XX:TieredStopAtLevel=1", "-jar", "notes-api-0.0.1-SNAPSHOT.jar"]
