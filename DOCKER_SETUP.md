# 🐳 Guide de Configuration Docker

Ce guide vous explique comment installer Docker et construire/publier votre image.

## 📦 Installation de Docker Desktop

### macOS (votre système actuel)

1. **Télécharger Docker Desktop pour Mac**
   - Visitez : https://www.docker.com/products/docker-desktop/
   - Cliquez sur "Download for Mac"
   - Choisissez la version correspondant à votre puce :
     - **Apple Silicon (M1/M2/M3)** : Docker Desktop for Mac with Apple silicon
     - **Intel** : Docker Desktop for Mac with Intel chip

2. **Installer**
   - Ouvrez le fichier `.dmg` téléchargé
   - Glissez Docker.app dans Applications
   - Lancez Docker Desktop depuis Applications
   - Acceptez les conditions et autorisez les permissions

3. **Vérifier l'installation**
   ```bash
   docker --version
   docker compose version
   ```

### Windows

1. Téléchargez Docker Desktop : https://www.docker.com/products/docker-desktop/
2. Lancez l'installateur
3. Suivez les instructions (WSL 2 sera installé si nécessaire)
4. Redémarrez votre ordinateur
5. Lancez Docker Desktop

### Linux (Ubuntu/Debian)

```bash
# Installer Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Ajouter votre utilisateur au groupe docker
sudo usermod -aG docker $USER

# Redémarrer la session ou exécuter
newgrp docker

# Vérifier
docker --version
```

---

## 🛠️ Construire l'image localement

### 1. Build de base
```bash
docker build -t mon-app-recettes:latest .
```

### 2. Tester localement
```bash
# Lancer le conteneur
docker run -p 8080:80 mon-app-recettes:latest

# Accédez à http://localhost:8080
```

### 3. Arrêter le conteneur
```bash
# Lister les conteneurs en cours
docker ps

# Arrêter un conteneur
docker stop <CONTAINER_ID>
```

---

## 📤 Publier sur GitHub Container Registry (automatique)

### Configuration (une seule fois)

Le workflow GitHub Actions est déjà configuré dans `.github/workflows/docker-publish.yml`

**Que fait ce workflow ?**
- ✅ Build automatique à chaque push sur `main`
- ✅ Publication sur GitHub Container Registry (ghcr.io)
- ✅ Tags automatiques (latest, version, sha)
- ✅ Plateforme linux/amd64
- ✅ Cache pour accélérer les builds

### Activer le workflow

1. **Rendez votre package public** (après le premier push) :
   - Allez sur GitHub → Votre repo → Packages
   - Cliquez sur votre image
   - Package settings → Change visibility → Public

2. **Push votre code**
   ```bash
   git add .
   git commit -m "Add Docker configuration"
   git push origin main
   ```

3. **Vérifier le build**
   - Allez sur GitHub → Actions
   - Vous verrez le workflow "Build and Push Docker Image" s'exécuter
   - Une fois terminé, votre image sera disponible sur `ghcr.io/VOTRE-USERNAME/VOTRE-REPO:latest`

### URL de votre image

Après le premier build réussi, votre image sera disponible à :
```
ghcr.io/<votre-username>/<nom-du-repo>:latest
```

Exemple :
```
ghcr.io/farestazi/architectures-web:latest
```

---

## 📤 Alternative : Publier sur DockerHub

Si vous préférez DockerHub au lieu de GitHub Container Registry :

### 1. Créer un compte DockerHub
- https://hub.docker.com/signup

### 2. Se connecter localement
```bash
docker login
# Entrez votre username et password DockerHub
```

### 3. Taguer et publier
```bash
# Taguer l'image
docker tag mon-app-recettes:latest VOTRE-USERNAME/mon-app-recettes:latest

# Publier
docker push VOTRE-USERNAME/mon-app-recettes:latest
```

### 4. Modifier le workflow pour DockerHub

Créez un secret GitHub `DOCKERHUB_TOKEN` et modifiez `.github/workflows/docker-publish.yml` pour utiliser DockerHub au lieu de ghcr.io.

---

## 🧪 Commandes utiles Docker

```bash
# Lister les images
docker images

# Supprimer une image
docker rmi <IMAGE_ID>

# Lister les conteneurs (tous)
docker ps -a

# Voir les logs d'un conteneur
docker logs <CONTAINER_ID>

# Nettoyer les images inutilisées
docker system prune -a

# Inspecter une image
docker inspect mon-app-recettes:latest

# Exécuter un shell dans le conteneur
docker run -it mon-app-recettes:latest sh
```

---

## ✅ Checklist pour le TD

- [ ] Docker Desktop installé et fonctionnel
- [ ] Image construite localement avec succès
- [ ] Conteneur testé sur http://localhost:8080
- [ ] Code pushé sur GitHub
- [ ] Workflow GitHub Actions exécuté avec succès
- [ ] Image publiée sur ghcr.io (ou DockerHub)
- [ ] Package rendu public sur GitHub
- [ ] URL de l'image partagée avec le professeur

---

## 🆘 Problèmes courants

### "Cannot connect to Docker daemon"
→ Docker Desktop n'est pas lancé. Démarrez l'application Docker Desktop.

### "denied: permission denied"
→ Vous n'êtes pas connecté au registry. Faites `docker login` ou vérifiez les permissions GitHub.

### "no space left on device"
→ Nettoyez les images inutilisées : `docker system prune -a`

### Le workflow GitHub Actions échoue
→ Vérifiez que le dépôt a les permissions "Read and write permissions" dans Settings → Actions → General → Workflow permissions.

---

## 📚 Ressources

- [Documentation Docker](https://docs.docker.com/)
- [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [Next.js Docker Documentation](https://nextjs.org/docs/deployment#docker-image)
