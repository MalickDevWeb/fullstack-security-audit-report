# 🔬 Analyse Réelle : Session & Authentification JWT

Ce document consigne l'analyse technique de la réponse d'authentification obtenue sur l'application.

---

## 📦 Données Réelles Capturées (`POST /api/auth/login`)

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "6ac0f29670a2dad1f3af9014",
    "name": "Papa Malick TEUW",
    "email": "malickteuw.devweb@gmail.com",
    "role": "student",
    "permissions": [],
    "suspended": false,
    "level": "débutant",
    "classIds": ["6a42dcfe15b848002c373fab"],
    "createdAt": "2026-10-03T12:18:30.992Z"
  }
}
```

---

## 🔍 Décryptage & Audit de Sécurité

### 1. Analyse du Jeton (`accessToken`)
Le jeton est un **JWT (JSON Web Token)** composé de 3 parties séparées par des points (`Header.Payload.Signature`) :

* **En-tête (Header) :**
  ```json
  {"alg": "HS256", "typ": "JWT"}
  ```
  * Algorithme : `HS256` (HMAC avec SHA-256). C'est un algorithme à clé secrète symétrique standard.
* **Contenu (Payload) :**
  ```json
  {
    "sub": "6ac0f29670a2dad1f3af9014",
    "role": "student",
    "email": "malickteuw.devweb@gmail.com",
    "permissions": [],
    "classIds": ["6a42dcfe15b848002c373fab"],
    "iat": 1791542816,
    "exp": 1791543716
  }
  ```

---

## 🛡️ Constats de l'Audit

### ✅ Points Positifs
1. **Durée de vie courte du jeton d'accès (`exp - iat`) :**
   * `1791543716 - 1791542816 = 900 secondes` (soit **15 minutes**).
   * **Verdict :** Excellente pratique de sécurité. Si un token d'accès est intercepté, sa validité expire très vite.
2. **Présence d'un Refresh Token :**
   * Un `refreshToken` plus long est fourni pour renouveler l'accès sans redemander le mot de passe.
3. **Pas de fuite de mot de passe :**
   * L'objet `user` ne renvoie aucun hash (`password_hash` / `salt`), ce qui est conforme aux bonnes pratiques OWASP.
4. **Identifiants non prédictibles :**
   * L'ID utilisateur (`6ac0f29670a2dad1f3af9014`) est un ObjectId/Hex de 24 caractères, évitant l'énumération séquentielle simple (`1, 2, 3`).

---

## ⚠️ Points d'Attention / Risques à Vérifier

1. **Stockage du Jeton côté Client (Front-end) :**
   * Le token est renvoyé directement dans le corps JSON (et non dans un cookie avec le flag `HttpOnly`).
   * *Conséquence :* Le frontend (React/Vue) doit le stocker (dans `localStorage` ou `sessionStorage`). Si le site venait à comporter une faille **XSS (Cross-Site Scripting)**, un script malveillant pourrait lire `localStorage.getItem('token')` et exfiltrer la session.
2. **Contrôle du Rôle côté Serveur (Principe de Confiance Zéro) :**
   * Le payload contient `"role": "student"`.
   * **Règle fondamentale :** Le backend doit vérifier la signature cryptographique du JWT à chaque requête et **ne jamais faire confiance à ce que le navigateur déclare** sans validation stricte.

