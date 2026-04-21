# 🔵🟢 Blue-Green Deployment with Docker & Apache

Un projet de déploiement web utilisant la stratégie **Blue-Green Deployment** avec Docker, Docker Compose et Apache comme reverse proxy.

---

## 📋 Table des matières

- [Présentation](#présentation)
- [Architecture](#architecture)
- [Prérequis](#prérequis)
- [Structure du projet](#structure-du-projet)
- [Installation & Lancement](#installation--lancement)
- [Basculer entre Blue et Green](#basculer-entre-blue-et-green)
- [Ports exposés](#ports-exposés)
- [Technologies utilisées](#technologies-utilisées)

---

## 📖 Présentation

Le **Blue-Green Deployment** est une stratégie de déploiement qui consiste à maintenir deux environnements de production identiques (Blue et Green). À tout moment, un seul environnement est actif et reçoit le trafic utilisateur. L'autre est en attente, prêt à recevoir le trafic en cas de mise à jour ou de rollback instantané.

**Avantages :**
- Zéro downtime lors des déploiements
- Rollback instantané en cas de problème
- Environnements de test en production réelle

---

## 🏗️ Architecture

```
                        ┌─────────────────────┐
                        │   Apache Proxy       │
      Client ──────────▶│   (port 8080)        │
                        │   Router / Reverse   │
                        │   Proxy              │
                        └────────┬────────────-┘
                                 │
                   ┌─────────────┴─────────────┐
                   │                           │
          ┌────────▼────────┐        ┌─────────▼───────┐
          │   🔵 Blue Site  │        │  🟢 Green Site  │
          │   (port 8081)   │        │   (port 8082)   │
          │   Apache httpd  │        │   Apache httpd  │
          └─────────────────┘        └─────────────────┘
```

Le proxy Apache route tout le trafic entrant vers l'environnement actif (Blue ou Green) via `ProxyPass`.

---

## ✅ Prérequis

- [Docker](https://docs.docker.com/get-docker/) `>= 20.x`
- [Docker Compose](https://docs.docker.com/compose/install/) `>= 3.8`

---

## 📁 Structure du projet

```
blue-green-deployment/
│
├── proxy/
│   ├── Dockerfile              # Image Apache avec modules proxy activés
│   └── 000-default.conf        # Configuration du reverse proxy (routing)
│
├── blue-site/
│   ├── Dockerfile              # Image Apache pour le site Blue
│   └── index.html              # Page HTML du site Blue
│
├── green-site/
│   ├── Dockerfile              # Image Apache pour le site Green
│   └── index.html              # Page HTML du site Green
│
├── docker-compose.yml          # Orchestration des 3 services
└── README.md
```

---

## 🚀 Installation & Lancement

**1. Cloner le dépôt**

```bash
git clone https://github.com/votre-utilisateur/blue-green-deployment.git
cd blue-green-deployment
```

**2. Construire et démarrer les conteneurs**

```bash
docker-compose up --build -d
```

**3. Vérifier que les conteneurs tournent**

```bash
docker ps
```

**4. Accéder à l'application**

Ouvrir le navigateur et aller sur :

```
http://localhost:8080
```

---

## 🔀 Basculer entre Blue et Green

Le fichier `proxy/000-default.conf` contrôle vers quel environnement le trafic est routé.

**Environnement actif actuel : 🟢 Green**

```apache
ProxyPass / http://green-site:80/
ProxyPassReverse / http://green-site:80/
```

**Pour basculer vers 🔵 Blue**, modifier le fichier `proxy/000-default.conf` :

```apache
ProxyPass / http://blue-site:80/
ProxyPassReverse / http://blue-site:80/
```

Puis recharger la configuration Apache **sans redémarrer le conteneur** :

```bash
docker exec proxy apachectl graceful
```

> `apachectl graceful` recharge la configuration à chaud — les connexions en cours ne sont pas coupées, ce qui garantit un basculement **sans aucune interruption de service**.

---

## 🌐 Ports exposés

| Service       | Port local | Port conteneur | Description              |
|---------------|------------|----------------|--------------------------|
| `proxy`       | `8080`     | `80`           | Point d'entrée principal |
| `blue-site`   | `8081`     | `80`           | Site Blue (direct)       |
| `green-site`  | `8082`     | `80`           | Site Green (direct)      |

> Les sites Blue et Green sont également accessibles directement sur leurs ports respectifs pour les tests.

---

## 🛠️ Technologies utilisées

| Technologie      | Usage                              |
|------------------|------------------------------------|
| Docker           | Conteneurisation des services      |
| Docker Compose   | Orchestration multi-conteneurs     |
| Apache httpd 2.4 | Serveur web & reverse proxy        |
| HTML             | Pages web Blue & Green             |

---
