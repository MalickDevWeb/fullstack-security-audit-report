# 🛠️ Panorama des Outils d'Évaluation de Sécurité dans l'Industrie

Ce document présente les principales catégories d'outils utilisées par les professionnels de la cybersécurité pour évaluer la robustesse des applications web et APIs, ainsi que leurs cas d'usage respectifs.

---

## 🧭 Les Deux Grandes Familles d'Outils

Dans l'industrie, les audits ne reposent pas sur un seul outil magique, mais sur la combinaison de deux approches complémentaires :

```text
               Évaluation de Sécurité
              ┌──────────┴──────────┐
              ▼                     ▼
     Analyse Dynamique (DAST)   Analyse Statique (SAST)
     (Test depuis l'extérieur)  (Test direct du code source)
```

---

## 1. Les Outils d'Analyse Dynamique (DAST / Proxies Web)

Ces outils analysent les applications en cours d'exécution via le réseau HTTP/HTTPS.

### 🔹 Burp Suite (PortSwigger)
* **Description :** C'est le standard de l'industrie pour les tests d'intrusion web, souvent comparé ou utilisé en alternative directe à OWASP ZAP.
* **Forces :**
  * Interface très réactive et gestion fine des sessions.
  * Moteur de répétition de requêtes (*Repeater*) puissant.
  * Écosystème d'extensions communautaires très riche (*BApp Store*).
  * Scanner automatisé de vulnérabilités réputé (dans sa version commerciale *Professional*).
* **Usage type :** Cartographie d'APIs complexes, manipulation manuelle de jetons et tests de logique métier.

### 🔹 Fuzzers d'URLs & d'Endpoints (ffuf / Gobuster)
* **Description :** Outils légers en ligne de commande (écrits en Go) conçus pour la rapidité.
* **Forces :** Capables d'envoyer des milliers de requêtes par seconde pour énumérer des répertoires ou des paramètres cachés.
* **Limite :** Ne comprennent pas la logique applicative profonde ; ils servent uniquement à la découverte initiale de surface d'attaque.

---

## 2. Les Outils d'Analyse Statique du Code (SAST / SCA)

Ces outils sont considérés comme **les plus exhaustifs** avant une mise en production, car ils ont accès direct à l'intégralité des lignes de code source et de l'architecture backend.

### 🔹 Semgrep
* **Description :** Moteur d'analyse statique rapide et polyvalent, orienté vers la sécurité du code moderne (TypeScript, Node.js, Python, Go).
* **Forces :**
  * Règles de sécurité écrites selon la syntaxe naturelle du langage analysé.
  * Analyse des flux de données non assainis (*taint analysis*).
  * Intégration facile dans les pipelines CI/CD (GitHub Actions, GitLab CI).

### 🔹 GitHub CodeQL
* **Description :** Technologie d'analyse sémantique développée par GitHub qui traite le code source comme une base de données interrogeable.
* **Forces :** Très puissant pour tracer le parcours précis d'une entrée utilisateur depuis une route d'API jusqu'à une fonction critique (base de données, exécution de commande).

### 🔹 SonarQube
* **Description :** Plateforme de gestion de la qualité et de la sécurité du code.
* **Forces :** Combine la détection des vulnérabilités de sécurité (Security Hotspots) avec l'analyse de bugs et de dette technique.

---

## ⚖️ Synthèse Comparative

| Outil | Type | Point fort principal | Limite principale |
|-------|------|----------------------|-------------------|
| **OWASP ZAP** | DAST / Proxy | Gratuit, open-source, excellent pour l'apprentissage et l'interception | Scanner automatisé parfois plus verbeux |
| **Burp Suite** | DAST / Proxy | Ergonomie professionnelle, écosystème d'extensions | Les fonctions de scan automatisé nécessitent une licence payante |
| **Semgrep** | SAST (Code) | Analyse instantanée du code source sans déploiement | Ne teste pas l'environnement serveur en direct |
| **CodeQL** | SAST (Code) | Modélisation poussée des flux de données complexes | Configuration plus lourde |

---

## 💡 Conclusion Méthodologique

Un outil DAST (comme ZAP ou Burp) ne remplace pas une revue de code (SAST), et inversement :
* **Le proxy (ZAP / Burp)** valide que les défenses réseau, les cookies et les middlewares fonctionnent correctement en production.
* **L'analyseur de code (Semgrep / CodeQL)** garantit qu'aucune faille logique n'a été oubliée dans les milliers de lignes de programmation.

