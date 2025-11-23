# Traefik Reverse Proxy Configuration

Ce dépôt contient la configuration Docker Compose pour déployer Traefik comme reverse proxy avec support HTTPS et Let's Encrypt.

## Prérequis

- Docker et Docker Compose installés
- Un nom de domaine configuré pointant vers votre serveur
- Accès aux ports 80 et 443

## Configuration

### 1. Créer le réseau Docker

Avant de lancer Traefik, créez le réseau externe nécessaire :

```bash
docker network create traefik-proxy
```

### 2. Configurer le domaine et l'email

Modifiez le fichier `docker-compose.yml` et remplacez :
- `bkeller@valaisweb.com` par votre adresse email pour Let's Encrypt
- `v1.valaisweb.com` par votre nom de domaine

### 3. Permissions du dossier ACME

Le dossier `acme` stocke les certificats SSL. Assurez-vous qu'il a les bonnes permissions :

```bash
mkdir -p acme
chmod 600 acme
```

## Utilisation

### Démarrer Traefik

```bash
docker-compose up -d
```

### Vérifier les logs

```bash
docker-compose logs -f traefik
```

### Arrêter Traefik

```bash
docker-compose down
```

## Accès au Dashboard

Le dashboard Traefik est accessible à l'adresse configurée dans les labels (par défaut : votre domaine configuré).

**Note** : Le mode `--api.insecure=true` est activé pour faciliter le développement. Pour la production, il est recommandé de sécuriser l'accès au dashboard avec une authentification.

## Structure du projet

- `docker-compose.yml` : Configuration principale de Traefik
- `acme/` : Dossier pour les certificats Let's Encrypt (ignoré par git)
- `dynamic/` : Configuration dynamique de Traefik (optionnel)

## Fonctionnalités

- ✅ Reverse proxy automatique avec détection Docker
- ✅ Certificats SSL automatiques avec Let's Encrypt
- ✅ Redirection HTTP vers HTTPS
- ✅ Dashboard de monitoring
- ✅ Support des configurations dynamiques

## Configuration de vos services

Pour exposer un service via Traefik, ajoutez les labels suivants à votre `docker-compose.yml` :

```yaml
services:
  mon-service:
    image: mon-image
    networks:
      - traefik-proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.mon-service.rule=Host(`mon-domaine.com`)"
      - "traefik.http.routers.mon-service.entrypoints=websecure"
      - "traefik.http.routers.mon-service.tls.certresolver=le"

networks:
  traefik-proxy:
    external: true
```

## Sécurité

- Les certificats sont stockés dans `acme/acme.json` et ne sont pas versionnés
- Le socket Docker est monté en lecture seule
- Seuls les services explicitement marqués (`exposedbydefault=false`) sont exposés

## Support et contribution

Pour toute question ou suggestion, n'hésitez pas à ouvrir une issue.
