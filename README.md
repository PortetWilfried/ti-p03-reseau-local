# Projet 3 — Déployer un réseau local filaire et Wi-Fi dans le respect des bonnes pratiques

> Projet réalisé dans le cadre de la formation **Technicien Informatique** (OpenClassrooms — RNCP niveau 5), novembre 2025.

## Contexte

Mise en place du réseau local d'une **médiathèque** : connexion des postes internes, des serveurs et des imprimantes en filaire, et déploiement de deux réseaux Wi-Fi distincts — un pour le personnel, un pour les visiteurs — avec niveaux de sécurité différenciés.

## Objectifs techniques

1. Élaborer un **plan d'adressage IP** complet (statique + dynamique via DHCP)
2. Construire la **maquette du réseau** sous Cisco Packet Tracer
3. Configurer un **Wi-Fi interne sécurisé** (WPA2 Enterprise) et un **Wi-Fi visiteurs** isolé (WPA2 Personal)
4. Sécuriser l'administration des équipements réseau
5. **Tester la solution** de bout en bout (connectivité, isolation, DHCP)

## Topologie

Le réseau s'articule autour d'un commutateur central reliant tous les équipements câblés, et d'un point d'accès Wi-Fi dual-SSID :

- **9 postes utilisateurs** (filaire) — `PC0` à `PC8`
- **3 imprimantes** — accueil + 2 publiques
- **3 serveurs** dédiés — DHCP/DNS, Radius (authentification 802.1X), Stockage
- **1 caméra IP** (IoT) — supervision
- **Réseau Wi-Fi médiathèque** (privé, WPA2 Enterprise via Radius) — tablettes pro + laptops staff
- **Réseau Wi-Fi visiteurs** (WPA2 Personal, SSID broadcasté) — appareils des usagers

## Plan d'adressage IP

Sous-réseau privé : **192.168.10.0 /24**

| Paramètre | Valeur |
|---|---|
| Masque de sous-réseau | 255.255.255.0 |
| Nombre d'IP hôtes disponibles | 254 |
| Première IP hôte | 192.168.10.1 |
| Dernière IP hôte | 192.168.10.254 |
| Adresse de diffusion | 192.168.10.255 |

### Configuration statique

| Équipement | IP attribuée |
|---|---|
| Routeur Wi-Fi (IP privée) | 192.168.10.1 |
| Serveur DHCP / DNS | 192.168.10.11 |
| Serveur Radius | 192.168.10.12 |
| Serveur Stockage | 192.168.10.13 |
| Imprimante accueil | 192.168.10.21 |
| Imprimante publique 1 | 192.168.10.22 |
| Imprimante publique 2 | 192.168.10.23 |

### Configuration dynamique (DHCP)

| Paramètre | Valeur |
|---|---|
| Nom de l'étendue (pool) | `serverPool` |
| Plage d'adresses | `192.168.10.24` → `192.168.10.254` |
| Nombre d'adresses distribuables | 231 |
| Passerelle par défaut | 192.168.10.1 |
| Serveur DNS | 192.168.10.11 |

## Configuration Wi-Fi

| Paramètre | Réseau médiathèque | Réseau visiteurs |
|---|---|---|
| **SSID** | `Wifi-Mediatheque` | `Wifi-Visiteurs` |
| **Diffusion du SSID** | ❌ Non (caché) | ✅ Oui |
| **Bandes** | 2,4 GHz + 5 GHz | 2,4 GHz + 5 GHz |
| **Mode de sécurité** | **WPA2 Enterprise** (authentification via Radius / 802.1X) | **WPA2 Personal** (mot de passe partagé) |

> Le choix d'un **SSID caché + WPA2 Enterprise** pour le réseau interne évite l'affichage public du nom et impose une authentification individuelle par compte (traçabilité). Le Wi-Fi visiteurs est en SSID broadcasté pour faciliter la connexion des usagers, avec un mot de passe simple à changer régulièrement.

## Stack technique

| Catégorie | Outils |
|---|---|
| Simulation réseau | **Cisco Packet Tracer** |
| Services réseau | DHCP, DNS, Radius (802.1X) |
| Sécurité Wi-Fi | WPA2 Enterprise / Personal |
| Documentation | Schéma logique, plan d'adressage, procédure de test |

## Démarche

1. **Analyse du besoin** — typologie des utilisateurs (staff vs visiteurs), nombre d'équipements, contraintes de sécurité
2. **Conception du plan d'adressage** — choix d'un /24, séparation IPs statiques (infrastructure) / dynamiques (clients)
3. **Construction de la maquette Packet Tracer** — switch central, raccordement filaire, point d'accès dual-SSID
4. **Configuration des services** — DHCP scope, DNS, Radius pour l'authentification staff
5. **Configuration du Wi-Fi** — deux SSID distincts, niveaux de sécurité adaptés à chaque usage
6. **Tests de connectivité** — ping inter-équipements, DHCP lease, authentification Wi-Fi, isolation visiteurs

## Difficultés rencontrées et solutions

- **Problème** : configurer WPA2 Enterprise sur le HomeRouter Packet Tracer ne suffit pas — il faut aussi enrôler le serveur Radius (client AAA) et créer les comptes utilisateurs côté Radius.
- **Solution** : configuration croisée — clé partagée entre routeur et Radius, déclaration du routeur comme client AAA sur Radius, ajout des comptes utilisateurs avec attributs adaptés.
- **Apprentissage** : un Wi-Fi "Enterprise" n'est pas une simple case à cocher — c'est une **chaîne d'authentification** (client → AP → Radius → base de comptes). Comprendre ce flux est indispensable pour diagnostiquer un échec d'auth en entreprise.

## Compétences validées

- Déployer et configurer des équipements réseau (switch, routeur Wi-Fi)
- Concevoir et documenter l'architecture réseau d'une entreprise
- Mettre en œuvre les bonnes pratiques de sécurité (SSID caché, authentification 802.1X, isolation visiteurs)
- Rédiger une documentation technique exploitable

## Livrables

- 📊 [Plan d'adressage et configuration réseau](docs/01-configuration-reseau.md) — version Markdown du plan technique
- 📁 *Fichier Packet Tracer (.pkt)
- 🖼️ *Capture de la topologie complète

## Liens

- [← Retour au portfolio](https://github.com/PortetWilfried)
- [Voir le projet 2 — Déploiement Windows 11 →](https://github.com/PortetWilfried/ti-p02-deploiement-windows11)
