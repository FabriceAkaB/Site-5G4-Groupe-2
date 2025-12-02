+++
title = "Notes de cours"
weight = 2
+++

Notes de cours sur votre sujet.

## NOTES DE COURS – MediaPipe Hands : Fondements, Fonctionnement et Applications Modernes

### 1. Introduction générale
MediaPipe est une technologie développée par Google qui permet d’analyser des images ou des vidéos en temps réel et d’extraire des informations utiles grâce à des modèles d’intelligence artificielle optimisés. Elle est devenue rapidement populaire parce qu’elle permet de faire de la vision par ordinateur « avancée » sans avoir besoin d’un ordinateur puissant ou d’une carte graphique.

Dans le monde de la robotique et de l’interaction homme-machine, comprendre la position et les mouvements de la main est essentiel. La main humaine est capable de précision, d’expression, de manipulation, et c’est l’un des organes les plus utilisés pour contrôler un robot ou une interface. C’est pour cette raison que MediaPipe Hands, le module de détection et de suivi de mains, est devenu rapidement une référence.

L’objectif de ces notes de cours est d’expliquer clairement les fondements théoriques, le fonctionnement technique et les applications pratiques de MediaPipe Hands. On va aussi voir comment cette technologie s’intègre dans des systèmes plus avancés comme les robots humanoïdes, et pourquoi elle est très utilisée dans les projets étudiants, les laboratoires d’innovation et même dans certaines startups.

Enfin, ces notes donnent un aperçu complet allant de l’origine de MediaPipe, jusqu’aux applications dans la robotique moderne, en passant par son fonctionnement interne, ses limites, ses avantages, et plusieurs exemples concrets.

### 2. Origine et raison d’être de MediaPipe
#### 2.1. Pourquoi Google a créé MediaPipe
Avant MediaPipe, les développeurs et chercheurs qui voulaient faire de la vision par ordinateur en temps réel devaient combiner plusieurs librairies, outils et modèles différents. Beaucoup de solutions n’étaient pas pensées pour fonctionner sur un téléphone ou sur un ordinateur portable sans GPU. Certaines approches demandaient une grande puissance de calcul, et d’autres manquaient de précision ou de simplicité.

Google avait besoin d’une plateforme capable de :
- détecter des objets ou des parties du corps ;
- exécuter des modèles de Machine Learning en temps réel ;
- fonctionner sur Android, iOS, Windows, Linux, et même sur le web ;
- être flexible pour intégrer plusieurs modèles à la suite.

MediaPipe a été conçu pour répondre exactement à ce besoin : fournir une infrastructure complète qui permet aux modèles d’être utilisés facilement, rapidement, et sur n’importe quel appareil.

#### 2.2. Objectifs initiaux du projet
Les objectifs de MediaPipe étaient clairs :
- rapidité : 30 à 60 FPS sans carte graphique ;
- portabilité : appareils mobiles, ordinateurs, navigateurs ;
- modularité : un ensemble d’outils prêts à l’emploi (mains, visage, corps, segmentation, etc.) ;
- open source : accessible gratuitement, modifiable, réutilisable ;
- intégration facile dans des projets réels.

#### 2.3. Les choix techniques derrière MediaPipe
Le cœur de MediaPipe est un graph processing framework, qui permet de définir une suite d'opérations appelées « calculators ». Chaque calculator applique une transformation sur l’image (exemple : détecter une paume, cropper la main, prédire les landmarks, etc.). Toute la pipeline est optimisée pour que l’image passe le plus vite possible à travers ces modules.

MediaPipe utilise aussi TensorFlow Lite, une version très légère de TensorFlow, qui permet d’utiliser des modèles entraînés sur des appareils à faible puissance. Des optimisations comme XNNPACK ou NNAPI permettent d’accélérer encore le calcul sur CPU.

### 3. MediaPipe Hands : fondements théoriques
#### 3.1. Le concept de landmarks
MediaPipe Hands génère une liste de 21 points clés (appelés landmarks) qui décrivent la main humaine. Ces points correspondent à :
- la base de la main (le poignet) ;
- les articulations principales ;
- les phalanges ;
- la pointe de chaque doigt.

Chaque landmark contient des coordonnées normalisées (x, y, z) :
- **x** : position horizontale entre 0 et 1 ;
- **y** : position verticale entre 0 et 1 ;
- **z** : profondeur relative (pas en mètres, mais une estimation).

Les landmarks sont une représentation « simplifiée » mais très utile de la structure de la main.

#### 3.2. Architecture du modèle
Le modèle MediaPipe Hands n’est pas un seul modèle, mais un pipeline avec deux modèles :
1. **Palm Detection** : ce modèle est entraîné pour détecter uniquement la paume, car c’est la partie la plus stable visuellement. Détecter une paume est plus rapide et plus robuste que détecter une main entière.
2. **Hand Landmark Model** : ce modèle prend une image croppée de la main et prédit les 21 landmarks avec une grande précision.

Cette architecture permet de :
- réduire la quantité de calcul ;
- accélérer le traitement ;
- améliorer la stabilité ;
- obtenir une meilleure précision sur les doigts.

#### 3.3. Types de données retournées
Le modèle retourne :
- multi_hand_landmarks : liste des 21 points ;
- multi_handedness : main gauche ou droite (estimation) ;
- landmark[i].x, y, z : coordonnées du point *i* ;
- score : confiance du modèle.

#### 3.4. 2D vers pseudo-3D
MediaPipe donne bien une valeur z pour chaque point, mais il ne s’agit pas d’une vraie profondeur dans l’espace. C’est une profondeur relative, utile pour :
- comprendre si un doigt est plié ;
- déterminer si la main pivote ;
- évaluer une forme générale.

Mais on ne peut pas mesurer une distance exacte comme « votre main est à 32 cm de la caméra ».

### 4. Fonctionnement interne : pipeline détaillé
#### 4.1. Étapes
1. Capture d’une image ;
2. Détection de la paume ;
3. Extraction de la zone de la main ;
4. Passage dans le modèle Landmark ;
5. Production des 21 points ;
6. Dessin des points et connexions.

#### 4.2. Performance
MediaPipe peut tourner :
- à 30 ou 60 FPS sur un ordinateur portable ;
- à ~20 FPS sur un smartphone milieu de gamme ;
- à ~10 FPS sur un Raspberry Pi 4.

La performance dépend de la résolution et du CPU.

#### 4.3. Principaux attributs
```text
Hands(static_image_mode=False, ...)
```
- max_num_hands : nombre maximal de mains suivies ;
- min_detection_confidence : seuil de détection ;
- min_tracking_confidence : qualité du suivi.

La variable `results.multi_hand_landmarks` contient la liste des landmarks détectés.

### 5. Avantages de MediaPipe Hands
#### 5.1. Rapidité
Les modèles sont très optimisés et fonctionnent sans carte graphique.

#### 5.2. Simplicité d’intégration
Quelques lignes de code suffisent.

#### 5.3. Open-source
Tout le monde peut l’utiliser gratuitement.

#### 5.4. Multi-plateforme
Fonctionne sur Windows, Linux, macOS, Android, iOS.

#### 5.5. Parfait pour prototyper
Idéal pour :
- la robotique ;
- les jeux ;
- les interfaces innovantes ;
- les projets scolaires.

#### 5.6. Fonctionne sur Raspberry Pi
En réduisant la résolution à 640×480, MediaPipe peut fonctionner entre 8 et 12 FPS sur un Pi 4.

### 6. Limites techniques
- **Dépendance à la lumière** : une pièce trop sombre ou avec un contre-jour fort peut faire perdre le tracking.
- **Occlusions** : si des doigts sont cachés, le modèle perd facilement la structure.
- **Profondeur imprécise** : pas de vraie 3D mesurable.
- **Mouvements très rapides** : certains points peuvent « trembler ».
- **Pas conçu pour l’industrie** : ce n’est pas un système de vision professionnel haut de gamme comme Vicon ou Optitrack.

### 7. MediaPipe pour tout le corps
MediaPipe propose plusieurs modules :
- MediaPipe Face Mesh : 468 points du visage ;
- MediaPipe Pose : 33 points du corps humain ;
- MediaPipe Holistic : combinaison visage + pose + mains.

MediaPipe Holistic est capable de suivre tout le corps en temps réel, ce qui en fait une alternative très accessible aux systèmes de motion capture classiques.

Applications :
- avatars 3D ;
- jeux vidéo ;
- animation faciale ;
- robots humanoïdes qui imitent un humain ;
- fitness tracking.

### 8. Applications pratiques
#### 8.1. Jeux vidéo et AR
- filtres Snapchat ;
- jeux qui utilisent les gestes de la main ;
- expériences de réalité augmentée.

#### 8.2. Interfaces main à ordinateur
- contrôle du curseur ;
- navigation avec gestes ;
- clic virtuel avec le pouce et l’index.

#### 8.3. Robotique
MediaPipe est utilisé pour :
- piloter un bras robotique ;
- contrôler une main articulée ;
- faire de l’apprentissage par imitation ;
- enregistrer des gestes humains pour entraîner des modèles.

#### 8.4. Robots humanoïdes
Même si les robots avancés comme Tesla Optimus utilisent des modèles plus complexes, le principe est similaire :
1. Un humain fait une démonstration ;
2. Le système enregistre la position des articulations ;
3. Le robot apprend à reproduire la tâche.

MediaPipe peut servir de base pour créer un petit prototype d’imitation learning.

### 9. MediaPipe Hands : approche technique complète
Cette section explique plus en détail comment utiliser MediaPipe Hands dans un script Python.

#### 9.1. Installation
- Installer Python 3.10 ou 3.11 ;
- cr?er un environnement ;
- installer `mediapipe`, `opencv-python`, `numpy`.

#### 9.2. Autoriser la webcam
Sur Windows, il faut autoriser Python ? acc?der ? la cam?ra. Dans Visual Studio Code, choisir l'interpr?teur Python du bon environnement.

#### 9.3. Extraction des landmarks
`results.multi_hand_landmarks` retourne les points.

```python
for i, lm in enumerate(hand.landmark):
    x = lm.x
    y = lm.y
    z = lm.z
```

#### 9.4. Conversion en valeurs exploitables
Avec les landmarks, on peut :
- calculer des distances ;
- évaluer si un doigt est plié ;
- mesurer des angles.

#### 9.5. Dessin d’une main robotique
Ton atelier en 2D utilise les landmarks pour reconstruire graphiquement une main artificielle.

#### 9.6. Filtrage
Pour stabiliser les mouvements, on peut utiliser :
- un filtre One Euro ;
- un filtre de Kalman ;
- une moyenne glissante.

### 10. Légèreté, portabilité et utilisation embarquée
#### 10.1. Sur mobile (Android/iOS)
MediaPipe peut être intégré dans une application mobile en utilisant :
- MediaPipe Tasks ;
- TensorFlow Lite ;
- Unity + MediaPipe.

Les performances sont excellentes sur un téléphone moderne.

#### 10.2. Sur navigateur
Google propose maintenant MediaPipe Tasks for Web qui permet de faire du suivi de main directement dans un navigateur à l’aide de WebAssembly.

#### 10.3. Sur Raspberry Pi
Pour obtenir de bonnes performances :
- résolution 640×480 ;
- CPU governor en mode performance ;
- éclairage stable.

### 11. Conclusion
MediaPipe Hands est aujourd’hui l’un des outils les plus accessibles pour apprendre et pratiquer la vision par ordinateur moderne. Sa rapidité, sa facilité d’utilisation et sa compatibilité avec presque toutes les plateformes en font une technologie idéale pour la robotique, les interfaces humaines, les jeux vidéo, les projets éducatifs et même les premiers prototypes de téléopération.

Bien qu’il ait des limites (notamment en profondeur et en stabilité), MediaPipe permet de comprendre des concepts importants comme la détection, la régression de landmarks, le traitement en temps réel et la construction de pipelines d’IA. Ces notes de cours montrent aussi comment cette technologie peut servir de base pour des systèmes beaucoup plus avancés comme les robots humanoïdes modernes, qui utilisent des modèles plus complexes mais suivent les mêmes principes.

MediaPipe représente donc une excellente porte d’entrée vers la robotique, la vision par ordinateur et l’apprentissage par imitation.

### 12. Annexes
#### 12.1. Glossaire
- **Landmark** : point clé détecté par un modèle ML ;
- **Pipeline** : enchaînement d’opérations de traitement ;
- **Regression model** : modèle qui prédit des valeurs continues ;
- **TensorFlow Lite** : version légère de TensorFlow optimisée pour mobile.

#### 12.2. Index complet des 21 landmarks
(INSERTION IMAGE ICI)

#### 12.3. Comparaison MediaPipe vs OpenPose
- MediaPipe : rapide, léger, simple ;
- OpenPose : plus précis mais plus lourd.

#### 12.4. Liens externes
- Google MediaPipe documentation ;
- TensorFlow Lite ;
- Exemples de projets GitHub.

---

Structuration des idées avec chatgpt 5.1  
Correction du texte avec chatgpt 5.1
