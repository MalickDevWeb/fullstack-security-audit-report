# 📘 Documentation : OWASP ZAP (Zed Attack Proxy)

## 📌 Qu'est-ce que c'est ?
**OWASP ZAP** est un proxy d'interception HTTP/HTTPS open-source développé par la fondation OWASP. Il agit comme un intermédiaire entre votre navigateur web et le serveur applicatif.

---

## 🎯 Quel est son rôle principal ?

```text
Navigateur (Client) ───▶ [ OWASP ZAP ] ───▶ Serveur Web / API
                              ▲
                    (Interception & Analyse)
```

1. **Voir l'invisible (Cartographie API) :**
   Capture toutes les requêtes en arrière-plan (AJAX, Fetch, appels REST/GraphQL, tokens, cookies).
2. **Intercepter et modifier (Tampering) :**
   Mettre une requête en pause pour modifier les paramètres avant leur arrivée sur le serveur (tester la robustesse de la validation des données).
3. **Tester les autorisations (IDOR / BOLA) :**
   Rejouer des requêtes avec des identifiants différents pour vérifier si un utilisateur peut accéder aux données d'un autre utilisateur.
4. **Analyse passive de sécurité :**
   Inspecter les en-têtes HTTP de réponse (ex. `Content-Security-Policy`, `X-Frame-Options`) et les cookies pour repérer les mauvaises configurations courantes.

---

## 🚀 Guide de démarrage rapide

### 1. Démarrage
* Lancer ZAP.
* À la question *"Do you want to persist the ZAP Session?"*, choisir :
  `No, I do not want to persist this session at this moment in time` puis cliquer sur **Start**.

### 2. Exploration Manuelle (Manual Explore)
* Dans l'onglet d'accueil **Quick Start**, cliquer sur **Manual Explore**.
* Entrer l'adresse de l'application à analyser (ex. votre environnement de test ou un lab dédié comme OWASP Juice Shop).
* Choisir le navigateur (Chrome ou Firefox) et cliquer sur **Launch Browser**.
* Le navigateur s'ouvre : toute navigation effectuée dedans passe automatiquement par ZAP.

### 3. Les onglets essentiels à observer
* **Sites (à gauche) :** Arborescence hiérarchique de tous les dossiers et endpoints découverts sur le domaine.
* **History (en bas) :** Journal chronologique de chaque requête HTTP (code retour, méthode `GET`/`POST`, URL, taille).
* **Request / Response (au centre) :** Détail brut des en-têtes (Headers) et du corps (Body JSON/HTML) envoyé et reçu.
* **Alerts (en bas) :** Liste des avertissements de sécurité détectés automatiquement (ex. cookies sans attribut `HttpOnly` ou `Secure`).

---

## 💡 Règle d'or de l'audit
L'outil ne remplace pas l'analyse humaine : son objectif premier est de vous donner une visibilité totale sur les échanges réseau afin de comprendre la logique métier et la gestion des droits d'accès.

