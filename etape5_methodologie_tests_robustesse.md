# 🧪 Guide Méthodologique : Tester la Résilience de son Application

Ce guide décrit la démarche d'évaluation de la robustesse de votre application de manière structurée et sécurisée.

---

## 🎯 Principe de Base : Le Test par les Cas aux Limites (Boundary Testing)

Tester la sécurité de sa propre application ne consiste pas à tenter de la casser au hasard, mais à vérifier que l'API applique strictement les règles métiers lorsqu'on lui soumet des données inattendues ou atypiques.

---

## 🔬 Les 3 Scénarios d'Évaluation Méthodique

### Scénario 1 : Validation des Formats et des Types
L'objectif est de vérifier que le backend valide la structure des données avant tout traitement.

1. **Données manquantes ou de type erroné :**
   * Que répond l'API si un champ numérique obligatoire reçoit du texte ou une valeur vide ?
   * *Comportement attendu :* Le serveur renvoie un code d'erreur clair `400 Bad Request` ou `422 Unprocessable Entity` indiquant une erreur de validation, **sans déclencher d'exception interne `500`**.
2. **Gestion des longueurs extrêmes :**
   * Un champ texte (ex. nom, bio) accepte-t-il des entrées de taille déraisonnable (ex. 10 000 caractères) ?
   * *Comportement attendu :* Le backend impose une limite maximale explicite pour préserver les ressources de stockage et de mémoire.

---

### Scénario 2 : Résilience aux Caractères Spéciaux
L'objectif est de s'assurer que le backend sépare strictement données et instructions.

1. **Présence de délimiteurs de syntaxe :**
   * Enregistrer un texte contenant des caractères usuels mais réservés en programmation (ex. apostrophes `'`, guillemets `"`, chevrons `<`, `>`, caractères Unicode ou accents).
2. **Observation du résultat :**
   * À la relecture de la ressource (dans le profil ou la page), les caractères sont-ils restitués fidèlement sans perturber l'affichage ?
   * La réponse du serveur est-elle toujours un document JSON sain ?

---

### Scénario 3 : Vérification des Contrôles d'Autorisation
L'objectif est de vérifier qu'un compte d'un niveau donné ne peut pas exécuter d'actions hors de son périmètre.

1. **Appel d'une ressource sans token :**
   * Si une requête vers une ressource privée (ex. `/api/independent/me`) est effectuée sans l'en-tête d'authentification :
   * *Comportement attendu :* Le serveur doit immédiatement répondre `401 Unauthorized`.
2. **Cohérence des données retournées :**
   * Lorsque vous demandez les informations de votre compte, vérifiez que le serveur ne renvoie aucune donnée relative aux autres étudiants enregistrés sur la plateforme.

---

## 📊 Matrice d'Observation : Résultats Réels Obtenus

| Test réalisé | Endpoint testé | Données modifiées | Code HTTP obtenu | Réponse du serveur | Conforme aux règles de sécurité ? |
|--------------|----------------|-------------------|-------------------|---------------------|-----------------------------------|
| **Contrôle d'accès sans session** | `/api/independent/me` | Suppression du header `Authorization` | **`401 Unauthorized`** | `{"statusCode":401,"code":"UNAUTHORIZED","message":"Jeton d'accès invalide ou expiré."}` | ✅ **OUI (Conforme)** |

---

## 🎯 Ce que ce résultat démontre (Analyse de Sécurité)

1. **Protection stricte des routes privées :**
   Le middleware d'authentification intercepte la requête avant même de toucher aux données métier.
2. **Gestion d'erreur propre et standardisée :**
   * Le code retour est rigoureusement conforme aux standards RFC (`401`).
   * Le corps JSON est structuré (`statusCode`, `code`, `message`).
   * **Aucune fuite de stack trace :** Le serveur ne dévoile aucune information interne sur le framework, la base de données ou le code source.


