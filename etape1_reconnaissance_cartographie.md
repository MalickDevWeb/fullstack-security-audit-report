# 🗺️ Étape 1 & 2 : Reconnaissance & Cartographie des Endpoints API

Ce document détaille la méthodologie pour observer, cartographier et analyser le comportement d'une application web à l'aide d'un proxy d'interception.

---

## 🎯 Objectif de l'étape

Avant tout audit de sécurité, il est impératif de comprendre l'architecture technique de la cible sans chercher à l'attaquer :
1. Identifier les technologies front/back.
2. Lister tous les points de contact (endpoints API REST / GraphQL).
3. Analyser la façon dont les jetons d'authentification (JWT, Cookies) transitent.
4. Repérer la structure des identifiants (IDs numériques, UUIDs) utilisés dans les requêtes.

---

## 🛠️ Protocole d'Exécution avec ZAP

```text
1. Lancer ZAP (Mode Exploration Manuelle)
         │
         ▼
2. Ouvrir le navigateur contrôlé par ZAP
         │
         ▼
3. Se connecter avec le compte de test (Élève)
         │
         ▼
4. Parcourir exhaustivement toutes les sections
   (Profil, Cours, Formulaires, Téléchargements)
         │
         ▼
5. Analyser dans ZAP :
   ├── Onglet Sites (Arborescence des routes)
   └── Onglet Historique (Détail Requête / Réponse)
```

---

## 🔍 Que rechercher dans l'onglet Historique de ZAP ?

Lors de l'analyse d'une requête spécifique (ex. `POST /api/auth/login` ou `GET /api/user/profile`) :

### 1. Dans la Requête (Request)
* **Méthode HTTP :** Est-ce un `GET`, un `POST`, un `PUT`, ou un `DELETE` ?
* **En-têtes d'autorisation :**
  * Y a-t-il un en-tête `Authorization: Bearer <TOKEN>` ?
  * Y a-t-il un cookie de session ? Si oui, possède-t-il les flags `HttpOnly` et `Secure` ?
* **Paramètres transmis :**
  * Y a-t-il des paramètres d'URL (`?user_id=123`) ?
  * Y a-t-il un corps JSON contenant un rôle ou des permissions (`{"role": "student"}`) ?

### 2. Dans la Réponse (Response)
* **Code HTTP de retour :** `200 OK`, `401 Unauthorized`, `403 Forbidden`, `500 Internal Error`.
* **Fuite d'informations (Excessive Data Exposure) :**
  * Le serveur renvoie-t-il des champs non demandés par l'écran (ex. `password_hash`, informations d'autres utilisateurs, logs d'erreur détaillés) ?
* **En-têtes de sécurité :**
  * Présence ou absence des en-têtes défensifs recommandés :
    * `Strict-Transport-Security` (HSTS)
    * `Content-Security-Policy` (CSP)
    * `X-Frame-Options` (Protection anti-Clickjacking)
    * `X-Content-Type-Options: nosniff`

---

## 📝 Fiche de Relevé : Endpoints Réels Découverts

Voici la cartographie issue de la capture OWASP ZAP :

| ID ZAP | Méthode | Endpoint découvert | Type de données | Rôle identifié |
|--------|---------|---------------------|-----------------|----------------|
| **29** | `POST` | `/api/auth/login` | JSON | Authentification (envoi email / mot de passe) |
| **32** | `GET` | `/api/announcements/...` | JSON | Récupération des annonces de la plateforme |
| **35** | `GET` | `/api/independent/me` | JSON | Données privées de l'élève connecté |
| **37** | `GET` | `/api/courses` | JSON | Liste de l'ensemble des cours |
| **51** | `GET` | `/api/lessons/{id}` | JSON | Détail d'une leçon (identifiant type hash/UUID) |
| **52** | `GET` | `/api/courses/{id}` | JSON | Détail d'un cours spécifique |
| **56** | `POST` | `/api/engagement/time` | JSON (`201 Created`) | Télémétrie / temps passé sur la plateforme |

---

## 💡 Analyse d'Architecture (Ce que ces captures nous révèlent)

1. **Architecture découplée (SPA + API REST) :**
   * Le front-end statique appelle une API dédiée sous `/api/*`.
2. **Format des données :**
   * L'API communique exclusivement en `application/json`.
3. **Format des identifiants (IDs) :**
   * Regardez les requêtes 51 et 52 : les IDs des leçons et cours ressemblent à des hachages (`6a92d0...`, `6a92c6...`). Ce ne sont pas de simples entiers (`1, 2, 3`), ce qui rend la devinette d'identifiants (brute-force d'IDOR) plus difficile par énumération séquentielle.


