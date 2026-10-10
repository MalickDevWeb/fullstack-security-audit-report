# 🛡️ Étape 6 : Audit des En-têtes de Sécurité & Rate Limiting

Ce document consigne l'analyse technique des en-têtes HTTP de réponse renvoyés par l'API backend (`https://jaang.saharux.com/api/independent/me`).

---

## 📋 En-têtes HTTP de Réponse Capturés

```http
HTTP/1.1 200 OK
Date: Fri, 09 Oct 2026 10:46:57 GMT
Server: nginx/1.27.5
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Referrer-Policy: strict-origin-when-cross-origin
Strict-Transport-Security: max-age=15768000
X-Powered-By: Express
Vary: Origin
Access-Control-Allow-Credentials: true
X-RateLimit-Limit: 120
X-RateLimit-Remaining: 119
X-RateLimit-Reset: 60
Content-Length: 0
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
```

---

## 🔍 Analyse & Bilan de Sécurité

### 🟢 1. Excellentes Défenses Identifiées

1. **Protection Anti-DDoS & Brute-force (`Rate Limiting`) :**
   * `X-RateLimit-Limit: 120`
   * `X-RateLimit-Remaining: 119`
   * `X-RateLimit-Reset: 60`
   * **Verdict :** **Très bon point de sécurité.** L'API limite chaque utilisateur à un quota strict (120 requêtes maximum par minute). Cela empêche les attaques par déni de service et les tentatives d'énumération automatisées.

2. **Chiffrement Garanti (`HSTS`) :**
   * `Strict-Transport-Security: max-age=15768000`
   * **Verdict :** Force les navigateurs à n'utiliser que le protocole HTTPS pendant 6 mois, protégeant contre les attaques de dégradation de protocole (SSL Stripping).

3. **Protection contre le Clickjacking :**
   * `X-Frame-Options: SAMEORIGIN`
   * **Verdict :** Empêche un site externe malveillant d'intégrer votre application dans une `<iframe>` invisible pour piéger l'utilisateur.

4. **Protection contre le reniflage de type MIME :**
   * `X-Content-Type-Options: nosniff`
   * **Verdict :** Empêche le navigateur de deviner ou d'exécuter un fichier dont le type MIME serait trompeur.

---

### 🟠 2. Pistes d'Amélioration / Recommandations Défensives

1. **Suppression de l'en-tête de divulgation de technologie :**
   * `X-Powered-By: Express`
   * **Recommandation :** Dans votre code backend Node.js / Express, désactivez cet en-tête pour ne pas révéler aux attaquants quel framework vous utilisez :
     ```javascript
     app.disable('x-powered-by');
     // ou via le middleware Helmet :
     app.use(helmet.hidePoweredBy());
     ```

2. **Masquage de la version Nginx :**
   * `Server: nginx/1.27.5`
   * **Recommandation :** Dans la configuration de votre serveur Nginx (`nginx.conf`), ajoutez :
     ```nginx
     server_tokens off;
     ```
     Cela masquera le numéro de version précis (`1.27.5`), limitant les informations données aux scanners automatisés.

