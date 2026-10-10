# 🛡️ Étape 3 : Théorie & Bonnes Pratiques du Contrôle d'Accès (IDOR / BOLA)

Ce document aborde les concepts d'architecture défensive liés au contrôle d'accès dans les APIs modernes (OWASP Top 10 - A01: Broken Access Control).

---

## 📌 Qu'est-ce que l'IDOR / BOLA ?

* **IDOR (Insecure Direct Object Reference) :** Référence directe et non protégée à un objet interne (ex. clé primaire, fichier, identifiant de compte).
* **BOLA (Broken Object Level Authorization) :** Terme équivalent dans l'OWASP API Top 10 désignant le fait qu'une API omet de vérifier si l'utilisateur qui demande une ressource a bien le droit d'y accéder.

```text
Requête : GET /api/documents/1042
             │
             ▼
Contrôle Serveur :
  1. L'utilisateur est-il connecté ? ➔ Oui (Authentification)
  2. L'utilisateur est-il propriétaire de 1042 ? ➔ ??? (Autorisation)
```

Si le serveur se contente de vérifier que le token est valide sans vérifier l'appartenance de la ressource, il y a un défaut d'autorisation.

---

## 🏗️ Les Bonnes Pratiques d'Architecture Défensive

Pour concevoir des applications résilientes, plusieurs mécanismes doivent être appliqués :

### 1. Contrôle d'accès basé sur la session (Scoped Queries)
Au lieu de chercher directement un élément par son seul identifiant reçu en paramètre :
* ❌ **Approche fragile :**
  ```text
  SELECT * FROM documents WHERE id = :param_id;
  ```
* ✅ **Approche robuste :**
  ```text
  SELECT * FROM documents WHERE id = :param_id AND user_id = :session_user_id;
  ```
  Le serveur force la clause sur l'identifiant extrait du jeton de session validé.

### 2. Utilisation d'identifiants non séquentiels
* Préférer les identifiants opaques (UUID v4, ObjectIDs aléatoires de 128 bits) aux entiers incrémentaux (`1, 2, 3...`).
* *Bénéfice :* Rend la prédiction de ressources plus complexe, bien que cela ne remplace jamais un vrai contrôle de permissions côté code.

### 3. Matrice de droits (RBAC / ABAC)
* **RBAC (Role-Based Access Control) :** Les routes sensibles (ex. création d'examens, suppression de cours) doivent comporter un middleware vérifiant le rôle (`teacher`, `admin`).
* **ABAC (Attribute-Based Access Control) :** Vérifie le contexte dynamique (ex. « L'élève est-il inscrit dans la classe associée à ce cours ? »).

---

## 📋 Checklist de Revue de Code

Lors de la relecture du code backend :
- [ ] Chaque endpoint d'API vérifie-t-il explicitement la propriété de l'objet demandé ?
- [ ] Les routes d'administration ou de gestion sont-elles protégées par un middleware de rôle strict ?
- [ ] Les messages d'erreur indiquent-ils simplement un refus (`403 Forbidden` ou `404 Not Found`) sans divulguer la structure interne de la base de données ?

