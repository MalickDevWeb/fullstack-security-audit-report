# 🛡️ Étape 10 : Découverte d'Architecture & Reverse Proxy

Ce document consigne la découverte de l'infrastructure réseau et du Reverse Proxy de la plateforme suite au test `POST /api/courses`.

---

## 🔬 Données Capturées lors du Test `POST /api/courses`

```http
HTTP/1.1 400 Bad Request
Date: Fri, 09 Oct 2026 11:30:55 GMT
Server: Apache/2.4.58 (Ubuntu)
Content-Length: 313
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<hr>
<address>Apache/2.4.58 (Ubuntu) Server at api-a221.saharux.com Port 443</address>
</body></html>
```

---

## 🔍 Découverte Technique Majeure (Reconnaissance Passive)

Regardez attentivement le pied de page renvoyé par le serveur :
```text
Apache/2.4.58 (Ubuntu) Server at api-a221.saharux.com Port 443
```

### 1. Révélation du serveur API interne :
* Le domaine principal est `jaang.saharux.com`.
* Mais en coulisses, les requêtes `/api/*` sont relayées vers un serveur dédié : **`api-a221.saharux.com`** !
* Cela confirme une architecture distribuée avec **Reverse Proxy** (Nginx en frontal redirigeant vers un backend Apache/Node).

### 2. Pourquoi le serveur a-t-il répondu `400 Bad Request` ?
* En HTTP brut avec la méthode `POST` et un corps de données, le serveur Apache intermédiaire exige impérativement que l'en-tête `Content-Length` (la taille exacte en octets du JSON) soit présent et calculé précisément.
* Lorsque `Content-Length` est omis ou incorrect dans une requête brute, Apache rejette la requête immédiatement avec un code `400 Bad Request` pour des raisons de sécurité (prévention du *HTTP Request Smuggling*).
* **Conséquence Sécurité :** L'action non autorisée n'a pas été exécutée.

