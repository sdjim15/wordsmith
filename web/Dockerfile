# ---- Étape 1 : compilation ----
FROM golang:1.23-alpine AS build
WORKDIR /src
COPY dispatcher.go .
# CGO_ENABLED=0 : binaire statique, sans dépendance à une libc
# -ldflags "-s -w" : retire les symboles de débogage (~9 Mo -> ~6 Mo)
# Cache de compilation Go conservé entre deux builds : la bibliothèque
# standard (net/http...) n'est compilée qu'une fois, seul notre code est
# recompilé quand dispatcher.go change
RUN --mount=type=cache,target=/root/.cache/go-build \
    CGO_ENABLED=0 go build -ldflags="-s -w" -o dispatcher dispatcher.go

# ---- Étape 2 : exécution ----
# "scratch" est une image vide : on n'y met que static/ et le binaire
FROM scratch
WORKDIR /app
COPY static ./static
COPY --from=build /src/dispatcher .
EXPOSE 80
CMD ["./dispatcher"]
