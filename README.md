# 🏍️ VR Moto HUD - Prototype Photoréaliste

Un prototype d'interface tête haute (HUD) pour casque de moto en Réalité Virtuelle, conçu avec HTML et A-Frame (WebXR). Ce projet simule une expérience de conduite immersive avec des données en temps réel et des alertes de sécurité avancées.

## ✨ Fonctionnalités Actuelles

* **HUD Intégré :** Affichage de la vitesse, de la limitation et de la navigation ancrés dans le champ de vision du pilote.
* **Alertes d'Angles Morts :** Système de prévention par halos lumineux latéraux (inspiration système Tesla) pour signaler l'approche d'un danger par l'arrière.
* **Radar Tactique :** Visualisation minimaliste des entités détectées autour de la moto.
* **Environnement Dynamique :** Sensation de vitesse via l'animation du défilement de la route.

## 🚀 Installation et Lancement

Pour que les futurs modèles 3D et les textures (HDRI) se chargent correctement, les navigateurs exigent que les fichiers soient servis par un serveur local (à cause des restrictions CORS), et non ouverts via un simple double-clic sur le fichier HTML.

1. Clone ce dépôt sur ta machine :
   `git clone https://github.com/TON_NOM/TON_PROJET.git`

2. Ouvre un terminal dans le dossier du projet et lance un serveur local.
   * Si tu utilises Python : `python -m http.server`
   * Si tu utilises Node.js : `npx serve`
   * Si tu utilises VS Code : Installe l'extension "Live Server" et clique sur "Go Live".

3. Ouvre ton navigateur à l'adresse indiquée (généralement `http://localhost:8000` ou `http://localhost:3000`).

## 🛠️ Roadmap : Vers le Rendu AAA

Le code actuel pose les bases fonctionnelles. Les prochaines étapes visent à atteindre un réalisme de type jeu vidéo :

* Remplacement des primitives géométriques par des modèles 3D complets optimisés (`.glb` / `.gltf`).
* Intégration d'une map HDRI pour un éclairage naturel et des reflets physiques (PBR) sur la carrosserie.
* Personnalisation de la typographie du HUD via des polices MSDF adaptées à la VR.
* Ajout de sons spatiaux (moteur, vent, alertes sonores).

## 📜 Licence

Distribué sous la licence MIT.
