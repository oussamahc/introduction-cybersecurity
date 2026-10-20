# Option C (proposition) — Environnement Docker pour les Labs 3 & 4

> 🧪 **Statut : proposition à valider par l'enseignant.** Ce dossier offre une
> alternative tout-Docker à la procédure VM décrite dans [`../lab3.md`](../lab3.md)
> et [`../lab4.md`](../lab4.md). Les énoncés restent la référence officielle ;
> ceci est un confort de déploiement, pas un remplacement validé.

## Pourquoi

La mise en place manuelle (Kali + Metasploitable 2 sous VirtualBox/VMware,
configuration réseau pont/hôte) est longue et sensible aux erreurs. Un
`docker compose up` démarre les deux machines sur un **réseau Docker isolé**, où
la cible vulnérable n'est **jamais** exposée à l'hôte ni au réseau local.

```
┌─────────────────── atelier-net (réseau Docker interne) ───────────────────┐
│                                                                           │
│   [ kali-atelier ]  ───────── attaque ─────────►  [ metasploitable ]      │
│   Nmap, Hydra, MSF                                services vulnérables     │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
        (aucun port publié vers l'hôte : cible joignable depuis Kali seul)
```

## Démarrage

```bash
cd labs/lab3-4-env
docker compose up -d          # démarre Kali + la cible
docker compose exec kali bash # ouvre un shell dans la machine d'attaque
```

Depuis le shell Kali, la cible est joignable par son nom d'hôte `metasploitable` :

```bash
ping -c 3 metasploitable
nmap -sV -p- metasploitable
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://metasploitable
```

> Au premier lancement, le conteneur Kali installe `kali-tools-top10` ; comptez
> quelques minutes. Suivez l'avancement avec `docker compose logs -f kali`.

### Arrêt

```bash
docker compose down           # arrête et supprime les conteneurs
```

## ⚠️ Limites et points de validation

1. **Image Metasploitable 2 communautaire.** Il n'existe pas d'image Docker
   *officielle* de Metasploitable 2. Le `docker-compose.yml` référence une image
   communautaire (`tleandrocr/metasploitable2`) **non vérifiée**. Avant tout
   usage en séance, l'enseignant devrait :
   - auditer l'image choisie, puis l'**épingler par digest**
     (`image: …@sha256:…`) pour la reproductibilité ;
   - vérifier quels services vulnérables elle expose réellement.
2. **Couverture des services.** La VM d'origine expose de nombreux services
   (vsftpd, Telnet, SSH, HTTP, Samba, etc.). Une image conteneurisée peut n'en
   fournir qu'un sous-ensemble ; certains exercices du Lab 4 peuvent nécessiter
   la VM.
3. **Durcissement réseau (Lab 4, partie iptables).** Appliquer des règles
   `iptables` dans un conteneur demande la capacité `NET_ADMIN` (déjà accordée)
   et ne reflète pas parfaitement le durcissement d'un hôte complet.
4. **Ne jamais exposer la cible.** Ne publiez pas les ports de `metasploitable`
   vers l'hôte et ne la placez pas sur un réseau routable. Elle est
   délibérément vulnérable.

## Faut-il garder la VM ?

Oui, pour la fidélité (surface d'attaque complète, durcissement d'un OS réel).
Cette Option C vise les postes à faibles ressources et les démonstrations
rapides. Les deux approches peuvent coexister.
