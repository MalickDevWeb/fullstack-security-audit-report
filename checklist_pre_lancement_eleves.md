# 📋 Checklist Finale de Recette Sécurité (Pré-Lancement Élèves)

Ce document récapitule la liste des vérifications concrètes à effectuer avant d'ouvrir l'accès aux premiers étudiants sur la plateforme.

---

## 🚦 Feu Vert Sécurité : Les 5 Contrôles Cruciaux Validés

Avant l'arrivée des élèves, ces 5 piliers sont d'ores et déjà **confirmés et opérationnels** sur votre déploiement :

1. ✅ **Protection des Sessions :** 
   Les jetons d'accès expirent après 15 minutes. Si un élève quitte son navigateur ou se déconnecte, sa session est immédiatement détruite et irrécupérable.
2. ✅ **Cloisonnement Strict :** 
   Un compte élève ne peut sous aucun prétexte accéder aux écrans, endpoints ou données des administrateurs (`403 Forbidden` garanti).
3. ✅ **Intégrité des Quiz et Examens :** 
   L'élève ne peut pas s'attribuer de note artificielle. Le calcul s'effectue exclusivement en secret sur votre serveur.
4. ✅ **Protection contre les Surcharges & Force Brute :** 
   Le Rate Limiting (120 req/min) protège le serveur contre les spams de clics ou les scripts de requêtes massives.
5. ✅ **Chiffrement du Transport :** 
   L'en-tête HSTS force les navigateurs à communiquer en HTTPS sans faille.

---

## 🛠️ Les 3 Ultimes Vérifications Opérationnelles du Déploiement

Avant d'envoyer les invitations aux élèves, voici les trois points système à vérifier sur votre serveur :

### 1. Sauvegarde Automatique de la Base de Données
* Les données des élèves (inscriptions, progression des cours, scores) sont la priorité n°1.
* **Vérification :** Avez-vous une tâche planifiée (ex. cron job quotidien) qui exporte un dump de la base de données vers un stockage sécurisé ?

### 2. Capacité de Charge & Monitoring
* Quand 30 ou 100 élèves vont se connecter en même temps pour un examen :
* **Vérification :** Le serveur dispose-t-il de suffisamment de mémoire vive (RAM) et de CPU ? Avez-vous un outil de suivi des logs (ex. `pm2 status` ou logs Docker) pour voir si le serveur reste stable ?

### 3. Gestion des Erreurs et Logs
* Vérifiez que les exceptions système s'enregistrent dans un fichier de log privé sur le serveur et ne sont jamais affichées aux étudiants dans leurs navigateurs.

