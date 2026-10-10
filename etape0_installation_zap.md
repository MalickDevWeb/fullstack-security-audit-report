# 📘 Guide d'Installation : Java 17 & OWASP ZAP sous Windows

Ce guide récapitule toutes les étapes nécessaires pour installer et réinstaller l'environnement de proxy local sans erreur.

---

## ☕ Étape 1 : Installation de Java 17 (Prérequis obligatoire)

OWASP ZAP est développé en Java et requiert au minimum **Java 17 (JRE/JDK 64-bit)** pour s'exécuter.

### 1. Téléchargement
* **Source officielle recommandée :** Eclipse Adoptium (Temurin).
* **Lien direct :** [OpenJDK 17 (Windows x64 .msi)](https://github.com/adoptium/temurin17-binaries/releases/download/jdk-17.0.12+7/OpenJDK17U-jdk_x64_windows_hotspot_17.0.12_7.msi)

### 2. Paramétrage lors de l'installation
* Lancer le fichier `.msi`.
* Si demandé, choisir : **« Installer juste pour vous (votre nom d'utilisateur) »**.
* **Points d'attention dans la liste des composants :**
  * Activer l'installation de : **« Définir la variable JAVA_HOME »** (remplacer la croix rouge par *Installé sur le disque local*).
  * Activer l'installation de : **« Clés de registre JavaSoft (Oracle) »**.
* Terminer l'installation.
* **Chemin par défaut d'installation :**
  `C:\Users\<VotreNom>\AppData\Local\Programs\Eclipse Adoptium\jdk-17.0.12.7-hotspot\bin\java.exe`

---

## ⚡ Étape 2 : Installation d'OWASP ZAP

### 1. Téléchargement
* **Site officiel :** [https://www.zaproxy.org/download/](https://www.zaproxy.org/download/)
* **Version Windows :** `Windows (64) Installer` (ex. `ZAP_2_17_0_windows.exe`).

### 2. Gestion de l'alerte Antivirus (Faux Positif)
* Windows Defender ou le navigateur peut marquer le fichier avec l'avertissement *« Virus détecté »*.
* **Raison :** ZAP est un outil de test de sécurité (classé comme *HackTool / Riskware* par les signatures antivirus génériques).
* **Action :** 
  * Ouvrir **Sécurité Windows** > **Protection contre les virus et menaces** > **Historique de protection**.
  * Retrouver le blocage lié à `ZAP_2_..._windows.exe`.
  * Cliquer sur **Actions** > **Autoriser sur l'appareil**.

### 3. Liaison avec Java (si l'installateur demande `java.exe`)
* Si l'assistant `install4j` affiche : *« L'assistant n'a pu localiser aucun Java Runtime... »* :
  1. Cliquer sur le bouton **Localisation**.
  2. Naviguer vers le dossier de Java :
     `C:\Users\<VotreNom>\AppData\Local\Programs\Eclipse Adoptium\jdk-17.0.12.7-hotspot\bin`
  3. Sélectionner le fichier **`java.exe`** et cliquer sur **Ouvrir**.

### 4. Finalisation
* Choisir le type d'installation : **« Installation standard »**.
* Cliquer sur **Suivant** jusqu'à la fin de l'installation.

---

## 🚀 Étape 3 : Premier Démarrage de ZAP

1. Ouvrir le menu Démarrer de Windows et lancer **ZAP** (Zed Attack Proxy).
2. À la question *"Do you want to persist the ZAP Session?"* :
   * Sélectionner : **`No, I do not want to persist this session at this moment in time`**.
   * Cliquer sur **Start**.
3. Dans l'onglet principal **Démarrage rapide (Quick Start)** :
   * Cliquer sur l'icône verte **Exploration Manuelle (Manual Explore)**.
   * Renseigner l'URL cible (ex. `https://jaang.saharux.com/`).
   * Cliquer sur **Lancer le navigateur (Launch Browser)**.
4. Le navigateur s'ouvre avec le bandeau d'avertissement : *« Chrome est contrôlé par un logiciel de test automatisé »*, confirmant que tout le flux réseau est intercepté et consigné dans ZAP.

