# Inazuma Discord Theme

Programme sans interface web (bot, worker, tâche planifiée…), déployé sur le VPS.

Pour l'instant, `docker-compose.yml` lance un conteneur de démo. Remplace-le par ton programme :
chaque push sur `main` lance `~/infra/deploy/deploy.sh inazuma-discord-theme` sur le serveur
(`git pull`, `docker compose build`, `docker compose up -d`), quel que soit le contenu du compose.

## Exemples de Dockerfile

**Node.js** (bot Discord, worker…)

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev
COPY . .
USER node
CMD ["node", "index.js"]
```

**Python**

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
USER nobody
CMD ["python", "main.py"]
```

Puis dans `docker-compose.yml`, remplace `image:` et `command:` par `build: .`

## À garder

- `container_name: inazuma-discord-theme`, pour retrouver le conteneur facilement (`docker logs inazuma-discord-theme`)
- `restart: unless-stopped`, pour qu'il redémarre après un plantage ou un reboot du serveur
- les secrets dans le `.env` du serveur, jamais dans le repo

## Une base de données ?

Ajoute un service `db` comme dans le modèle Nuxt + Postgres (`vps-infra/templates/nuxt-postgres`),
sans lui mettre de `ports:`. Et si tu veux des migrations au déploiement, un service `migrate`
dans le profil `tools` est lancé automatiquement par `deploy.sh`.

## Logs

```bash
docker logs -f inazuma-discord-theme
```
