# 🛡️ Étape 13 : Test de Compartimentation des Ressources (Prévention IDOR)

Ce document consigne le protocole de vérification de l'étanchéité des ressources entre utilisateurs et classes (OWASP Top 10 - A01: Broken Access Control).

---

## 🎯 Objectif du Test (Point 7 du Plan d'Audit)

Vérifier si un élève peut accéder à un cours ou une ressource privée en manipulant manuellement l'identifiant (ID) dans l'URL :
```text
Requête : GET /api/courses/{id_inexistant_ou_autre_classe}
             │
             ▼
Contrôle Serveur :
  - Le cours existe-t-il ?
  - Si oui, l'élève appartient-il à la classe autorisée (classId) ?
```

---

## 🔬 Protocole de Test dans OWASP ZAP (Requêteur)

Dans votre catalogue de cours, l'ID réel du cours Flutter est :
`6a92c6ae8048783b49c38983`

Pour tester la robustesse de l'API face à une référence directe :

### 1. Test avec un faux identifiant (ID inexistant)
* **Méthode :** `GET`
* **URL :** `https://jaang.saharux.com/api/courses/000000000000000000000000`
* **En-têtes :** Votre token JWT valide

---

## 📊 Matrice des Réponses Attendues

| Code HTTP Reçu | Signification Sécurité | Diagnostic |
|:--------------:|------------------------|:----------:|
| **`404 Not Found`** | Le serveur recherche proprement l'objet et indique son absence sans crasher. | ✅ **Sécurisé** |
| **`403 Forbidden`** | Le serveur refuse l'accès car la ressource n'appartient pas au périmètre de l'élève. | ✅ **Sécurisé** |
| **`500 Internal Error`** | L'identifiant atypique fait planter la requête (mauvaise gestion des exceptions). | ⚠️ **Fragilité** |

