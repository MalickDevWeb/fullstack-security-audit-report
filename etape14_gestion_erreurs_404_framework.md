# 🛡️ Étape 14 : Gestion des Exceptions & Réponses 404 (Test Réussi)

Ce document consigne l'analyse de la réponse du serveur face à une tentative de manipulation d'identifiant (ID inexistant).

---

## 🔬 Requête Testée (Point 7 du Plan d'Audit)

* **Méthode :** `GET`
* **Cible :** `https://jaang.saharux.com/api/courses/000000000000000000000000`
* **Session :** Compte élève authentifié

---

## 📋 Réponse Obtenue du Serveur

```http
HTTP/1.1 404 Not Found
Content-Type: application/json; charset=utf-8

{
  "message": "Cannot GET /api/courses/000000000000000000000000",
  "error": "Not Found",
  "statusCode": 404
}
```

---

## 🎯 Analyse & Bilan de Sécurité

### 1. Robustesse du Gestionnaire d'Erreurs :
* Dans beaucoup d'applications fragiles, soumettre un identifiant inconnu provoque une exception de base de données non gérée (ex: `CastError` de Mongoose ou plantage SQL) qui renvoie une erreur `500 Internal Server Error` avec une fuite du code source (stack trace).
* **Votre résultat :** Le serveur gère l'absence de route ou de ressource proprement avec un code standard **`404 Not Found`**.

### 2. Signature du Framework Backend Identifiée :
Le format exact de la réponse JSON :
```json
{"message": "Cannot GET ...", "error": "Not Found", "statusCode": 404}
```
Est la signature typique de **NestJS** (ou d'une couche middleware Express standardisée). 
* **Points forts :** Structure JSON uniforme pour toutes les erreurs de l'application, ce qui facilite la maintenance et évite toute fuite de variables d'environnement ou de requêtes internes.

