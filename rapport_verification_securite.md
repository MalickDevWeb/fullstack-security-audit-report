# 🛡️ Rapport d'Audit & Grille de Vérification de Sécurité

Ce document rassemble la synthèse des vérifications techniques effectuées sur l'application (`https://jaang.saharux.com`) ainsi que les points de contrôle restants pour valider la robustesse globale du système.

---

## 📊 1. Synthèse des Contrôles Déjà Validés

| Domaine testé | Observation technique | Statut |
|---------------|-----------------------|:------:|
| **Protection des fichiers sensibles** | Absence d'exposition directe de `/.env` ou `/.git` (redirection SPA propre) | ✅ Conforme |
| **Authentification par jeton (JWT)** | Durée de vie courte de l'accessToken (15 min) + Refresh token séparé | ✅ Conforme |
| **Protection des routes privées** | Rejet strict sans session (`401 Unauthorized`) sur `/api/independent/me` | ✅ Conforme |
| **Protection Anti-DDoS / Brute-force** | `X-RateLimit-Limit: 120` requêtes/min actif | ✅ Conforme |
| **En-têtes de transport sécurisé** | HSTS (`Strict-Transport-Security`) actif pour 6 mois | ✅ Conforme |
| **Protection anti-Clickjacking** | `X-Frame-Options: SAMEORIGIN` présent | ✅ Conforme |
| **Protection MIME** | `X-Content-Type-Options: nosniff` présent | ✅ Conforme |

---

## 🔬 2. Grille de Vérification des Contrôles d'Accès (À tester manuellement)

Pour vérifier que le cloisonnement des rôles (élève vs admin) est étanche :

### Test A : Tentative d'accès à un endpoint privilégié avec un compte élève
* **Concept :** Prendre la requête valide avec votre token d'élève (`Authorization: Bearer <token_eleve>`).
* **Action :** Modifier l'URL pour viser un chemin d'administration supposé (ex. `/api/admin/users` ou `/api/admin/dashboard`).
* **Résultat attendu :**
  * Le serveur doit renvoyer **`403 Forbidden`** (ou `404 Not Found`).
  * Si le serveur renvoie `200 OK` avec des données, il y a un défaut de contrôle de rôle.

---

### Test B : Vérification de la non-altération du rôle (Privilege Escalation)
* **Concept :** Lors de la mise à jour de son profil élève (ex. `PUT /api/independent/me`), observer le corps JSON envoyé.
* **Action :** Vérifier que si un paramètre `"role": "admin"` est ajouté manuellement dans le JSON envoyé, le serveur l'ignore purement ou le rejette.
* **Résultat attendu :** Le rôle en base de données reste inchangé (`student`).

---

### Test C : Étancheité entre élèves (Prévention IDOR)
* **Concept :** Deux élèves inscrits ne doivent pas pouvoir consulter les notes ou devoirs privés de l'autre.
* **Action :** Si une ressource utilise un identifiant dans l'URL (ex. `/api/lessons/{id}` ou `/api/submissions/{id}`), vérifier que le serveur valide bien l'inscription de l'élève connecté avant de délivrer le contenu privé.
* **Résultat attendu :** Accès restreint uniquement aux ressources légitimes de la classe.

---

## 💡 Résumé : Pourquoi le 100% sûr n'existe pas

La sécurité d'une application est un **processus continu de réduction des risques** :
1. Chaque contrôle en place (Rate Limiting, HSTS, JWT courts, vérification des rôles) élimine une catégorie entière de risques.
2. La tenue à jour des dépendances (`npm audit`) et la relecture régulière du code (revue de code) garantissent que les nouvelles fonctionnalités n'introduisent pas de régressions.

