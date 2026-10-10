# 🛡️ Étape 2 : Analyse de l'Authentification & Gestion des Sessions

Ce document détaille comment auditer le mécanisme de connexion et la gestion des sessions d'une API à partir d'une capture de proxy.

---

## 🎯 Objectifs de l'étape

1. Analyser la requête d'authentification (`POST /api/auth/login`).
2. Identifier le mécanisme de maintien de session (Token JWT dans un en-tête `Authorization` vs Cookie de session).
3. Vérifier les protections défensives :
   * Si JWT : Le token est-il signé avec un algorithme robuste ?
   * Si Cookie : Porte-t-il les attributs de sécurité indispensables (`HttpOnly`, `Secure`, `SameSite`) ?
4. Vérifier l'absence d'exposition excessive de données sensibles dans la réponse de login.

---

## 🔬 Protocole d'Examen dans OWASP ZAP

### 1. Sélectionner la requête dans l'Historique
* Dans ZAP, cliquer sur la ligne **ID 29 (`POST /api/auth/login`)**.
* Regarder les panneaux centraux : **Requête (Request)** et **Réponse (Response)**.

---

## 📋 Checklist d'Audit

### A. La Requête (Ce qui part vers le serveur)
- [ ] Le mot de passe est-il transmis en clair dans le JSON ou chiffré dans le transport (HTTPS obligatoire) ?
- [ ] Y a-t-il un jeton anti-CSRF ou des en-têtes personnalisés ?

### B. La Réponse (Ce que le serveur renvoie)
- [ ] **Fuite de données sensibles :**
  * La réponse contient-elle uniquement un token et les infos publiques de l'utilisateur ?
  * Vérifier qu'il n'y a **aucun hash de mot de passe** (`password_hash`, `bcrypt`), aucun secret interne de base de données renvoyé.
- [ ] **Type de Session :**
  * **Cas 1 : Jeton dans le corps JSON (`{"token": "ey..."}`)**
    * Le frontend doit stocker ce token. *Où est-il stocké ?* (LocalStorage = vulnérable aux vols par XSS ; Cookie HttpOnly = plus résistant).
  * **Cas 2 : En-tête `Set-Cookie`**
    * Vérifier la présence du drapeau `HttpOnly` (empêche le vol du cookie via JavaScript/XSS).
    * Vérifier la présence du drapeau `Secure` (le cookie ne voyage que par HTTPS).
    * Vérifier `SameSite=Lax` ou `SameSite=Strict` (protection contre les attaques CSRF).

---

## 🧪 Exercice Pratique sur la Requête 29

1. Dans ZAP, cliquez sur la requête **29 (`POST /api/auth/login`)**.
2. Allez dans l'onglet **Réponse (Response)**.
3. Regardez les premières lignes du corps de réponse (Body).
4. Notez si vous voyez un cookie dans `Set-Cookie` ou un champ `token` / `access_token` dans le JSON.

