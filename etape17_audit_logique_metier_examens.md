# 🛡️ Étape 17 : Audit des Réponses aux Examens & Quiz (Logique Métier)

Ce document consigne le protocole de vérification de l'intégrité des résultats d'examens et de quiz (OWASP Top 10 - A04: Insecure Design & Business Logic Flaws).

---

## 🎯 Objectif du Test (Point 13 du Plan d'Audit)

Vérifier si le calcul des notes et des validations de cours s'effectue **côté client (navigateur)** ou **côté serveur (backend sécurisé)** :

```text
Cas A (Vulnérable) :
Client calcule le score ──▶ Envoie {"score": 20, "passed": true} ──▶ Serveur enregistre aveuglément

Cas B (Sécurisé & Conforme) :
Client envoie les choix ──▶ {"answers": [1, 3, 2]} ──▶ Serveur calcule & valide le score
```

---

## 🔬 Les Contrôles à Réaliser

1. **Calcul des scores côté Serveur :**
   * L'API ne doit jamais accepter un score ou une note brute directement envoyée par le navigateur.
   * C'est le backend qui doit comparer les réponses avec le barème secret en base de données.
2. **Rejeu de soumission :**
   * Une fois un examen ou un quiz validé, le serveur doit refuser les soumissions ultérieures pour la même tentative.
3. **Validation des types de réponses :**
   * Si une question attend un entier (numéro de choix `0`, `1`, `2`), soumettre un tableau ou un texte invalide doit être rejeté proprement (`400 Bad Request`).

