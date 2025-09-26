# Projet CI/CD – DEV & PROD automatisés (GitHub Actions + Ansible + Docker)

Ce dépôt est un squelette *clé en main* pour réaliser l'exercice : pipeline CI/CD complet déployant sur deux environnements (DEV/PROD) simulés par des conteneurs Docker équipés d'OpenSSH.

## 1) Prérequis locaux
- Git, Docker, Docker Compose, Ansible, Node.js >= 18
- Ports libres : 2222 (DEV) et 2200 (PROD)

## 2) Démarrer les serveurs DEV & PROD (locaux)
```bash
docker compose up -d
# Vérifier:
ssh root@localhost -p 2222  # mdp: devpassword
ssh root@localhost -p 2200  # mdp: prodpassword
```

## 3) Tester Ansible en local
```bash
ansible -i ansible/inventory/dev.ini dev -m ping -k
ansible -i ansible/inventory/prod.ini prod -m ping -k
```

## 4) Déploiement manuel (local)
```bash
ansible-playbook -i ansible/inventory/dev.ini ansible/playbooks/deploy-dev.yml -k
ansible-playbook -i ansible/inventory/prod.ini ansible/playbooks/deploy-prod.yml -k
```

> Un fichier `/var/www/html/index.html` sera installé sur les hôtes, indiquant la réussite et l'environnement de déploiement.

## 5) CI/CD avec GitHub Actions
1. Crée un dépôt GitHub et pousse ce code.
2. Dans **Settings > Secrets and variables > Actions > New repository secret**, ajoute (facultatif si tu utilises mots de passe locaux) :
   - `DEV_SSH_PASSWORD`, `PROD_SSH_PASSWORD` (si tu ne veux pas de mots de passe en clair en local)
3. Le workflow s'exécute sur `push` vers `dev` (déploiement DEV) et `main` (déploiement PROD).

> Par défaut l'inventaire est configuré pour du **localhost** (les conteneurs). En environnement réel, remplace `ansible_host`, `ansible_port`, etc. par tes VPS/VM et privilégie une **clé SSH** (et `ansible_ssh_private_key_file`).

## 6) Scripts utiles
```bash
# Lancer l'app Node localement (port 3000)
cd app && npm install && npm start
```

## 7) À adapter
- URL du dépôt dans le rôle Ansible (`git.repo`)
- Packages nécessaires (Node/Nginx, autres)
- Sécurisation via clés SSH (GitHub Secrets)
