# Architecture du Wazuh SOC Lab

## 1. Présentation

Ce laboratoire met en place une architecture SIEM basée sur Wazuh afin de superviser un endpoint Windows 11 depuis un serveur Ubuntu.

L'environnement est entièrement virtualisé avec VirtualBox.

L'objectif est de reproduire une architecture SOC simple permettant la collecte, l'analyse et la visualisation des événements de sécurité.

---

## 2. Architecture

                  ┌─────────────────────────────┐
                  │       Windows 11 Pro        │
                  │     192.168.56.101          │
                  │                             │
                  │  • Wazuh Agent              │
                  │  • Windows Event Logs       │
                  │  • Microsoft Defender       │
                  │  • FIM                      │
                  │  • SCA / CIS Benchmark      │
                  └──────────────┬──────────────┘
                                 │
                                 │ TCP 1514
                                 │
                                 ▼
                  ┌─────────────────────────────┐
                  │        Wazuh Server         │
                  │       Ubuntu Server         │
                  │       192.168.56.10         │
                  │                             │
                  │  ┌───────────────────────┐  │
                  │  │    Wazuh Manager      │  │
                  │  │ Analyse / Détection   │  │
                  │  └───────────┬───────────┘  │
                  │              │              │
                  │  ┌───────────▼───────────┐  │
                  │  │    Wazuh Indexer      │  │
                  │  │ Stockage / Indexation │  │
                  │  └───────────┬───────────┘  │
                  │              │              │
                  │  ┌───────────▼───────────┐  │
                  │  │   Wazuh Dashboard     │  │
                  │  │ Visualisation / SOC   │  │
                  │  └───────────────────────┘  │
                  └─────────────────────────────┘

---

## 3. Composants

### Wazuh Manager

Le Wazuh Manager constitue le cœur du SIEM.

Il reçoit les événements provenant de l'agent Windows 11 et applique les règles de détection afin d'identifier les événements de sécurité.

Une règle personnalisée a notamment été développée pour détecter certains échecs d'authentification Windows.

---

### Wazuh Indexer

Le Wazuh Indexer est utilisé pour stocker et indexer les événements et alertes générés par Wazuh.

Il permet au Dashboard d'effectuer des recherches dans les données collectées.

Port utilisé :

TCP 9200

---

### Wazuh Dashboard

Le Wazuh Dashboard fournit l'interface graphique permettant d'analyser les événements de sécurité.

Il permet notamment de consulter :

- les alertes de sécurité ;
- les événements Windows ;
- les résultats FIM ;
- les résultats SCA ;
- les événements Microsoft Defender ;
- les règles déclenchées ;
- les données de Threat Hunting.

Accès :

HTTPS / TCP 443

---

### Wazuh Agent

L'agent Wazuh est installé sur Windows 11 Pro.

Il collecte les informations de sécurité de l'endpoint avant de les transmettre au Wazuh Manager.

Adresse du serveur :

192.168.56.10

Adresse de l'endpoint :

192.168.56.101

Communication principale :

TCP 1514

---

## 4. Flux de données

Le fonctionnement général du laboratoire est le suivant :

Windows 11
    ↓
Wazuh Agent
    ↓
Wazuh Manager
    ↓
Règles de détection
    ↓
Wazuh Indexer
    ↓
Wazuh Dashboard
    ↓
Analyse / Threat Hunting

---

## 5. Sources de données supervisées

Plusieurs sources de données ont été intégrées au laboratoire :

### Windows Event Logs

Collecte des événements Windows, notamment :

- connexions réussies ;
- échecs d'authentification ;
- modifications de comptes ;
- événements système.

### Microsoft Defender

Collecte du journal :

Microsoft-Windows-Windows Defender/Operational

Cela permet de faire remonter les événements de sécurité Microsoft Defender dans Wazuh.

### File Integrity Monitoring

Le module FIM permet de détecter :

- création de fichiers ;
- modification de fichiers ;
- suppression de fichiers.

### Security Configuration Assessment

Wazuh SCA analyse la configuration de Windows 11 à partir du :

CIS Microsoft Windows 11 Enterprise Benchmark v3.0.0

---

## 6. Règle de détection personnalisée

Une règle Wazuh personnalisée a été créée pour le laboratoire.

Rule ID :

100100

Niveau :

8

Description :

LAB SOC - Echec d'authentification detectee sur Windows 11

Le fichier correspondant est disponible dans :

../rules/local_rules.xml

---

## 7. Ports principaux

| Port | Service | Fonction |
|------|---------|----------|
| TCP 1514 | Wazuh Manager | Communication avec les agents |
| TCP 1515 | Wazuh Enrollment | Enrôlement des agents |
| TCP 55000 | Wazuh API | API du Manager |
| TCP 9200 | Wazuh Indexer | Indexation / recherche |
| TCP 443 | Wazuh Dashboard | Interface Web |

---

## 8. Résultat

Cette architecture permet de centraliser les événements d'un endpoint Windows 11 dans Wazuh et de réaliser différentes opérations SOC :

- collecte des logs ;
- supervision d'un endpoint ;
- analyse des événements ;
- détection d'échecs d'authentification ;
- surveillance de l'intégrité des fichiers ;
- analyse de configuration avec CIS Benchmark ;
- supervision Microsoft Defender ;
- création de règles personnalisées ;
- Threat Hunting.
