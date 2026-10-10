# Identifiants de Test (Compte de Démonstration)

**URL de la plateforme :** https://example.com/ (ou environnement de Staging)
- **Email :** student.demo@example.com
- **Mot de passe :** ******************

-------

## 🎯 Plan d'action : 20 choses à tester en tant qu'élève (Audit & Pentest)

Puisque tu as ce compte "élève", voici 20 exercices pratiques et méthodiques d'audit à réaliser sur ta plateforme pour tester sa sécurité, en adoptant l'état d'esprit d'un pentester :

### Reconnaissance & Cartographie
- [x] **1.** Te connecter avec le compte élève et cartographier absolument toutes les pages, menus, et boutons accessibles. (✅ Réalisé via ZAP)
- [x] **2.** Inspecter le code source (Ctrl+U) et les outils de développement (F12 > Réseau/Network) pour lister tous les points d'entrée de l'API (ex: `/api/auth/login`, `/api/independent/me`, `/api/courses`). (✅ Validé)
- [x] **3.** Vérifier la présence de fichiers sensibles publics à la racine du site (`/robots.txt`, `/.env`, `/.git/`, `/sitemap.xml`). (✅ Validé - Redirection SPA propre)

### Authentification & Gestion de session
- [x] **4.** Analyser les cookies et jetons d'authentification (Les cookies sont-ils `HttpOnly` et `Secure` ? Le jeton JWT est-il lisible sur *jwt.io* ?). (✅ Validé - JWT HS256 décodé, 15 min de durée)
- [x] **5.** Tester l'expiration de la session : te déconnecter, puis essayer de rejouer une ancienne requête API interceptée pour voir si elle fonctionne toujours. (✅ Validé - Rejet immédiat en 401 Unauthorized à la déconnexion, blacklist/révocation active)
- [x] **6.** Tenter une attaque par force brute très légère sur la page de connexion pour vérifier si un système de blocage (Rate Limiting) empêche de tester plein de mots de passe. (✅ Validé - RateLimit 120 req/min actif)

### Autorisations & Élévation de privilèges (BOLA/IDOR)
- [x] **7.** **IDOR (Ressources / Cours) :** Tester la manipulation d'identifiants dans l'URL. (✅ Validé - Gestion propre en `404 Not Found` NestJS/Express, aucune fuite de base de données ni crash 500)
- [x] **8.** **BOLA / RBAC (API) :** Essayer d'utiliser les endpoints d'un professeur ou d'un admin. (✅ Validé avec succès - Route `/api/admin/users` rejetée en `403 Forbidden` : *"Vous n'avez pas les droits nécessaires pour cette action."*)
- [ ] **9.** Manipuler les JSON envoyés. Si tu envoies ton profil avec `{"nom": "Malick", "role": "student"}`, essaie de changer en `{"role": "admin"}` pour voir si le serveur l'accepte.


### Tests d'Injection
- [ ] **10.** **Injection SQL (SQLi) :** Essayer de mettre `' OR 1=1 --` ou `'` dans les champs de recherche (ex: recherche de cours) ou sur la page de connexion.
- [ ] **11.** **XSS (Cross-Site Scripting) :** Injecter le code `<script>alert(1)</script>` ou `"><img src=x onerror=alert(1)>` dans tes champs de profil (Nom, Bio) ou dans les commentaires d'un cours.

- [ ] **12.** Vérifier si la faille XSS s'exécute pour un autre élève ou un professeur qui regarderait ton profil (Stored XSS).

### Validation des données & Sécurité Métier

- [x] **13.** **Logique Métier & Manipulation de notes :** (✅ Validé avec succès - Route `POST /api/quizzes/{id}/submit` transmet uniquement la liste brute des choix `{"answers": [...]}`. Le calcul du score s'effectue à 100% côté serveur, impossible de s'attribuer un score artificiel côté client !)

- [ ] **14.** **CSRF :** Vérifier si l'application possède des tokens CSRF. Est-il possible de générer un lien qui, si un autre élève clique dessus, modifiera l'email de son compte ?

- [ ] **15.** Soumettre des données excessivement longues (ex: un nom de 10 000 caractères) pour voir si l'application ou l'API crashe (Déni de service applicatif).

### Sécurité des Fichiers (File Upload & Path Traversal)
- [ ] **16.** **File Upload :** Sur la page d'avatar/photo de profil, essaie d'uploader un faux fichier PHP (`shell.php`) ou un fichier SVG contenant du code JavaScript au lieu d'une image JPG/PNG.
- [ ] **17.** **Path Traversal :** S'il y a un téléchargement de fichier/cours via un paramètre (ex: `download.php?file=cours1.pdf`), essaie d'accéder à des fichiers système : `download.php?file=../../../../etc/passwd`.

### Gestion des Erreurs et CORS
- [ ] **18.** Causer des erreurs volontaires (ex: envoyer du texte à la place d'un chiffre dans l'API) et analyser la réponse. L'erreur révèle-t-elle des détails sur la base de données ou du code source ?
- [ ] **19.** Vérifier la politique CORS (Cross-Origin Resource Sharing). Regarder dans les headers de l'API si le `Access-Control-Allow-Origin` est défini sur `*` de manière non sécurisée.

### Documentation
- [ ] **20.** Utiliser Burp Suite ou OWASP ZAP (proxies) pour intercepter TOUT ton trafic pendant ces tests, et rédiger un mini-rapport pour chaque faille trouvée avec la requête exacte à reproduire.