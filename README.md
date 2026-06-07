# 🔍 LAB 9 — Analyse de surface d'attaque Android avec Drozer

## Audit défensif en environnement autorisé

> **Objectif :** Découvrir comment utiliser Drozer pour analyser la surface d'attaque d'une application Android, identifier les composants exposés, évaluer les risques associés et proposer des remédiations conformes aux recommandations OWASP MASVS.

---

## 📖 Présentation

Ce laboratoire pédagogique vous place dans le rôle d'un **consultant en sécurité mobile** chargé d'évaluer la surface d'attaque d'une application Android dans un environnement entièrement contrôlé.

À l'aide de **Drozer**, vous apprendrez à :

* cartographier les composants Android exposés ;
* analyser les mécanismes de protection mis en place ;
* identifier les faiblesses de configuration ;
* évaluer les risques associés ;
* documenter les constats de manière professionnelle ;
* proposer des remédiations adaptées.

L'approche adoptée est **strictement défensive** : aucune exploitation offensive n'est réalisée.

---

## 🎯 Objectifs pédagogiques

À l'issue de ce laboratoire, vous serez capable de :

* Utiliser Drozer pour réaliser un audit de sécurité Android ;
* Identifier les composants Android exportés ;
* Évaluer les risques liés aux composants insuffisamment protégés ;
* Prioriser les vulnérabilités découvertes ;
* Produire un rapport d'audit structuré ;
* Corréler les constats avec les référentiels OWASP MASVS/MASTG.

---

## ⚙️ Prérequis

Avant de commencer, assurez-vous de disposer des éléments suivants :

### Environnement

* Windows, macOS ou Linux ;
* Android Studio (version 4.0 ou supérieure) ;
* Émulateur Android configuré (API 28 à 30 recommandée) ;
* Android Debug Bridge (ADB) fonctionnel ;
* Drozer installé sur la machine hôte ;
* Agent Drozer (`drozer-agent.apk`) ;
* Application de test vulnérable (`VulnerableApp.apk`).

---

## 🧪 Vérification de l'environnement

### Vérifier ADB

```bash
adb version
```

Résultat attendu :

```text
Android Debug Bridge version 1.0.xx
```

### Vérifier la détection de l'émulateur

```bash
adb devices
```

Résultat attendu :

```text
List of devices attached
emulator-5554    device
```

### Vérifier Drozer

```bash
drozer
```

Résultat attendu :

```text
Affichage de l'aide Drozer
```

---

## ⚠️ Cadre légal et périmètre

Ce laboratoire est réalisé dans un **environnement autorisé et contrôlé**.

### ✅ Autorisé

* Analyse d'un émulateur dédié ;
* Analyse de l'application VulnerableApp ;
* Identification et documentation des risques ;
* Utilisation de Drozer à des fins d'audit défensif.

### ❌ Interdit

* Exploiter les vulnérabilités découvertes ;
* Utiliser Drozer contre des applications réelles ;
* Manipuler des données utilisateur réelles ;
* Réaliser des actions offensives hors périmètre.

---

## 📂 Structure recommandée du projet

```text
LAB-9-Drozer/
├── preuves/
│   ├── activities/
│   ├── services/
│   ├── receivers/
│   ├── providers/
│   └── manifest/
├── captures/
├── rapport_final.md
├── triage.csv
├── checklist_fin.md
└── README.md
```

---

# 🚀 Déroulement du laboratoire

## Étape 1 — Configuration de l'environnement

### Installation des applications

Installer l'agent Drozer :

```bash
adb install drozer-agent.apk
```

Installer l'application vulnérable :

```bash
adb install VulnerableApp.apk
```

Configurer le port forwarding :

```bash
adb forward tcp:31415 tcp:31415
```

### Vérifications

* [ ] Émulateur démarré ;
* [ ] Agent Drozer installé ;
* [ ] Serveur Drozer activé ;
* [ ] VulnerableApp installée ;
* [ ] Port forwarding configuré.

---

## Étape 2 — Connexion à Drozer

Connexion :

```bash
drozer console connect
```

Informations de l'appareil :

```text
dz> device
```

Lister les modules :

```text
dz> list
```

Informations système :

```text
dz> run information.device
```

### Vérifications

* [ ] Console Drozer connectée ;
* [ ] Modules accessibles ;
* [ ] Informations Android affichées.

---

## Étape 3 — Cartographie des composants exposés

Lister les applications :

```text
dz> run app.package.list
```

Rechercher VulnerableApp :

```text
dz> run app.package.list -f vulnerable
```

Informations détaillées :

```text
dz> run app.package.info -a com.example.vulnerableapp
```

Activities :

```text
dz> run app.activity.info -a com.example.vulnerableapp
```

Services :

```text
dz> run app.service.info -a com.example.vulnerableapp
```

Receivers :

```text
dz> run app.broadcast.info -a com.example.vulnerableapp
```

Providers :

```text
dz> run app.provider.info -a com.example.vulnerableapp
```

---

## 📋 Tableau des composants exposés

| Type     | Composant           | Exporté | Protection       |
| -------- | ------------------- | ------- | ---------------- |
| Activity | LoginActivity       | Oui     | Aucune           |
| Activity | UserProfileActivity | Oui     | Permission       |
| Service  | DataSyncService     | Oui     | Aucune           |
| Receiver | BootReceiver        | Oui     | Aucune           |
| Provider | UserDataProvider    | Oui     | Lecture/Écriture |

---

## Étape 4 — Vérification des protections

Analyse du manifeste :

```text
dz> run app.package.manifest com.example.vulnerableapp
```

Intent Filters :

```text
dz> run app.activity.info -a com.example.vulnerableapp -i
```

Permissions des Providers :

```text
dz> run app.provider.info -a com.example.vulnerableapp -p
```

Recherche des URI accessibles :

```text
dz> run scanner.provider.finduris -a com.example.vulnerableapp
```

```text
dz> run app.provider.finduri com.example.vulnerableapp
```

---

## ⚠️ Analyse des risques

### Activities exportées

**Risque :**
Accès non autorisé à des écrans internes.

---

### Services exportés

**Risque :**
Exécution d'opérations sensibles.

---

### Broadcast Receivers

**Risque :**
Déclenchement d'actions non prévues.

---

### Content Providers

**Risque :**
Lecture ou modification non autorisée de données.

---

### Permissions insuffisantes

**Risque :**
Protection inadéquate des composants sensibles.

---

## 📁 Collecte des preuves

Structure recommandée :

```text
preuves/
├── activities/
├── services/
├── receivers/
├── providers/
└── manifest/
```

Pour chaque composant :

* Nom complet ;
* État d'exportation ;
* Protections présentes ;
* Risques identifiés ;
* Référence éventuelle au code ;
* Capture d'écran associée.

---

## 🚨 Triage des vulnérabilités

| ID | Composant           | Vulnérabilité                   | Confiance | Sévérité | Statut       |
| -- | ------------------- | ------------------------------- | --------- | -------- | ------------ |
| V1 | LoginActivity       | Exportée sans protection        | Élevée    | Élevée   | À corriger   |
| V2 | UserDataProvider    | URI accessibles                 | Élevée    | Critique | À corriger   |
| V3 | DataSyncService     | Validation absente              | Moyenne   | Moyenne  | À corriger   |
| V4 | BootReceiver        | Receiver insuffisamment protégé | Élevée    | Faible   | À surveiller |
| V5 | UserProfileActivity | Permission faible               | Élevée    | Moyenne  | À corriger   |

---

## 🔗 Mapping OWASP MASVS / MASTG

| ID | Référence       | Description                                   |
| -- | --------------- | --------------------------------------------- |
| V1 | MSTG-PLATFORM-1 | Exposer uniquement les composants nécessaires |
| V2 | MSTG-STORAGE-2  | Protection des données sensibles              |
| V3 | MSTG-PLATFORM-2 | Validation des entrées externes               |
| V4 | MSTG-PLATFORM-3 | Validation des intents                        |
| V5 | MSTG-AUTH-1     | Robustesse des mécanismes d'authentification  |

---

## 📦 Livrables attendus

* `rapport_final.md`
* `triage.csv`
* `checklist_fin.md`
* Dossier `preuves/`
* Captures d'écran des analyses Drozer

---

## 📝 Critères d'évaluation

| Critère            | Points |
| ------------------ | ------ |
| Traçabilité        | 4      |
| Cartographie       | 4      |
| Triage             | 3      |
| Mapping OWASP      | 3      |
| Remédiations       | 4      |
| Qualité du rapport | 2      |
| **Total**          | **20** |

---

## 🎓 Conclusion

Ce laboratoire vous a permis d'apprendre à utiliser **Drozer** pour analyser la surface d'attaque d'une application Android dans un cadre autorisé.

Les compétences acquises constituent une base essentielle pour la réalisation d'audits de sécurité mobile défensifs conformes aux bonnes pratiques professionnelles et aux standards OWASP.

---

### Auteur

**Cours :** Sécurité des applications mobiles
**Laboratoire :** LAB 9 — Analyse de surface d'attaque Android avec Drozer
**Version :** 1.0
**Licence :** Usage pédagogique uniquement.
