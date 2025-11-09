# Défense — SOC, Threat Intelligence et DFIR (résumé)

Ce document résume les concepts clés de la sécurité défensive : le Centre d'Opérations de Sécurité (SOC) et le renseignement sur les menaces, ainsi que la criminalistique numérique et la réponse aux incidents (DFIR), incluant l'analyse de logiciels malveillants.

## Aperçu

- Objectif : détecter, contenir et éliminer les attaques tout en préservant des preuves exploitables.
- Approche : surveillance continue, renseignement contextualisé, procédures de réponse formalisées.

---

## Centre d'Opérations de Sécurité (SOC)

Rôle principal : surveiller les systèmes et réseaux pour repérer et traiter rapidement les événements de sécurité.

Tâches courantes :

- Surveillance des logs et des alertes (SIEM)
- Détection d'intrusions, activité non autorisée et violations de politique
- Coordination avec IT pour la correction des vulnérabilités et la mise en œuvre de contre-mesures
- Containment et escalade vers les équipes DFIR quand nécessaire

Aspects à suivre : vulnérabilités, violations de politique, accès compromis et intrusions réseau.

### SIEM — définition rapide

SIEM (Système d'information de sécurité et de gestion d'événements) est utilisé pour agréger les informations de sécurité sous forme de journaux, d'alertes, d'artefacts et d'événements dans une plate-forme centralisée. Il permet aux analystes de sécurité d'effectuer une analyse en temps quasi réel pendant la surveillance de la sécurité et de corréler des événements pour détecter des incidents complexes.

## Threat Intelligence (renseignement sur les menaces)

But : transformer des données brutes en informations actionnables sur les adversaires (TTPs — tactiques, techniques et procédures).

Processus :

- Collecte : logs internes, traffic réseau, sources publiques et sources spécialisées
- Traitement : normalisation et enrichissement des données
- Analyse : identification d'acteurs, motifs et comportements récurrents
- Production : indicateurs de compromission (IoC) et recommandations opérationnelles

Résultat attendu : priorisation des risques et mesures défensives adaptées au profil d'adversaire.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/ad86b4613e8c0f852ae88be79d5f056f.png)

---

## DFIR — Criminalistique numérique et réponse aux incidents

Composantes principales : criminalistique, réponse aux incidents, analyse de malware.

### Criminalistique numérique

Objectif : collecter et analyser des preuves numériques tout en préservant leur intégrité.

Sources d'investigation :

- Système de fichiers (images disque, fichiers supprimés/partiellement écrasés)
- Mémoire vive (processus et artefacts en mémoire)
- Journaux système et applicatifs
- Journaux et captures réseau

### Réponse aux incidents (phases)

1. Préparation : procédures, playbooks et équipe formée
2. Détection & analyse : identifier et qualifier l'incident
3. Confinement, éradication & récupération : isoler, supprimer la menace et restaurer les services
4. Post-incident : rapport, retour d'expérience et amélioration des contrôles


![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/b162600f5990f249d921aa0e8b7822f7.png)

### Analyse de logiciels malveillants

Les logiciels malveillants existent sous de nombreuses formes ; voici un résumé concis des types les plus courants et pertinents pour la défense :

- Virus — code qui s'attache à un programme hôte et se propage en infectant d'autres fichiers ou systèmes ; peut corrompre ou supprimer des fichiers.
- Ver (Worm) — similaire au virus mais se propage de manière autonome sur les réseaux sans nécessiter d'hôte, souvent très viral.
- Cheval de Troie (Trojan) — programme qui semble légitime mais contient une fonctionnalité malveillante (ex. accès à distance, vol de données).
- Ransomware — chiffre les données d'un utilisateur ou d'un système et exige une rançon pour la clé de déchiffrement.
- Rootkit — logiciel destiné à masquer la présence d'autres malwares et à maintenir un accès furtif au système (niveau noyau ou utilisateur).
- Porte dérobée (Backdoor) — mécanisme qui offre un accès persistant à un attaquant, permettant des connexions ultérieures contournant l'authentification normale.
- Spyware / Espionnage — collecte furtive d'informations (identifiants, navigation, captures d'écran) et les exfiltre vers l'attaquant.
- Adware — affiche des publicités non désirées; peut aussi collecter des données utilisateur et servir d'outil de suivi.
- Botnet (bots) — réseau d'appareils compromis contrôlés à distance pour lancer des attaques (DDoS, campagne de spam, distribution de malware).
- Cryptominer non autorisé — utilise les ressources machine pour miner des cryptomonnaies, dégradant les performances et consommant de l'énergie.
- Fileless malware — s'exécute principalement en mémoire sans laisser (ou avec peu) d'artefacts sur disque, rendant la détection plus difficile.
- Keylogger — enregistre les frappes clavier pour voler mots de passe et autres données sensibles.

Note : il existe aussi des variantes polymorphes, packers et des combinaisons de ces types (ex. cheval de Troie qui installe un cryptominer). Ces classifications aident à prioriser les détections et les réponses.

Méthodes d'analyse :

- Analyse statique : inspection du binaire et du code sans exécution (signatures, reverse engineering, structures de fichiers)
- Analyse dynamique : exécution dans une sandbox ou environnement contrôlé pour observer le comportement (réseau, fichiers, processus)

---

## Checklist opérationnelle rapide

- Collecter et préserver les logs et images avant toute intervention
- Isoler les systèmes compromis pour éviter la propagation
- Appliquer indicateurs de compromission (IoC) et règles SIEM/EDR
- Documenter chaque action (audit trail) et notifier les parties prenantes
- Conduire un post-mortem et mettre à jour les playbooks

---

Pour approfondir : consultez MITRE ATT&CK, guides SIEM/EDR et les playbooks internes de réponse aux incidents.
