# 🛡️ Introduction à la Sécurité des Systèmes (GI2)

Travaux pratiques du module **Introduction à la Sécurité des Systèmes** (filière GI2).

**Enseignant :** Pr. Mohammed NASRI

Ce dépôt regroupe quatre ateliers couvrant les modèles formels de sécurité, les
modèles de contrôle d'accès applicatifs, et le durcissement (hardening) d'un
système Linux en situation offensive (Red Team) et défensive (Blue Team).

> ⚠️ **Avertissement.** Les applications Web et les machines cibles fournies dans
> ce dépôt sont **délibérément vulnérables** à des fins pédagogiques (mots de
> passe en clair, identifiants par défaut, absence de contrôle d'accès). Elles
> ne doivent **jamais** être exposées sur Internet ni déployées en production.
> N'utilisez les outils d'attaque (Lab 3 & 4) que contre les machines cibles
> prévues dans ces ateliers, dans votre propre environnement isolé.

---

## 📚 Contenu des ateliers

| Lab | Sujet | Environnement |
|-----|-------|---------------|
| [Lab 1](labs/lab1.md) | Modèles formels : **Bell-LaPadula** (confidentialité) et **Biba** (intégrité) | App Web PHP/MySQL ([`lab1-modeles`](labs/lab1-modeles)) |
| [Lab 2](labs/lab2.md) | Modèles de contrôle d'accès : **DAC, MAC, RBAC, ABAC** | App Web PHP/MySQL ([`lab2-controles-access`](labs/lab2-controles-access)) |
| [Lab 3](labs/lab3.md) | Préparation de l'environnement : Kali Linux + Metasploitable 2 | Docker / Machine virtuelle |
| [Lab 4](labs/lab4.md) | Audit, exploitation (Red Team) et durcissement (Blue Team) d'un serveur Linux | Kali + Metasploitable 2 |

---

## 🧰 Prérequis

- **Docker** et **Docker Compose** (labs 1 et 2, et option Docker du lab 3).
- Un hyperviseur : **VirtualBox** ou **VMware** (labs 3 et 4).
- Au moins **8 Go de RAM** (voir le [Lab 3](labs/lab3.md) pour le choix Docker vs VM
  selon votre configuration).

---

## 🚀 Démarrage rapide (Labs 1 & 2)

Chaque atelier applicatif se lance de façon autonome avec Docker Compose.

```bash
# Lab 1 — Modèles de Bell-LaPadula et Biba
cd labs/lab1-modeles
docker compose up -d
```

Une fois les conteneurs démarrés, les services sont accessibles sur :

| Service | URL | Identifiants |
|---------|-----|--------------|
| Application Web | http://localhost:8000 | voir `init.sql` (ex. `alice`, `bob`) |
| phpMyAdmin | http://localhost:8080 | utilisateur `root`, mot de passe `root` |
| MySQL | `localhost:3306` | base `tp_securite`, `root` / `root` |

Le code source de l'application se trouve dans `src/` et est monté en volume :
vos modifications sont prises en compte **sans reconstruire l'image**.

### Arrêter et réinitialiser

```bash
# Arrêter les conteneurs (les données sont conservées)
docker compose down

# Tout réinitialiser, y compris la base de données (ré-exécute init.sql)
docker compose down -v
```

> **Note.** Les labs 1 et 2 utilisent les **mêmes ports** (`8000`, `8080`,
> `3306`). Arrêtez un atelier (`docker compose down`) avant d'en démarrer un
> autre pour éviter un conflit de ports. Si le port `3306` est déjà utilisé par
> un MySQL local, arrêtez-le ou modifiez le mapping de port dans
> `docker-compose.yml`.

Pour le **Lab 2**, la procédure est identique depuis `labs/lab2-controles-access`.

---

## 🗂️ Structure du dépôt

```
.
├── README.md
└── labs/
    ├── lab1.md                     # Énoncé Lab 1
    ├── lab2.md                     # Énoncé Lab 2
    ├── lab3.md                     # Énoncé Lab 3
    ├── lab4.md                     # Énoncé Lab 4
    ├── lab1-modeles/               # Environnement applicatif Lab 1
    │   ├── docker-compose.yml
    │   ├── Dockerfile
    │   ├── init.sql                # Schéma + données de départ
    │   └── src/                    # Code PHP (monté en volume)
    └── lab2-controles-access/      # Environnement applicatif Lab 2
        ├── docker-compose.yml
        ├── Dockerfile
        ├── init.sql
        └── src/
```

---

## 🤝 Contribution

Les contributions (corrections, précisions dans les énoncés, améliorations des
environnements) sont les bienvenues via *pull request*. Merci de **ne pas
publier de corrigés** des exercices (implémentations de `security.php`, etc.)
dans ce dépôt public.
