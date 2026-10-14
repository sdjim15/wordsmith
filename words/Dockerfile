# ---- Étape 1 : dépendances (ne dépend que de pom.xml) ----
FROM maven:3.9-eclipse-temurin-17-alpine AS deps
WORKDIR /src
COPY pom.xml .
# Le dépôt Maven local (~/.m2) est un cache conservé entre les builds
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B -q dependency:go-offline dependency:copy-dependencies \
        -DoutputDirectory=/deps

# ---- Étape 2 : JRE minimal (ne dépend que de pom.xml) ----
# Modifier le code ne relance donc pas jdeps/jlink : l'étape reste en cache
FROM deps AS jre
# jdeps calcule les modules utilisés par les bibliothèques ;
# on y ajoute ceux qu'utilise notre propre code (Main.java)
RUN MODULES=$(jdeps --ignore-missing-deps -q --multi-release 17 \
        --print-module-deps /deps/*.jar) \
 && echo "Modules : $MODULES" \
 && jlink --add-modules "$MODULES,java.sql,jdk.httpserver" \
        --strip-debug --no-man-pages --no-header-files --compress=2 \
        --output /jre

# ---- Étape 3 : compilation du code ----
FROM deps AS build
COPY src ./src
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B -q verify

# ---- Étape 4 : exécution (Alpine + JRE minimal) ----
FROM alpine:3.20
WORKDIR /app
# Les couches qui changent rarement d'abord, words.jar (notre code) en dernier
COPY --from=jre /jre /opt/jre
COPY --from=deps /deps ./dependency
COPY --from=build /src/target/words.jar .
EXPOSE 8080
CMD ["/opt/jre/bin/java", "-Xmx8m", "-Xms8m", "-jar", "words.jar"]
