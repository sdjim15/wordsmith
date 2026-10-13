# ---- Étape 1 : compilation ----
FROM golang:1.23-alpine AS build
WORKDIR /src
COPY dispatcher.go .
# Binaire statique : il pourra tourner sur une image minimale sans libc
RUN CGO_ENABLED=0 go build -o dispatcher dispatcher.go

# ---- Étape 2 : exécution (le compilateur Go n'est plus nécessaire) ----
FROM alpine:3.20
WORKDIR /app
COPY --from=build /src/dispatcher .
# Le répertoire static doit se trouver dans le répertoire courant
COPY static ./static
EXPOSE 80
CMD ["./dispatcher"]
