FROM postgres:16-alpine
# Pour le TP : connexions autorisées sans mot de passe
ENV POSTGRES_HOST_AUTH_METHOD=trust
# Les scripts de ce répertoire sont exécutés au premier démarrage
# (base vide) pour créer les tables et insérer les données
COPY words.sql /docker-entrypoint-initdb.d/
EXPOSE 5432
