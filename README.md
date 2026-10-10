# 🛡️ Fullstack Security Audit & Pentest Report

[![OWASP Top 10](https://img.shields.io/badge/OWASP-Top%2010%20Compliant-blue.svg)](https://owasp.org)
[![Security Review](https://img.shields.io/badge/Audit-Greybox%20Tested-green.svg)]()
[![Tools](https://img.shields.io/badge/Tools-OWASP%20ZAP%20%7C%20Burp%20Suite-orange.svg)]()
[![Backend](https://img.shields.io/badge/Stack-Node.js%20%7C%20NestJS%20%7C%20React-purple.svg)]()

> **Référentiel complet de méthodologie d'audit applicatif, de tests d'intrusion (DAST) et de vérifications de sécurité pré-production (Security by Design).**

---

## 📌 Présentation du Projet

Ce dépôt regroupe l'ensemble des livrables, études de cas et méthodologies d'audit de sécurité applicatif (AppSec) réalisés lors d'une évaluation en boîte grise sur une architecture moderne couplant une **Single Page Application (React)** et une **API REST (NestJS / Node.js)** protégée par Reverse Proxy.

L'objectif de cet audit est de valider la robustesse de l'application avant sa mise en production à travers les 10 catégories majeures de l'**OWASP Top 10**.

---

## 🎯 Compétences Techniques Démontrées

* **Reconnaissance & Cartographie DAST :** Interception, écoute passive et analyse dynamique du trafic réseau via **OWASP ZAP**.
* **Cycle de Vie & Sécurité des Sessions :** Décodage et audit cryptographique des jetons JWT (signature HS256, durée de vie courte de 15 min, mécanisme actif de révocation/blacklist à la déconnexion).
* **Contrôle d'Accès & RBAC :** Vérification de l'étanchéité des privilèges côté serveur (`403 Forbidden` immédiat sur les routes d'administration `/api/admin/*` appelées par un compte restreint).
* **Mitigation IDOR / BOLA :** Validation de la non-prédictibilité des identifiants (ObjectIDs) et gestion propre des erreurs `404 Not Found` sans fuite de stack trace ou d'exception de base de données.
* **Logique Métier Sécurisée :** Conception Zero Trust sur la soumission d'examens/quiz (calcul des scores hermétique à 100% côté serveur).
* **Durcissement d'Infrastructure (Hardening) :** Validation du Rate Limiting (120 req/min), transport HSTS forcé pour 6 mois, protections anti-clickjacking (`SAMEORIGIN`) et MIME (`nosniff`).

---

## 📁 Structure du Référentiel

| Fichier | Description |
|---------|-------------|
| 📑 **[`GRAND_RAPPORT_AUDIT_FINAL.md`](GRAND_RAPPORT_AUDIT_FINAL.md)** | Synthèse complète de l'audit et conclusions d'évaluation pré-production. |
| 🛡️ **[`skill.md`](skill.md)** | Skill méthodologique de référence et modèle mental du pentester. |
| 📋 **[`identifant.md`](identifant.md)** | Grille de recette pratique en 20 points de contrôle. |
| ⚙️ **[`owasp_zap.md`](owasp_zap.md)** | Guide de maîtrise et configuration du proxy d'interception OWASP ZAP. |
| 🚀 **[`checklist_pre_lancement_eleves.md`](checklist_pre_lancement_eleves.md)** | Checklist opérationnelle de déploiement et de mise en production. |
| 🔬 **`etape*.md`** | 18 rapports techniques détaillant chaque test unitaire réalisé avec preuves de requêtes HTTP. |

---

## 🛠️ Outils & Standards Utilisés

* **Interception & DAST :** OWASP ZAP (Zed Attack Proxy), Burp Suite
* **Analyse de Code & Normes :** OWASP Top 10, Semgrep (SAST), npm audit (SCA)
* **Serveurs & Réseau :** Nginx 1.27, Apache 2.4 (Reverse Proxy), Node.js, Express, NestJS

---

## 👤 Auteur

**Papa Malick TEUW** — Développeur Fullstack & Passionné de Sécurité Applicative  
* [GitHub Profile](https://github.com/MalickDevWeb)
