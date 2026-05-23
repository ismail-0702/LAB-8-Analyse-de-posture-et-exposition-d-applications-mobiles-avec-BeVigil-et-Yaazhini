# LAB-8-Analyse-de-posture-et-exposition-d-applications-mobiles-avec-BeVigil-et-Yaazhini
Présentation
Ce lab porte sur l'analyse statique et l'évaluation de la posture de sécurité d'une application Android, en s'appuyant sur deux outils complémentaires : BeVigil pour l'exposition externe, et Yaazhini pour l'analyse interne de l'APK.
L'application choisie est DIVA (Damn Insecure and Vulnerable App), une application pédagogique intentionnellement vulnérable, ce qui en fait un terrain d'entraînement idéal pour ce type d'exercice.

Aucune exploitation active n'a été réalisée — l'analyse reste dans un cadre strictement pédagogique et autorisé.


Objectifs
L'idée derrière ce lab est de simuler une vraie démarche d'analyse de sécurité mobile, de bout en bout :

Définir un périmètre légal et éthique avant de commencer.
Préparer un workspace organisé et traçable.
Calculer le hash SHA-256 de l'APK pour garantir l'intégrité de l'artefact.
Explorer l'exposition externe avec BeVigil.
Disséquer l'APK avec Yaazhini : permissions, URLs, vulnérabilités, activités.
Regrouper et normaliser les constats dans un fichier CSV.
Les associer aux catégories OWASP MASVS.
Rédiger un rapport final complet.


Structure du projet
Le dépôt est organisé en dossiers thématiques, un par phase d'analyse :
<img width="361" height="565" alt="Arborescence du projet" src="https://github.com/user-attachments/assets/c4b89261-96eb-495d-9f00-a1df91f941ac" />
00-scope/        → Périmètre, APK source
01-bevigil/      → Notes d'analyse BeVigil
02-yaazhini/     → Notes d'analyse Yaazhini
03-triage/       → CSV de triage + mapping OWASP
04-report/       → Rapport final
screenshots/     → Toutes les captures d'écran

Périmètre et éthique
Avant de toucher quoi que ce soit, un fichier scope.md a été rédigé pour cadrer l'analyse : cible autorisée, propriétaire, type d'artefact, limites et période d'analyse. C'est une bonne habitude à prendre, même en contexte pédagogique.
<img width="1184" height="1003" alt="Fichier de périmètre" src="https://github.com/user-attachments/assets/523a7569-1115-4151-a2ce-828e8aa642ab" />

Informations générales de l'analyse
Un fichier analyse_info.txt regroupe toutes les métadonnées de la session : date, analyste, cible, hash SHA-256, versions des outils et environnement utilisé. L'objectif est d'assurer la traçabilité complète du travail.
<img width="1066" height="326" alt="Informations d'analyse" src="https://github.com/user-attachments/assets/7d91cf18-e6fa-4ed7-836b-7f23ab976982" />

Préparation de l'APK
L'APK DIVA a été placé dans 00-scope/ et son hash SHA-256 calculé pour s'assurer que l'artefact analysé est bien celui attendu, sans modification.
<img width="1893" height="161" alt="Hash SHA-256 de l'APK" src="https://github.com/user-attachments/assets/ac285ca6-4359-4e71-82b1-e188664eca37" />

Analyse avec BeVigil
BeVigil a été utilisé pour rechercher des informations publiques liées à l'application : domaines, sous-domaines, endpoints API, emails, technologies exposées...
Résultat : aucune donnée exploitable n'a été remontée pour DIVA. Ce n'est pas surprenant — DIVA est une application locale pédagogique, elle n'est pas indexée publiquement. Cette limite a été documentée dans 01-bevigil/bevigil_notes.md.
<img width="1919" height="933" alt="Tableau de bord BeVigil" src="https://github.com/user-attachments/assets/744c9c2c-1eda-4a7a-a836-9638ac990360" />
<img width="1919" height="920" alt="Aucun résultat BeVigil" src="https://github.com/user-attachments/assets/9c418051-9762-442f-9083-4c5e85126b13" />
<img width="1252" height="695" alt="Projet créé dans BeVigil" src="https://github.com/user-attachments/assets/c0a17618-288e-4616-b886-53f0d9d2e753" />

Analyse avec Yaazhini
Yaazhini a pris le relais pour l'analyse interne de l'APK. L'outil permet d'inspecter le contenu de l'application sans avoir besoin d'un appareil physique.
<img width="1919" height="876" alt="Page de téléchargement Yaazhini" src="https://github.com/user-attachments/assets/23733ef8-2d01-4f75-bfbc-96a44de737cd" />

Permissions détectées
Trois permissions ont été identifiées :

WRITE_EXTERNAL_STORAGE
READ_EXTERNAL_STORAGE
INTERNET

Le stockage externe est particulièrement à surveiller : si une application y écrit des données sensibles, elles deviennent accessibles à d'autres apps sur l'appareil.
<img width="1597" height="341" alt="Permissions Android" src="https://github.com/user-attachments/assets/44f551e3-dde3-4242-bd70-f123371b27a9" />
URLs détectées
Plusieurs URLs ont été repérées, dont http://payatu.com et des références Android standards. Toute URL en HTTP (non chiffré) mérite vérification pour savoir si elle est réellement utilisée dans des échanges réseau.
<img width="1594" height="345" alt="URLs liées" src="https://github.com/user-attachments/assets/ee8c35b6-fad3-468c-9004-81f4c0bd605c" />
Vulnérabilités identifiées
SévéritéNombreConstats principauxHigh1Communication non sécuriséeMedium3Debug activé, backup vulnérable, export incorrect de providersLow1JavaScript activé dans WebViewWarning9Stockage externeInformation51Protections UI manquantes
<img width="1168" height="442" alt="Vulnérabilités Yaazhini" src="https://github.com/user-attachments/assets/e09cecff-7758-46dc-afb8-dd9a1752557f" />
Activités Android
Yaazhini a listé les activités présentes dans DIVA, qui reflètent bien sa nature pédagogique — chaque activité correspond à une faiblesse de sécurité intentionnelle :

MainActivity, LogActivity, HardcodeActivity
InsecureDataStorage1Activity, SQLInjectionActivity
APICredsActivity, AccessControl1Activity

<img width="1603" height="958" alt="Activités Android" src="https://github.com/user-attachments/assets/2af27af8-2237-4eb1-b6c0-a755f87c648d" />

Triage des constats
Tous les résultats ont été consolidés dans 03-triage/triage.csv, avec pour chaque constat : source, preuve, niveau de confiance, sévérité, impact, recommandation et référence OWASP. Ce fichier sert de base pour le rapport final.

Mapping OWASP MASVS
Les constats ont ensuite été associés aux catégories du référentiel OWASP MASVS dans 03-triage/owasp_mapping.md :

MASVS-NETWORK — Communication non sécurisée
MASVS-STORAGE — Stockage externe, backup
MASVS-PLATFORM — Export de providers
MASVS-CODE — Debug activé
MASVS-RESILIENCE — Robustesse globale


Top 5 des constats
IDConstatSévéritéOWASPFIND-001Communication non sécurisée (HTTP)HighMASVS-NETWORKFIND-006android:debuggable activéMediumMASVS-RESILIENCEFIND-007android:allowBackup activéMediumMASVS-STORAGEFIND-008Export incorrect de providersMediumMASVS-PLATFORMFIND-002/003Permissions de stockage externeMediumMASVS-STORAGE

Recommandations

Forcer HTTPS/TLS pour toutes les communications réseau.
Désactiver android:debuggable dans les builds de production.
Désactiver android:allowBackup si l'app manipule des données sensibles.
Limiter les permissions au strict nécessaire.
Protéger ou restreindre les composants Android exposés (activities, providers, services, receivers).
Désactiver JavaScript dans les WebViews si ce n'est pas indispensable.


Limites de l'analyse
Quelques points à garder en tête pour interpréter les résultats :

BeVigil n'a rien retourné car DIVA n'est pas indexée publiquement — ce n'est pas un échec, c'est attendu.
DIVA est volontairement vulnérable : les résultats Yaazhini reflètent des failles intentionnelles, pas un cas réel.
Certaines URLs détectées sont des références Android standards sans trafic réseau réel.
Aucune exploitation active n'a été menée.
