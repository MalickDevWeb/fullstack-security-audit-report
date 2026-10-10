# 🛡️ Guide Avancé Pré-Production : Audit de Code Source & Pipeline CI/CD

Ce document présente les démarches avancées recommandées par l'industrie pour valider la sécurité d'une application avant sa mise en production.

---

## 📌 Pourquoi aller au-delà des tests d'interface ?

Les tests via proxy (tests dynamiques / boîte noire) permettent de vérifier les réponses visibles du serveur. Cependant, avant une mise en production critique, la méthode la plus approfondie et rigoureuse est **l'audit direct du code source (Secure Code Review)** combiné à l'analyse statique automatisée (SAST).

```text
Cycle de Validation Pré-Production :
1. Analyse Statique du Code (SAST) ──▶ Détecte les failles directement dans les fichiers source
2. Scan des Dépendances (SCA)       ──▶ Identifie les vulnérabilités (CVE) dans les packages tiers
3. Audit des Fichiers de Config     ──▶ Vérifie Docker, Nginx, variables d'environnement
```

---

## 🛠️ Les 3 Piliers Avancés à Exécuter avant la Production

### 1. Analyse Statique Automatisée du Code (SAST)
L'analyse statique inspecte l'intégralité du code source (Node.js / Express / NestJS) sans avoir besoin d'exécuter l'application, afin d'identifier des flux de données non assainis.

* **Outil de référence : Semgrep (Open Source)**
  Permet d'analyser le code JavaScript/TypeScript pour repérer :
  * Les requêtes de base de données non paramétrées.
  * L'absence de vérification des permissions sur certaines routes.
  * Les mauvaises configurations de cryptographie ou de JWT.
* **Mise en œuvre recommandée :**
  Exécuter un scan local sur le dossier du code source du backend :
  ```bash
  # Commande standard d'audit SAST
  semgrep --config auto
  ```

---

### 2. Audit de la Chaîne d'Approvisionnement (SCA - Dépendances)
Une grande partie du code d'une application moderne provient de bibliothèques tierces installées via `npm`.

* **Vérification native des dépendances :**
  Dans le dossier du projet backend et frontend, exécuter :
  ```bash
  npm audit
  ```
* **Objectif :** Détecter immédiatement si un package utilisé présente une vulnérabilité publique répertoriée (CVE critique) nécessitant une mise à jour immédiate avant la production.

---

### 3. Revue de la Configuration des Secrets & de l'Infrastructure
Pour s'assurer qu'aucun accès privilégié n'est exposé :

* **Vérification de l'historique Git (Chasse aux Secrets) :**
  * S'assurer qu'aucune clé privée, mot de passe de base de données ou secret JWT n'a été accidentellement validé dans les commits précédents de Git.
  * Outils standards : `gitleaks` ou `trufflehog`.
* **Cloisonnement de la Base de Données :**
  * La base de données (MongoDB, PostgreSQL) ne doit **jamais** écouter sur une adresse IP publique. Elle doit être confinée au réseau privé local (`localhost` ou réseau Docker interne).
* **Gestion des Variables d'Environnement :**
  * Vérifier que la variable `NODE_ENV` est strictement définie sur `production` (désactive les pages d'erreurs verbeuses de développement).
  * S'assurer que le secret de signature JWT (`JWT_SECRET`) est une chaîne aléatoire longue et complexe d'au moins 64 caractères générée cryptographiquement.

---

## 📋 Checklist Finale de Validation Pré-Production

- [ ] L'analyse `npm audit` ne remonte aucune vulnérabilité critique ou élevée.
- [ ] Le secret de signature JWT est fort, unique et stocké uniquement dans des variables d'environnement sécurisées.
- [ ] Le port de la base de données est fermé depuis l'extérieur (bloqué par le pare-feu du serveur).
- [ ] Les en-têtes défensifs de masquage (`X-Powered-By`) sont désactivés dans le code.
- [ ] Les sauvegardes automatiques de la base de données sont configurées et chiffrées.

