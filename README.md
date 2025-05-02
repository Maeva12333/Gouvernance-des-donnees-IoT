# 🚗 TP - Gouvernance des données & Sécurisation MQTT pour véhicules connectés

Ce projet illustre une architecture de traitement de données de télémétrie provenant de véhicules connectés, basée sur **MQTT**, **Python**, **Flask**, **machine learning**, et **sécurité réseau**. Il est divisé en deux parties : **traitement de la donnée** et **sécurisation d'une plateforme MQTT**.

![image](https://github.com/user-attachments/assets/acbef15f-4932-45af-ae7a-c66677390f74)


---

## 🧩 Partie 1 – Traitement des données avec MQTT

### ✅ Étape 1 – Génération et publication de données
- Récupération de **5 tracks** depuis l’API publique d’[enviroCar](https://envirocar.org/).
- Publication de chaque track sous forme de message **MQTT** via le broker **Mosquitto** (localhost).
- Vérification du bon fonctionnement en publiant/abonnant sur un topic `test_channel`.

### ✅ Étape 2 – Réception et traitement asynchrone
- Un **subscriber MQTT** écoute les messages sur le topic `envirocar/telemetry`.
- Chaque message est traité et sauvegardé dans un **fichier CSV**.

### ✅ Étape 3 – Filtrage et enrichissement
- Chargement du fichier CSV.
- Filtrage des trajets ayant une vitesse moyenne > 80 km/h.
- Export des données filtrées dans un second CSV.

### ✅ Étape 4 – Analyse prédictive
- Utilisation de **scikit-learn** pour entraîner un modèle de **régression linéaire** afin de prédire la consommation en fonction de la vitesse moyenne.

### ✅ Étape 5 – Visualisation cartographique
- Utilisation de **Folium** pour afficher un trajet GPS de manière interactive (coordonnées extraites du JSON des données).

### ✅ Étape 6 – Interface Web
- Déploiement d’une **application Flask** permettant de :
  - Lister les tracks disponibles.
  - Visualiser la carte interactive d’un trajet.

---

## 🔐 Partie 2 – Sécurisation d'une plateforme MQTT

### ✅ Étape 1 – Authentification
- Configuration de Mosquitto avec un fichier `password_file`.
- Test de connexions MQTT avec et sans identifiants (`vehicule_user` / `1234`).

### ✅ Étape 2 – Intégration d’AWS Secrets Manager
- Création de secrets pour stocker les identifiants MQTT.
- Application des **principes de sécurité AWS** :
  - Accès par **rôle IAM**.
  - **Rotation automatique** des secrets.
  - Suppression des identifiants codés en dur.
  - Journalisation des accès via **AWS CloudTrail**.

### ✅ Étape 3 – Chiffrement TLS
- Mise en place d’un **certificat TLS auto-signé** pour sécuriser les flux MQTT via le port `8883`.
- Comparaison entre :
  - MQTT non sécurisé (messages visibles avec Wireshark).
  - MQTT sécurisé TLS (données chiffrées et protégées contre les attaques MitM).

---

## 🔐 Contrôle d’accès (ACL)

### Points d’analyse sur le schéma AWS :
- **Point 2 - AWS IoT Core** :
  - Authentification par certificat X.509.
  - ACL par topic, isolant chaque véhicule.
- **Point 5 - Lambda receive telemetry** :
  - Droits en lecture sur les topics de télémétrie, en écriture sur DynamoDB uniquement.
- **Point 6 - Lambda remote command** :
  - Autorisé uniquement à envoyer des commandes via des topics spécifiques.

---

## 🧠 Questions de réflexion

### 🔹 Sécurité pour une flotte à grande échelle
- Certificats uniques + rotation automatique.
- Contrôle d'accès par ID véhicule.
- Détection de comportements anormaux avec AWS IoT Defender.

### 🔹 Compromis entre sécurité et performance
- TLS consomme plus de ressources embarquées.
- Filtrage et vérification augmentent la latence.
- Sécurité vs simplicité d’intégration.

### 🔹 Adaptation à une connectivité intermittente
- Utilisation d’un **buffer local** pour les données en attente.
- Reprise automatique (retry).
- Synchronisation à la reconnexion via **Device Shadow** ou logique locale.

### 🔹 Risques persistants
- Compromission physique ou firmware.
- Failles logicielles non connues.
- Mauvaises configurations IAM.
- Rejeu de messages si non protégés par timestamps/ID uniques.

---

## 📦 Stack technique

- `paho-mqtt`, `Flask`, `scikit-learn`, `pandas`, `folium`
- `mosquitto` (broker local)
- `Wireshark` (analyse réseau)
- AWS services (Secrets Manager, IAM, IoT Core – simulés/conceptualisés)

---

## 📝 Auteurs & Remerciements
Maeva SIMO KAMWA
Réalisé dans le cadre du module **Cloud & Gouvernance des Données**.  
Merci à [enviroCar](https://envirocar.org/) pour l'API open data.
