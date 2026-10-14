# ---- Étape 1 : compilation + JRE minimal (Alpine, JDK 17) ----
FROM maven:3.9-eclipse-temurin-17-alpine AS build
WORKDIR /src
# Le pom d'abord : les dépendances restent en cache tant qu'il ne change pas
COPY pom.xml .
RUN mvn -B dependency:go-offline
COPY src ./src
RUN mvn -B verify

# jdeps liste les modules Java réellement utilisés par l'appli et ses
# bibliothèques ; jlink fabrique un JRE qui ne contient que ceux-là
RUN MODULES=$(jdeps --ignore-missing-deps -q --multi-release 17 \
        --print-module-deps target/words.jar target/dependency/*.jar) \
 && echo "Modules : $MODULES" \
 && jlink --add-modules "$MODULES" \
        --strip-debug --no-man-pages --no-header-files --compress=2 \
        --output /jre

# ---- Étape 2 : exécution (Alpine + JRE minimal) ----
FROM alpine:3.20
WORKDIR /app
COPY --from=build /jre /opt/jre
COPY --from=build /src/target/words.jar .
# words.jar charge ses bibliothèques depuis le dossier dependency/
COPY --from=build /src/target/dependency ./dependency
EXPOSE 8080
CMD ["/opt/jre/bin/java", "-Xmx8m", "-Xms8m", "-jar", "words.jar"]
