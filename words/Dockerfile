# ---- Étape 1 : compilation avec Maven + JDK 17 ----
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /src
# Copier le pom seul d'abord : les dépendances restent en cache
# tant que le pom ne change pas
COPY pom.xml .
RUN mvn -B dependency:go-offline
COPY src ./src
RUN mvn -B verify

# ---- Étape 2 : exécution avec le JRE seulement ----
FROM eclipse-temurin:17-jre
WORKDIR /app
# words.jar référence ses bibliothèques via le classpath "dependency/"
COPY --from=build /src/target/words.jar .
COPY --from=build /src/target/dependency ./dependency
EXPOSE 8080
CMD ["java", "-Xmx8m", "-Xms8m", "-jar", "words.jar"]
