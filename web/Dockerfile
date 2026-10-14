# ---- Étape 1 : compilation ----
FROM golang:1.23-alpine AS build
WORKDIR /src
COPY dispatcher.go .
# CGO_ENABLED=0 : binaire statique, sans dépendance à une libc
# -ldflags "-s -w" : retire les symboles de débogage (~9 Mo -> ~6 Mo)
RUN CGO_ENABLED=0 go build -ldflags="-s -w" -o dispatcher dispatcher.go

# ---- Étape 2 : exécution ----
# "scratch" est une image vide : on n'y met que le binaire et static/
FROM scratch
WORKDIR /app
COPY --from=build /src/dispatcher .
COPY static ./static
EXPOSE 80
CMD ["./dispatcher"]
