# 🛡️ Étape 15 : Test de Neutralisation des Caractères Spéciaux & XSS

Ce document consigne le protocole de vérification de l'échappement des données saisies dans les formulaires (OWASP Top 10 - A03: Injection / Cross-Site Scripting).

---

## 🎯 Objectif du Test (Point 11 du Plan d'Audit)

Vérifier si le formulaire de profil (ou tout champ texte éditable par l'élève) sépare correctement les données de l'affichage HTML et neutralise les caractères de balisage web :
`<`, `>`, `"`, `'`, `&`.

```text
Saisie utilisateur : "Papa Malick <test>"
         │
         ▼
Enregistrement en BDD : Texte brut
         │
         ▼
Rendu dans React / Vue :
  - Soit échappé automatiquement : &lt;test&gt;
  - Soit affiché comme chaîne de caractères littérale
  - JAMAIS interprété comme une vraie balise HTML active
```

---

## 🔬 Protocole Pratique (Dans le navigateur)

1. Dans le navigateur contrôlé par ZAP, rendez-vous sur la page **Mon Profil** / Paramètres de compte.
2. Modifiez votre **Nom** ou votre **Prénom** en ajoutant un délimiteur inoffensif, par exemple :
   ```text
   Papa Malick <test>
   ```
3. Cliquez sur **Enregistrer** (ou *Sauvegarder*).
4. Rafraîchissez la page (`F5`).

---

## 📊 Matrice d'Observation

| Comportement Observé | Signification Sécurité | Diagnostic |
|----------------------|------------------------|:----------:|
| Le nom s'affiche textuellement : `Papa Malick <test>` | Le framework (React/Vue) traite la valeur comme du texte pur et échappe les balises. | ✅ **Sécurisé (Pas de XSS)** |
| Le mot `<test>` disparaît de l'écran | Le navigateur a interprété le texte comme une balise HTML invisible ! | ⚠️ **Vulnérabilité XSS potentielle** |
| Le serveur rejette la saisie avec une erreur de validation | Le backend filtre strictement les caractères spéciaux à l'entrée. | ✅ **Sécurisé** |

