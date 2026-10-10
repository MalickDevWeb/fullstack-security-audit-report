# 🛡️ Étape 16 : Audit des Zones Interactives (Recherche, IA & Exercices)

Ce document détaille la méthodologie d'audit sur les 4 points d'entrée interactifs identifiés sur la plateforme Jàng.

---

## 🎯 Les 4 Surfaces d'Attaque Interactives

```text
Plateforme Jàng (Élève)
   ├── 1. Barre de Recherche (Test de syntaxe & Reflected XSS)
   ├── 2. Tuteur IA / Chatbot (Robustesse du prompt & Échappement Markdown)
   ├── 3. Réponses aux Exercices & Quiz (Validation de format & logique métier)
   └── 4. Commentaires de Leçon (Test de persistance Stored XSS)
```

---

## 🔬 Protocole 1 : La Barre de Recherche (Le plus rapide)

### Le Test :
1. Dans la barre de recherche des cours, tapez exactement :
   ```text
   flutter' " <test>
   ```
2. Appuyez sur **Entrée**.
3. **Ce qu'il faut observer :**
   * Est-ce que le site affiche : *« Aucun résultat pour flutter' " <test> »* ?
   * **Critère de sécurité :** Si les caractères `' " <test>` sont réaffichés à l'écran de manière propre et textuelle (sans faire planter la page ni faire disparaître `<test>`), la recherche est parfaitement sécurisée contre les injections de syntaxe et le XSS réfléchi.

---

## 🔬 Protocole 2 : Le Tuteur IA / Chatbot

### Le Test :
1. Ouvrez le Tuteur IA.
2. Envoyez-lui une question contenant des guillemets et des balises, par exemple :
   ```text
   Explique-moi la différence entre <Widget> et "State" en Dart ?
   ```
3. **Critère de sécurité :**
   * L'IA doit répondre normalement en expliquant le concept.
   * L'interface de discussion (chat) doit afficher vos balises dans des blocs de code ou du texte brut sans aucune anomalie graphique.

