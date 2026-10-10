# 🛡️ Étape 4 : Injections & Validation Robuste des Entrées

Ce document couvre les principes théoriques et défensifs concernant les injections (OWASP Top 10 - A03: Injection) et les méthodes pour sécuriser le traitement des données utilisateurs.

---

## 📌 Qu'est-ce qu'une faille d'injection ?

Une injection survient lorsqu'une donnée fournie par un utilisateur extérieur est interprétée comme **une instruction de commande ou de code** par un interpréteur (moteur SQL, moteur NoSQL, shell système, interpréteur de templates).

```text
Entrée utilisateur non assainie
              │
              ▼
Concaténation dans une chaîne de commande
              │
              ▼
Interpréteur (SQL, Bash, NoSQL, HTML)
              │
              ▼
Exécution involontaire d'instructions arbitraires
```

---

## 🔬 Les Formes Majeures d'Injection

### 1. Injections SQL (SQLi)
* **Mécanisme :** L'entrée utilisateur casse la syntaxe SQL et altère la structure de la requête.
* **Exemple vulnérable :**
  ```sql
  -- Requête non paramétrée
  SELECT * FROM users WHERE email = 'USER_INPUT';
  ```
* **Contre-mesure absolue :** Utiliser exclusivement des **requêtes préparées (Parameterized Queries)** ou des ORM bien configurés.
  ```sql
  -- Requête sécurisée
  SELECT * FROM users WHERE email = ?;
  ```

### 2. Injections NoSQL
* Dans les applications utilisant des bases orientées documents (ex. MongoDB), l'envoi d'objets au lieu de chaînes de caractères (ex. `{"$ne": ""}`) peut altérer la logique de requête si les types ne sont pas validés strictement.
* **Contre-mesure :** Validation stricte des schémas de données (ex. avec Zod, Joi) et vérification du typage (`typeof input === 'string'`).

### 3. Cross-Site Scripting (XSS)
* **Mécanisme :** Du code JavaScript non échappé est renvoyé dans une page HTML et exécuté dans le navigateur des autres utilisateurs.
* **Contre-mesure :**
  * Échappement contextuel automatique (intégré par défaut dans React/Vue via les balises JSX/templates).
  * Mise en œuvre d'en-têtes HTTP défensifs comme la **Content Security Policy (CSP)**.

---

## 🏗️ La Défense en Profondeur : Les 3 Règles d'Or

1. **Validation en liste blanche (Whitelist Validation) :**
   Vérifier systématiquement le format, la longueur et le type des données dès leur entrée sur l'API (ex. une adresse email doit respecter le format regex d'un email, un ID doit être un format attendu).
2. **Séparation stricte du code et des données :**
   Ne jamais concaténer directement des chaînes de texte fournies par l'utilisateur dans des requêtes de base de données ou des commandes système.
3. **Principe du moindre privilège (Least Privilege) :**
   Le compte de base de données utilisé par l'application ne doit posséder que les droits strictement nécessaires (ex. pas de droits `DROP TABLE` ou `SUPERUSER` pour l'utilisateur web).

---

## 📋 Checklist de Sécurisation des Entrées

- [ ] Toutes les entrées d'API sont-elles validées avec un validateur de schéma (ex. Zod, Joi, Form Request Laravel) ?
- [ ] Les requêtes à la base de données utilisent-elles toutes des requêtes préparées ?
- [ ] Le code évite-t-il toute fonction exécutant directement des chaînes système (ex. `eval()`, `exec()`, `system()`) ?

