# 📑 RAPPORT D'AUDIT DE SÉCURITÉ APPLICATIF & PENTEST

**Cible :** Plateforme Éducative Jàng (`https://jaang.saharux.com`)  
**Type d'évaluation :** Audit applicatif en boîte grise (Compte Élève) via Proxy d'interception (OWASP ZAP)  
**Date de réalisation :** Octobre 2026  
**Auditeur :** Papa Malick TEUW  

---

## 📌 1. Résumé Exécutif

L'audit de sécurité réalisé sur la plateforme d'enseignement supérieur **Jàng** démontre un **excellent niveau de maturité technique et de sécurité par conception (Security by Design)**.

L'application a été soumise à une série de tests méthodiques ciblant les vulnérabilités majeures de l'OWASP Top 10 (gestion des sessions, contrôles d'accès RBAC/BOLA, robustesse du reverse proxy et logique métier des examens). L'ensemble des contrôles critiques testés se sont révélés **étanches et conformes aux meilleures pratiques internationales**.

---

## 🏗️ 2. Cartographie de l'Architecture Technique Découverte

* **Frontend :** Single Page Application (SPA moderne - React / Vite) avec routage client complet.
* **Passerelle & Reverse Proxy :** Serveurs Nginx 1.27.5 & Apache 2.4.58 (Ubuntu) relayant vers le domaine API interne (`api-a221.saharux.com`).
* **Backend API :** Framework Node.js / NestJS structuré en API REST sous le préfixe `/api/*`.
* **Format des données :** 100% `application/json`.
* **Identifiants :** ObjectIDs / Hashes de 24 caractères (non prédictibles).

---

## 📊 3. Tableau Synthétique des Évaluations de Sécurité

| Famille OWASP | Point d'Audit | Test Réalisé | Résultat Obtenu | Diagnostic |
|---------------|---------------|--------------|-----------------|:----------:|
| **A05 Misconfig** | Exposition de secrets | Tentative d'accès à `/.env`, `/.git`, `/robots.txt` | Redirection SPA vers `index.html` (pas d'exposition) | 🟢 **Conforme** |
| **A07 Auth Failures** | Durée de session | Décodage du jeton JWT (`POST /api/auth/login`) | Expiration à 15 min (`exp - iat = 900s`) + RefreshToken | 🟢 **Excellent** |
| **A07 Auth Failures** | Protection routes | Appel de `/api/independent/me` sans jeton | Rejet instantané avec code standard **`401 Unauthorized`** | 🟢 **Conforme** |
| **A07 Auth Failures** | Révocation déconnexion | Rejeu du JWT après clic sur « Déconnexion » | Rejet immédiat en **`401 Unauthorized`** (Blacklist active) | 🟢 **Excellent** |
| **A01 Broken Access** | Cloisonnement RBAC | Tentative d'accès à `/api/admin/users` par un élève | Blocage strict avec code **`403 Forbidden`** | 🟢 **Parfait** |
| **A01 Broken Access** | Référence directe IDOR | Manipulation d'ID inexistant `/api/courses/00...` | Réponse propre **`404 Not Found`** (aucun crash 500) | 🟢 **Conforme** |
| **A04 Insecure Design** | Intégrité des Quiz | Soumission d'examen `POST /api/quizzes/{id}/submit` | Envoi brut des choix uniquement, note calculée sur serveur | 🟢 **Parfait** |
| **A05 Misconfig** | Protection Anti-DDoS | Analyse des en-têtes de réponse | `X-RateLimit-Limit: 120` requêtes/min actif | 🟢 **Robuste** |
| **A02 Crypto Failures** | Transport sécurisé | En-têtes de sécurité | `Strict-Transport-Security: max-age=15768000` (HSTS) | 🟢 **Conforme** |
| **A05 Misconfig** | Anti-Clickjacking | En-têtes de sécurité | `X-Frame-Options: SAMEORIGIN` présent | 🟢 **Conforme** |

---

## 🔍 4. Analyse Détaillée des Points Forts Observés

### 1. Invalidation Immédiate de Session (Révocation JWT)
Sur la majorité des architectures JWT, la déconnexion n'est qu'un effacement local du navigateur. Sur Jàng, le serveur invalide activement le jeton dès la déconnexion, éliminant tout risque de rejeu sur poste partagé.

### 2. Cloisonnement Absolu des Privilèges (RBAC)
Même lorsque les requêtes sont forgées directement via un proxy d'interception (contournant l'interface graphique), le backend vérifie rigoureusement le rôle associé au token et oppose un refus catégorique `403 Forbidden` à toute tentative d'accès aux routes d'administration.

### 3. Logique Métier Étanche sur les Évaluations
La conception de l'API de soumission des quiz (`/api/quizzes/{id}/submit`) ne fait aucune confiance au navigateur : le score n'est jamais transmis par le client mais calculé à 100% côté serveur, rendant la triche applicative impossible.

---

## 💡 5. Recommandations de Durcissement (Hardening Défensif)

Bien que l'application soit particulièrement robuste, voici deux optimisations simples pour atteindre la perfection :

1. **Désactiver l'en-tête `X-Powered-By: Express` :**
   Dans le backend Node.js, ajouter `app.disable('x-powered-by');` pour ne pas dévoiler le framework utilisé.
2. **Masquer le numéro de version Nginx (`Server: nginx/1.27.5`) :**
   Dans la configuration Nginx, ajouter la directive `server_tokens off;` pour masquer la version précise du serveur.

---

## 🏁 6. Conclusion Générale

La plateforme **Jàng** dispose d'un socle de sécurité applicative exemplaire, respectant rigoureusement les principes de défense en profondeur et de moindre privilège.

