# 🛡️ Étape 11 : Protection WAF / Reverse Proxy contre les Requêtes Forgees

Ce document analyse le comportement des couches de passerelle web (Apache/Nginx) en amont de l'API.

---

## 🔍 Constat Technique sur l'Erreur 400

Lors de l'envoi d'une requête `POST` forgée manuellement dans ZAP :
```text
Client (ZAP Requêteur) ──▶ Nginx (Front) ──▶ Apache (Proxy) ──▶ Express (API)
                                                ▲
                                                │
                              Bloqué ici : 400 Bad Request
```

### 1. Pourquoi Apache bloque la requête manuelle :
Quand vous copiez-collez du texte dans l'éditeur brut de ZAP, les sauts de lignes sous Windows utilisent souvent le format `\r\n` (CRLF) qui peut corrompre le décompte d'octets ou le formatage strict des en-têtes d'Apache.
Apache 2.4.58 applique la directive `HttpProtocolOptions Strict`, qui rejette toute requête dont les délimiteurs HTTP ne sont pas rigoureusement conformes.

### 2. Le Bénéfice de Sécurité (Défense en Profondeur) :
C'est un excellent comportement défensif :
* **Protection contre le HTTP Request Smuggling :** En rejetant les requêtes suspectes ou mal formées au niveau du Reverse Proxy, Apache protège l'application Express backend contre les attaques de désynchronisation de requêtes.
* Le backend applicatif ne traite même pas les requêtes non conformes.

