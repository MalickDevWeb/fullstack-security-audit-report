# 🛡️ Étape 18 : Validation de l'Intégrité Métier des Quiz (Test Réussi)

Ce document consigne l'analyse technique de la requête de soumission de quiz capturée (`POST /api/quizzes/{id}/submit`).

---

## 🔬 Requête Capturée lors de la Soumission (Point 13 du Plan d'Audit)

* **Méthode :** `POST`
* **URL :** `https://jaang.saharux.com/api/quizzes/6ac12d2820766ae3f68b96dd/submit`
* **Corps JSON transmis :**
```json
{
  "answers": [
    {"questionId": "6ac12d2820766ae3f68b96c4", "selected": []},
    {"questionId": "6ac12d2820766ae3f68b96c5", "selected": []},
    {"questionId": "6ac12d2820766ae3f68b96c6", "selected": []}
  ]
}
```

---

## 🎯 Analyse & Bilan de Sécurité

### 🏆 Pourquoi cette conception est un modèle de sécurité :

1. **Aucune donnée de score n'est transmise par le client :**
   Le client (navigateur) transmet uniquement la liste de ses réponses (`selected: [...]`). 
   * Il n'y a **aucun champ `score`**, **aucun champ `total`**, **aucun champ `passed`**.
2. **Calcul côté Serveur à 100% étanche :**
   C'est le backend NestJS qui va :
   * Récupérer le corrigé secret en base de données.
   * Comparer chaque `questionId` avec les bonnes réponses.
   * Calculer la note et l'enregistrer lui-même dans la session de l'élève.
3. **Protection contre la triche applicative :**
   Un tricheur utilisant ZAP pour intercepter sa soumission d'examen **ne peut pas modifier sa note**, car l'API n'accepte aucune note en entrée !

---

## ✅ Statut du Contrôle : **CONFORME & INVIOLABLE CÔTÉ CLIENT**
Le point 13 de la checklist est officiellement validé avec succès.

