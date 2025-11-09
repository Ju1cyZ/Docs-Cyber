# Nmap — scanner de ports et découverte réseau

Nmap (Network Mapper) est un outil open-source utilisé pour la découverte de réseau et la sécurité. Il permet d'identifier les hôtes actifs, les ports ouverts, les services qui y sont liés et parfois les versions logicielles.

Cas d'usage :
- Cartographie d'un réseau avant audit
- Découverte des services exposés
- Vérification post-hardening pour s'assurer qu'aucun port non voulu n'est ouvert

Options utiles :
- -sS : scan SYN (stealth)
- -p- : scanner tous les ports
- -A : détection de version et OS, scripts NSE
- -T4 : vitesse accélérée (attention sur les réseaux sensibles)
- -oA sortie : exporter résultats (formats nmap/gnmap/xml)

Exemples :
- Scan rapide des ports courants : `nmap -sS -T4 192.168.1.0/24`
- Scan complet avec détection : `nmap -sS -p- -A -T4 cible.example.com`

Bonnes pratiques (défense) :
- Surveiller les scans entrants comme indicateur d'intérêt malveillant
- Coupler avec IDS/IPS pour détecter scans à grande échelle
- Restreindre l'exposition des services non nécessaires par pare-feu et filtrage

Ressources :
- Documentation officielle : https://nmap.org/

---

## Documentation complète et guide d'utilisation

Cette page donne un guide plus détaillé pour l'utilisation de Nmap, orienté à la fois pour les pentesters et pour les équipes de défense qui doivent détecter et se protéger contre des scans.

### Installation

- Linux (Debian/Ubuntu) : `sudo apt update ; sudo apt install nmap`
- Fedora/CentOS : `sudo dnf install nmap` ou `sudo yum install nmap`
- MacOS (Homebrew) : `brew install nmap`
- Windows : installez depuis https://nmap.org/download.html (package inclut Zenmap GUI sur certaines versions)

### Types de scans (raccourci)

- -sS : SYN scan (rapide, « stealth »)
- -sT : TCP connect (utilise la pile TCP, plus bruyant)
- -sU : scan UDP (lent, nécessite privilèges)
- -sA : ACK (détecte état des firewalls/filtrage)
- -sV : détection de version des services
- -O : détection du système d'exploitation
- -Pn : ne pas ping avant scan (assume hôte up)
- -p <ports> : spécifier un ou plusieurs ports, ex. `-p 22,80,443` ou `-p-` pour tous

### Options avancées

- Timing : `-T0` (paranoïa) à `-T5` (agressif). `-T4` est souvent un bon compromis pour la vitesse.
- NSE (Nmap Scripting Engine) : `--script` pour exécuter des scripts (ex. `--script vuln` ou `--script default`)
- Fragmentation et options IP : `-f` (fragment packets) ou `--data-length` pour tenter d'échapper à certains IDS (usage éthique seulement)
- Décoys / source spoofing : `-D`, `-S` (attention légal/éthique)

### Exemples détaillés

- Scan découverte et version sur un hôte :

```
nmap -sS -sV -O -p- -T4 cible.example.com
```

- Scan réseau /24 et export des résultats :

```
nmap -sS -T4 -p1-65535 -oA results/local-scan 192.168.1.0/24
```

- Utiliser NSE pour chercher vulnérabilités connues :

```
nmap --script vuln -sV cible.example.com
```

### Interpréter les résultats

- Port ouvert (`open`) : service accessible — action prioritaire pour défense.
- Port filtré (`filtered`) : pare-feu ou filtre bloque la sonde — utile pour détecter présence de filtrage.
- Port fermé (`closed`) : hôte répond mais port fermé — moins critique.

### Intégration et automatisation

- Exécuter Nmap via scripts (bash/python) et indexer les résultats XML dans un SIEM.
- Coupler avec des playbooks : si un port critique est ouvert, déclencher un ticket et un scan approfondi.

### Détection et contremesures (pour équipes Blue Team)

- Détecter scans par corrélation de logs (IDS signatures, nombreux SYN vers plusieurs ports)
- Mettre en place honeypots pour surveiller techniques d'énumération
- Appliquer firewall rules et limiter exposition des services

### Sécurité, éthique et légalité

L'utilisation de Nmap sur des réseaux ou hôtes sans autorisation est illégale dans de nombreuses juridictions. Ne scannez que des cibles dont vous avez l'autorisation explicite.

### Images (exemples visuels)

- Interface Zenmap (GUI) :
	![Zenmap screenshot](https://nmap.org/images/zenmap-screenshot.png)
- Exemple de sortie nmap :
	![Nmap output example](https://nmap.org/images/nmap-scan.png)

*(Si les images externes ne s'affichent pas, vous pouvez remplacer les URLs par des captures locales dans `docs/images/`.)*

### Références

- Site officiel Nmap : https://nmap.org/
- Documentation NSE : https://nmap.org/book/nse.html
- Guide pratique : Nmap Network Scanning (F. Lyon)