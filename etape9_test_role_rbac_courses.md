# 🛡️ Étape 9 : Test du Contrôle d'Accès par Rôle (BOLA / RBAC)

Ce document consigne le protocole de vérification du contrôle d'accès basé sur les rôles (RBAC) sur l'API de gestion des cours.

---

## 🎯 Objectif du Test (Point 8 du Plan d'Audit)

Vérifier qu'un utilisateur disposant du rôle standard **`student`** ne peut pas exécuter d'actions d'écriture ou d'administration (comme créer un nouveau cours via la méthode `POST`), qui doivent être strictement réservées aux professeurs ou administrateurs.

---

## 🔬 Protocole de Test dans OWASP ZAP (Requêteur)

1. **Requête Légitime observée (Lecture) :**
   * Méthode : `GET`
   * URL : `https://jaang.saharux.com/api/courses`
   * Résultat : `200 OK` (l'élève a le droit légitime de consulter la liste des cours de sa classe).

2. **Tentative d'Élévation de Privilège (Écriture) :**
   * Méthode : Changer `GET` en **`POST`**
   * URL : `https://jaang.saharux.com/api/courses`
   * En-tête : Conserver le token d'élève actif (`Authorization: Bearer <token_eleve>`)
   * En-tête : `Content-Type: application/json`
   * Corps (Body) :
     ```json
     {
       "title": "Cours Test Audit",
       "subtitle": "Test d'autorisation"
     }
     ```

---

## 📊 Matrice des Résultats Attendus

| Code HTTP Reçu | Interprétation Sécurité | Diagnostic |
|:--------------:|-------------------------|:----------:|
| **`403 Forbidden`** | Le serveur valide le token mais constate que le rôle `student` n'a pas les droits de création. | ✅ **Sécurisé & Conforme** |
| **`404 Not Found`** | La route `POST /api/courses` n'existe pas ou est masquée. | ✅ **Sécurisé** |
| **`405 Method Not Allowed`** | L'endpoint interdit explicitement la méthode `POST`. | ✅ **Sécurisé** |
| **`201 Created` / `200 OK`** | Le cours a été créé par le compte élève ! | ❌ **Vulnérabilité BOLA (Absence de contrôle de rôle)** |

