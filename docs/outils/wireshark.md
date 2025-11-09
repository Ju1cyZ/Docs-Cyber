# Wireshark — capture et analyse de trafic réseau

Wireshark est un analyseur de paquets réseau graphique permettant d'inspecter en détail le trafic réseau. Il est utile pour le dépannage, l'analyse forensique et l'examen des communications suspectes.

Principes de base :
- Capturer sur l'interface appropriée (ou importer un fichier .pcap)
- Utiliser des filtres d'affichage (ex. `http`, `ip.addr == 10.0.0.5`) pour isoler le trafic pertinent
- Suivre un flux TCP/UDP pour reconstituer une session (Right-click → Follow → TCP Stream)

Filtres courants :
- Capture filter (BPF) : `port 80` (réduit ce qui est capturé)
- Display filter : `http && ip.src == 192.168.1.10` (filtre après capture)

Conseils d'analyse :
- Chercher des anomalies : connexions vers IPs externes inhabituelles, volumes de trafic élevés, retransmissions
- Vérifier les en-têtes TLS (SNI) pour identifier des domaines visités
- Coupler avec logs réseau et SIEM pour corrélation temporelle

Sécurité et confidentialité :
- Les captures contiennent des données sensibles (identifiants, payloads) — protégez les fichiers .pcap
- Préférez l'analyse dans un environnement isolé pour les captures suspectes

Ressources :
- Documentation officielle : https://www.wireshark.org/docs/

---

## Guide complet Wireshark

### Installation

- Windows : installez depuis https://www.wireshark.org/download.html (l'installateur inclut Npcap pour la capture)
- Linux (Debian/Ubuntu) : `sudo apt install wireshark` (pensez aux permissions pour capturer)
- MacOS (Homebrew) : `brew install --cask wireshark`

> Note : la capture de paquets nécessite souvent des privilèges root/Npcap selon le système. Sur Linux, il est courant de permettre l'accès via le groupe `wireshark`.

### Capture vs Filtrage

- Capture filters (BPF) : appliqués au moment de la capture pour réduire la quantité de données saisies. Exemples : `host 10.0.0.5`, `port 53`.
- Display filters : appliqués après capture pour naviguer dans un grand fichier pcap. Syntaxe riche : `tcp.analysis.retransmission`, `dns.qry.name == "example.com"`.

### Analyse pas-à-pas (flux HTTP simple)

1. Capturer ou charger un .pcap
2. Filtrer par protocole : `http` ou `tcp.port == 80`
3. Sélectionner un paquet lié à une session et faire « Follow → TCP Stream » pour reconstituer la requête/réponse
4. Utiliser « Export Objects → HTTP » pour extraire fichiers transférés

### Analyse TLS/HTTPS

- Si vous disposez des clés privées (serveur) et activez SSLKEYLOGFILE pour les clients, Wireshark peut déchiffrer TLS pour analyse.
- Sans clés, inspecter les handshakes (SNI, certificats) et métadonnées (adresses, tailles) pour indices d'activité suspecte.

### TShark (ligne de commande)

- TShark est la version CLI de Wireshark, utile pour automatiser l'analyse et extraire statistiques :

```
tshark -r capture.pcap -Y "http.request" -T fields -e ip.src -e http.host
```

### Filtres et expressions utiles

- `ip.addr == 10.10.10.10` — adresses source ou destination
- `tcp.port == 22` — port TCP
- `dns.qry.name contains "login"` — DNS queries
- `tcp.analysis.retransmission` — retransmissions TCP (problèmes ou exfiltration lente)

### Cas pratiques (exemples)

- Détection d'exfiltration par DNS : chercher volume de requêtes DNS inhabituelles, réponses longues ou requêtes codées.
- Analyse post-intrusion : corréler sessions suspectes (IPs externes) avec logs serveur et SIEM pour reconstituer activité.

### Profils, règles et automatisation

- Créer un profil Wireshark (coloring rules, columns, display filters) adapté aux investigations pour gagner du temps.
- Utiliser TShark pour extraire indicateurs (IPs, user-agents) et les injecter dans le SIEM.

### Images et captures d'écran

- Interface Wireshark (exemple) :
	![Wireshark UI](https://www.wireshark.org/assets/images/wireshark-screenshot.png)
- Exemple de Follow TCP Stream :
	![Follow TCP Stream example](https://www.wireshark.org/assets/images/wireshark-follow-stream.png)

### Sécurité et conformité

- Les fichiers pcap peuvent contenir données personnelles et sensibles — chiffrez et limitez l'accès.
- Respectez les politiques de vie privée et la législation sur la conservation des données lors des captures réseau.

### Ressources et lecture

- Wireshark User Guide : https://www.wireshark.org/docs/wsug_html/
- Capture filters (BPF) : https://www.tcpdump.org/manpages/pcap-filter.7.html
- Wireshark display filter reference : https://www.wireshark.org/docs/dfref/