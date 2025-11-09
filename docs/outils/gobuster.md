---
title: Gobuster
description: Utilisation et exemples de l'outil Gobuster pour le bruteforce de répertoires, DNS, vhosts et fuzzing.
---

# Gobuster

Une documentation concise et pratique pour utiliser Gobuster (outil en Go) — recherche de répertoires/fichiers, découverte de sous-domaines (DNS), vhosts et fuzzing de paramètres.

## À propos

Gobuster est un outil rapide écrit en Go, utilisé principalement pour la reconnaissance web :

- Bruteforce de répertoires/fichiers (mode `dir`).
- Découverte de sous-domaines via DNS (`dns`).
- Recherche de virtual hosts (`vhost`).
- Fuzzing de paramètres et endpoints (`fuzz`).

Site officiel / dépôt : https://github.com/OJ/gobuster

## Installation

Vous pouvez installer Gobuster de différentes manières selon votre plateforme.

### Via Go (recommandé si vous avez Go installé)

```bash
go install github.com/OJ/gobuster/v3@latest
# L'exécutable sera dans $GOPATH/bin ou $GOBIN
```

### Linux (deb/apt) — exemples

Sur certaines distributions vous trouverez gobuster dans les dépôts :

```bash
sudo apt update
sudo apt install gobuster
```

### Windows

Téléchargez le binaire depuis les Releases du dépôt GitHub et placez-le dans un répertoire présent dans votre PATH.

### Brew (macOS / Homebrew)

```bash
brew install gobuster
```

## Wordlists recommandées

Les wordlists populaires :

- SecLists (recommandé) : https://github.com/danielmiessler/SecLists
- common.txt / directory-list-2.3-medium.txt / big.txt selon le niveau de profondeur

Exemple d'installation de SecLists :

```bash
git clone https://github.com/danielmiessler/SecLists.git /opt/seclists
```

## Usage général

La forme générale :

```bash
gobuster <mode> -u <target> -w <wordlist> [options]
```

Modes principaux :

- `dir` : découverte de répertoires/fichiers HTTP
- `dns` : discovery DNS (résolution d'enregistrements A/AAAA)
- `vhost` : recherche de virtual hosts
- `fuzz` : fuzzing générique

### Découverte de répertoires (mode dir)

Exemple basique :

```bash
gobuster dir -u https://example.com/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 50
```

Options utiles :

- `-u, --url` : URL cible (ou `-u` + `-k` pour ignorer certif SSL si nécessaire)
- `-w, --wordlist` : chemin de la wordlist
- `-t, --threads` : nombre de threads (par défaut ~10)
- `-x` : extensions à tester (ex: `php,html,txt`)
- `-s` : codes HTTP à considérer comme intéressants (ex: `200,204,301,302,307,401,403`)
- `-o` : fichier de sortie
- `-a` : user-agent personnalisé

Gestion du SSL (ignorer la vérification) :

```bash
gobuster dir -u https://example.com/ -w wordlist.txt -k
```

Exemple avec extensions :

```bash
gobuster dir -u https://example.com/ -w /opt/SecLists/Discovery/Web-Content/common.txt -x .php,.html,.bak -t 40 -o results_dir.txt
```

### Découverte DNS (mode dns)

```bash
gobuster dns -d example.com -w /opt/SecLists/Discovery/DNS/subdomain-top1million-20000.txt -t 50 -o results_dns.txt
```

Options notables :

- `-d, --domain` : domaine principal
- `-r, --resolvers` : fichier de résolveurs DNS à utiliser (par défaut /etc/resolv.conf sur Linux)

Exemple avec résolveurs custom :

```bash
gobuster dns -d example.com -w subs.txt -r resolvers.txt -t 40
```

### Virtual host discovery (mode vhost)

```bash
gobuster vhost -u https://example.com -w vhosts.txt -t 50
```

Le mode vhost teste des en-têtes Host différents pour détecter des virtual hosts hébergés derrière la même IP.

### Fuzzing (mode fuzz)

Le mode `fuzz` vous permet d'injecter le mot courant de la wordlist dans une URL ou un paramètre via la token `{}`, par ex :

```bash
gobuster fuzz -u https://example.com/page.php?file=FUZZ -w wordlist.txt -t 40
```

Dans Gobuster v3+, la syntaxe correcte pour la position de fuzz est d'utiliser `FUZZ` (ou `{}` selon la version) ; vérifiez la version et l'argument d'aide (`gobuster help fuzz`).

## Sorties et formats

- Par défaut Gobuster affiche les résultats en console.
- Utilisez `-o <fichier>` pour écrire les résultats.
- Certaines versions supportent `-json` ou un format JSON (vérifiez `gobuster --help`).

Exemple :

```bash
gobuster dir -u https://example.com -w wordlist.txt -o output.txt
```

## Astuces et bonnes pratiques

- Commencez avec une wordlist courte puis montez en taille pour réduire le bruit.
- Utilisez des `-t` (threads) adaptés à la cible et à votre bande passante : 10-50 est classique.
- Respectez la charge cible : évitez de surcharger des serveurs en production.
- Filtrez les faux positifs (wildcards DNS ou pages d'erreur custom) en ajustant `-s` et en testant manuellement les URLs trouvées.
- Combinez avec `wfuzz`, `dirsearch` ou `ffuf` si vous voulez comparer résultats/perf.

## Gestion des wildcards et pages d'erreur

Certains sites répondent toujours 200 même pour des chemins inexistants. Pour détecter cela :

1. Testez un fichier aléatoire improbable (ex: `/thispathshouldnotexist_12345`) et observez le code et la longueur.
2. Si vous observez la même longueur qu'une découverte, utilisez `--wildcard` ou filtrez par longueur/regex (selon les capacités de la version) ou examinez manuellement.

## Exemples pratiques complets

1) Bruteforce rapide, wordlist medium, extensions .php et .bak, sortie dans un fichier :

```bash
gobuster dir -u https://target.local/ -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x .php,.bak -t 40 -s 200,204,301,302,307,403 -o gobuster_dir.txt
```

2) Découverte DNS avec résolveurs rapides :

```bash
gobuster dns -d example.com -w /opt/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -r resolvers.txt -t 50 -o gobuster_dns.txt
```

3) Fuzz d'une URL avec paramètre :

```bash
gobuster fuzz -u "https://example.com/page.php?file=FUZZ" -w /opt/SecLists/Discovery/Web-Content/common.txt -t 30 -o gobuster_fuzz.txt
```

## Exemple de sortie (extrait)

```
# Exemple simplifié
/admin (Status: 301)
/robots.txt (Status: 200)
/uploads (Status: 403)
```

## Dépannage

- Si Gobuster ne démarre pas : vérifiez que l'exécutable est dans le PATH et exécutez `gobuster --version`.
- Erreurs SSL : utilisez `-k` pour ignorer la vérification, ou fournissez un certificat valide.
- Problèmes de permissions sur wordlists : vérifiez les droits de lecture.

## Sécurité et éthique

Ne lancez Gobuster que sur des cibles pour lesquelles vous avez l'autorisation explicite. Les scans non autorisés peuvent être illégaux ou considérés comme une activité malveillante.

## Références

- Dépôt GitHub : https://github.com/OJ/gobuster
- SecLists (wordlists) : https://github.com/danielmiessler/SecLists

---

Si vous voulez, je peux :

- ajouter des exemples Windows PowerShell adaptés (escape des guillemets, chemins) ;
- ajouter des scénarios de test (fichiers de résolveurs, scripts d'exécution) ;
- générer une page sœur avec captures d'écran ou exemples JSON.

