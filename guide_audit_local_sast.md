# 🛡️ Guide Pratique : Réaliser un Audit SAST & Dépendances en Local

Ce document détaille les étapes pratiques pour analyser le code source et les dépendances d'un projet web sur votre machine.

---

## 🧭 Prérequis Disponibles sur votre Machine

* **Node.js :** Version `v24.15.0` installée et opérationnelle.
* **npm :** Gestionnaire de paquets prêt à l'emploi.

---

## 🔬 1. Audit Rapide des Dépendances (SCA) avec `npm audit`

Si vous possédez le dossier source de votre projet (frontend ou backend contenant un fichier `package.json`) :

### Commande :
Ouvrez votre terminal dans le dossier du projet et lancez :
```bash
npm audit
```

### Que fait cette commande ?
1. Elle compare la liste exacte de toutes vos bibliothèques installées avec la base de données officielle des vulnérabilités de sécurité du registre npm.
2. Elle classe les résultats par gravité :
   * **Low / Moderate :** Risques mineurs.
   * **High / Critical :** Vulnérabilités sévères à corriger immédiatement avant la production.

### Comment corriger automatiquement ?
```bash
npm audit fix
```
*(Met à jour les paquets vulnérables vers leurs versions de correctif sans casser la compatibilité).*

---

## 🔬 2. Analyse Statique Automatisée (SAST) avec Semgrep

**Semgrep** est l'un des scanners de code les plus réputés pour inspecter le code JavaScript, TypeScript, React et NestJS.

### Utilisation directe via `npx` (Sans installation lourde) :
Puisque vous avez Node.js, vous pouvez lancer une analyse ponctuelle sans installer Semgrep de façon permanente :
```bash
# Dans le dossier de votre code source
npx @semgrep/cli scan --config auto
```

### Que détecte Semgrep lors de son analyse ?
* Les variables non assainies qui entrent dans des requêtes de base de données.
* Les routes d'API où la vérification d'authentification a été oubliée.
* Les clés privées ou secrets codés en dur dans les fichiers `.js` / `.ts`.
* Les mauvaises configurations de CORS ou de cookies.

