# Cloudron OpenVAS

Package Cloudron minimal pour exécuter OpenVAS/GVM.

## Prérequis

- Une instance Cloudron opérationnelle
- `cloudron` CLI installé localement
- Docker disponible sur la machine de build

## Structure du projet

- `CloudronManifest.json` : manifeste de l'app Cloudron
- `Dockerfile` : image OpenVAS (base `immauss/openvas:latest`)
- `icon.svg` : icône de l'application

## Build et installation sur Cloudron

Depuis ce dépôt :

```bash
cloudron build
cloudron install
```

## Accès et login OpenVAS

Sur les versions récentes des conteneurs communautaires OpenVAS, le mot de passe `admin` est généré aléatoirement au premier démarrage.

- Ouvre les logs de l’app Cloudron et récupère le mot de passe généré.
- Si nécessaire, teste `admin/admin`, puis change immédiatement le mot de passe.

### Récupérer le mot de passe admin (Cloudron)

1. Installe l’app et attends quelques minutes (initialisation des feeds).
2. Dans Cloudron Dashboard : **App OpenVAS -> Logs**.
3. Cherche les lignes contenant `admin`, `password`, `generated`.

En SSH sur le serveur Cloudron, fallback en CLI :

```bash
docker ps --format '{{.Names}}' | grep -i openvas
docker logs -f <nom_du_conteneur> | grep -Ei 'admin|password|generated'
```

Si rien ne sort immédiatement, relance sans `grep` pour lire tout le boot :

```bash
docker logs -f <nom_du_conteneur>
```

Puis essaie l’authentification :

- utilisateur : `admin`
- mot de passe : valeur trouvée dans les logs
- fallback : `admin/admin` (à changer immédiatement si ça passe)

## Référence Greenbone Community Container (hors Cloudron)

Si tu veux déployer la stack communautaire Greenbone directement via Docker Compose :

```bash
mkdir -p ~/greenbone-community-container
cd ~/greenbone-community-container
curl -f -O -L https://greenbone.github.io/docs/latest/_static/docker-compose.yml
docker compose pull
docker compose up -d
docker compose logs -f
```

La dernière commande permet de récupérer les informations de login générées au premier démarrage.