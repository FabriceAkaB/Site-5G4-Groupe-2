+++
title = "Atelier"
weight = 3
+++

Votre atelier, avec un éventuelle lien vers un repos git.

## 1. Introduction de l’atelier

### Titre

**Atelier : Téléopération virtuelle d’une main robotique 2D avec MediaPipe Hands**

### Objectif

Dans cet atelier, on utilise la librairie **MediaPipe Hands** (Google) pour :

* suivre une main humaine en temps réel avec une webcam
* extraire un niveau de “flexion” pour chaque doigt (pouce, index, majeur, annulaire, auriculaire)
* contrôler une **main robotique virtuelle en 2D** dessinée à côté du flux de la webcam

Le résultat final à l’écran :

* à gauche : la webcam avec la main réelle + les landmarks MediaPipe
* à droite : une main stylisée de robot (paume + doigts) et des barres verticales qui montent/descendent selon l’ouverture des doigts.

---

## 2. Pré-requis techniques et installation

### 2.1. Version de Python compatible

**MediaPipe n’est pas encore compatible avec Python 3.13.**

Pour éviter les erreurs du type :

> `ERROR: No matching distribution found for mediapipe`

il faut utiliser **Python 3.10 ou 3.11**.

Sur ta machine Windows, tu peux vérifier les versions installées avec :

```bash
py -0p
```

On recommande d’installer **Python 3.10** (si ce n’est pas déjà fait), puis de créer un environnement virtuel pour ce projet.

### 2.2. Création de l’environnement (Windows)

Dans le terminal (CMD ou PowerShell), à la racine de ton projet :

```bash
py -3.10 -m venv mp-env
mp-env\Scripts\activate
```

Tu dois voir apparaître :

```bash
(mp-env) C:\Users\TonNom>
```

Ensuite, mets à jour `pip` et installe les librairies nécessaires :

```bash
pip install --upgrade pip
pip install opencv-python mediapipe numpy
```

### 2.3. Lancer le script depuis l’environnement

Toujours avec `(mp-env)` activé :

```bash
python ton_script.py
```

Ici, le script de l’atelier s’appelle par exemple : `atelier_main_robot_2d.py`.

---

## 3. Donner accès à la caméra (VS Code / Windows)

Pour que la webcam fonctionne :

1. **Sur Windows**

   * Va dans `Paramètres > Confidentialité > Caméra`
   * Vérifie que “Autoriser les applications de bureau à accéder à la caméra” est **activé**
   * Si VS Code apparaît dans la liste, donne-lui l’autorisation.

2. **Dans VS Code**

   * Ouvre le dossier où se trouve ton script Python.
   * Choisis l’interpréteur Python de ton environnement virtuel (`mp-env`).

     * En bas à droite, tu peux cliquer sur la version de Python et sélectionner `mp-env`.

3. Au premier lancement, Windows peut te demander si tu autorises `Python` à accéder à la caméra → clique sur **Autoriser**.

Si la caméra n’est pas accessible, le script te renverra :

```python
RuntimeError("Webcam introuvable ou non accessible.")
```

---

## 4. Vue d’ensemble du code

Voici ton code (je ne le modifie pas, je l’explique) :

```python
import cv2
import math
import mediapipe as mp
import numpy as np

# Taille carrée pour juxtaposer webcam et main virtuelle
FRAME_SIZE = 480
FINGERS = ["thumb", "index", "middle", "ring", "pinky"]

mp_hands = mp.solutions.hands
mp_draw = mp.solutions.drawing_utils

# Initialisation du modèle MediaPipe Hands
hands = mp_hands.Hands(
    static_image_mode=False,
    max_num_hands=1,
    min_detection_confidence=0.5,
    min_tracking_confidence=0.5,
)
```

### 4.1. Imports et constantes

* `cv2` : OpenCV, pour la capture webcam et le dessin 2D.
* `math` : pour calculer des distances (racine carrée).
* `mediapipe` : librairie de Google qui fournit **MediaPipe Hands**.
* `numpy` : pour manipuler des tableaux d’images (canvas, concaténation).

Constantes :

* `FRAME_SIZE = 480` : l’image de la webcam est redimensionnée en 480×480, ce qui facilite la juxtaposition avec la main virtuelle.
* `FINGERS` : la liste des doigts, pour gérer facilement les barres de flexion.

Initialisation de MediaPipe :

* `mp_hands.Hands(...)` crée un objet **Hands** configuré pour du temps réel :

  * `static_image_mode=False` : mode vidéo continu.
  * `max_num_hands=1` : on ne suit qu’une seule main.
  * `min_detection_confidence=0.5`, `min_tracking_confidence=0.5` : seuils de confiance.

---

## 5. Calcul du niveau de flexion de chaque doigt

```python
def compute_finger_values(hand_landmarks):
    """
    Retourne une valeur 0..1 pour chaque doigt.
    0 = très ouvert, 1 = très fermé (pointe proche du poignet).
    """
    lm = hand_landmarks.landmark
    wrist = lm[0]

    thumb_tip = lm[4]
    index_tip = lm[8]
    middle_tip = lm[12]
    ring_tip = lm[16]
    pinky_tip = lm[20]

    def dist(a, b):
        return math.sqrt((a.x - b.x) ** 2 + (a.y - b.y) ** 2)

    d_thumb = dist(thumb_tip, wrist)
    d_index = dist(index_tip, wrist)
    d_middle = dist(middle_tip, wrist)
    d_ring = dist(ring_tip, wrist)
    d_pinky = dist(pinky_tip, wrist)

    # Heuristique : distances typiques main ouverte/fermée
    MIN_D = 0.10
    MAX_D = 0.35

    def norm(d):
        v = (MAX_D - d) / (MAX_D - MIN_D)
        return max(0.0, min(1.0, v))

    return {
        "thumb": norm(d_thumb),
        "index": norm(d_index),
        "middle": norm(d_middle),
        "ring": norm(d_ring),
        "pinky": norm(d_pinky),
    }
```

### 5.1. Récupération des landmarks

* `hand_landmarks.landmark` contient une liste de 21 points (landmarks).
* `lm[0]` : poignet (`WRIST`).
* `lm[4]`, `8`, `12`, `16`, `20` : les extrémités de chaque doigt (TIP).

Chaque landmark a des coordonnées **normalisées** `x` et `y` entre 0 et 1 (position dans l’image).

### 5.2. Distance poignet ↔ pointe du doigt

La fonction `dist(a, b)` calcule la distance euclidienne entre deux points `(x,y)`.

* Pour chaque doigt, on calcule la distance `d_...` entre la pointe du doigt et le poignet.
* Intuition :

  * Main ouverte → les doigts sont loin du poignet → distance grande.
  * Main fermée → doigts proches du poignet → distance petite.

### 5.3. Normalisation en [0, 1]

* `MIN_D = 0.10`, `MAX_D = 0.35` sont des valeurs **heuristiques** :

  * ~0.35 : main très ouverte.
  * ~0.10 : main très fermée.

On inverse la distance pour avoir :

* 0 → doigt ouvert
* 1 → doigt fermé

```python
v = (MAX_D - d) / (MAX_D - MIN_D)
```

Puis on **clamp** pour rester dans `[0.0, 1.0]` :

```python
return max(0.0, min(1.0, v))
```

La fonction retourne un dictionnaire :

```python
{
  "thumb": 0.3,
  "index": 0.8,
  ...
}
```

Ces valeurs servent ensuite à dessiner les barres et à rendre la main robotique “vivante”.

---

## 6. Dessin de la main robotique 2D

```python
def draw_robot_hand(hand_landmarks, finger_values):
    """Dessine la main virtuelle 2D : main robot (paume+doigts) + barres de flexion."""
    canvas = np.zeros((FRAME_SIZE, FRAME_SIZE, 3), dtype=np.uint8)

    if hand_landmarks is None or finger_values is None:
        cv2.putText(
            canvas,
            "Aucune main detectee",
            (80, FRAME_SIZE // 2),
            cv2.FONT_HERSHEY_SIMPLEX,
            0.8,
            (0, 0, 255),
            2,
            cv2.LINE_AA,
        )
        return canvas
```

### 6.1. Création du canvas

* `canvas` est une image noire de 480×480 pixels.
* Si aucune main n’est détectée → message “Aucune main detectee”.

### 6.2. Projection des landmarks en pixels

```python
    pts = []
    for lm in hand_landmarks.landmark:
        x = int(min(max(lm.x * FRAME_SIZE, 0), FRAME_SIZE - 1))
        y = int(min(max(lm.y * FRAME_SIZE, 0), FRAME_SIZE - 1))
        pts.append((x, y))
```

* On convertit les coordonnées normalisées `[0,1]` en pixels `[0,FRAME_SIZE]`.
* On stocke chaque point dans `pts`.

### 6.3. Dessin de la paume (forme remplie)

```python
    palm_idx = [0, 1, 2, 5, 9, 13, 17]
    palm_pts = np.array([pts[i] for i in palm_idx], dtype=np.int32)
    cv2.fillPoly(canvas, [palm_pts], (40, 90, 180))
    cv2.polylines(canvas, [palm_pts], True, (140, 220, 255), thickness=3)
```

* `palm_idx` : indices de landmarks qui entourent la paume.
* `fillPoly` : colore cette zone d’un bleu foncé (paume du robot).
* `polylines` : contour plus clair, style “métal”.

### 6.4. Dessin des doigts façon bras robotique

```python
    finger_paths = [
        [0, 1, 2, 3, 4],      # pouce
        [0, 5, 6, 7, 8],      # index
        [0, 9, 10, 11, 12],   # majeur
        [0, 13, 14, 15, 16],  # annulaire
        [0, 17, 18, 19, 20],  # auriculaire
    ]
    for path in finger_paths:
        finger_pts = np.array([pts[i] for i in path], dtype=np.int32)
        cv2.polylines(canvas, [finger_pts], False, (120, 240, 200), thickness=10, lineType=cv2.LINE_AA)
        cv2.polylines(canvas, [finger_pts], False, (20, 50, 120), thickness=2, lineType=cv2.LINE_AA)
        # embout
        tip = finger_pts[-1]
        cv2.circle(canvas, tip, 9, (180, 255, 120), -1, lineType=cv2.LINE_AA)
        cv2.circle(canvas, tip, 11, (20, 50, 120), 2, lineType=cv2.LINE_AA)
```

* Chaque `path` suit les joints d’un doigt du poignet jusqu’à la pointe.
* On dessine des lignes épaisses (épaisseur 10) → effet **bras robotique**.
* On ajoute un cercle au bout du doigt → comme un capteur / embout mécanique.

### 6.5. Barres de flexion sur la droite

```python
    base_y = FRAME_SIZE - 40
    max_height = 200
    bar_width = 24
    spacing = 8
    start_x = FRAME_SIZE - (len(FINGERS) * (bar_width + spacing)) - 20

    for i, name in enumerate(FINGERS):
        v = finger_values[name]  # 0..1
        h = int(v * max_height)
        ...
        cv2.rectangle(...)
        cv2.putText(...)
```

* On construit une série de barres verticales à droite du canvas.
* Pour chaque doigt :

  * `v` = valeur de flexion 0..1.
  * `h` = hauteur de la barre → 0 (ouvert) à 200 px (fermé).
* On dessine :

  * une barre verte remplie (`cv2.rectangle`)
  * un contour blanc
  * une lettre (`T`, `I`, `M`, `R`, `P`) pour chaque doigt.

Texte en haut :

```python
cv2.putText(
    canvas,
    "Main robotique 2D (q pour quitter)",
    (20, 32),
    cv2.FONT_HERSHEY_SIMPLEX,
    0.7,
    (255, 255, 255),
    2,
    cv2.LINE_AA,
)
```

---

## 7. Boucle principale : capture, traitement, affichage

```python
def main():
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        raise RuntimeError("Webcam introuvable ou non accessible.")

    while True:
        success, frame = cap.read()
        if not success:
            break

        frame = cv2.flip(frame, 1)
        frame = cv2.resize(frame, (FRAME_SIZE, FRAME_SIZE))

        rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        results = hands.process(rgb)

        hand_landmarks = None
        finger_values = None
        if results.multi_hand_landmarks:
            hand_landmarks = results.multi_hand_landmarks[0]
            mp_draw.draw_landmarks(
                frame, hand_landmarks, mp_hands.HAND_CONNECTIONS
            )
            finger_values = compute_finger_values(hand_landmarks)

        robot_canvas = draw_robot_hand(hand_landmarks, finger_values)
        combined = np.hstack((frame, robot_canvas))

        cv2.imshow("Teleoperation virtuelle - MediaPipe Hands", combined)
        if cv2.waitKey(1) & 0xFF == ord("q"):
            break

    cap.release()
    cv2.destroyAllWindows()
```

### 7.1. Capture et préparation de l’image

* `VideoCapture(0)` : ouvre la webcam par défaut.
* `flip(frame, 1)` : effet miroir (plus naturel pour l’utilisateur).
* `resize` : met la frame au format 480×480.

### 7.2. Passage dans MediaPipe

* Conversion en RGB, puis `hands.process(rgb)` :

  * renvoie `results.multi_hand_landmarks` si une main est détectée.
* On prend la première main `results.multi_hand_landmarks[0]` (une seule main gérée).

### 7.3. Affichage + main virtuelle

* On dessine les landmarks sur la frame d’origine (`mp_draw.draw_landmarks`).
* On calcule `finger_values` avec la fonction vue plus haut.
* On génère `robot_canvas` avec `draw_robot_hand(...)`.
* On met les deux images côte à côte :

```python
combined = np.hstack((frame, robot_canvas))
```

* `imshow` affiche le résultat final :

  * gauche : main réelle + landmarks
  * droite : main robot + barres de flexion

On peut quitter avec la touche **q**.

---

## 8. Idées bonus / extensions cool à ajouter

Pour aller plus loin, tu peux proposer/designer :

### 8.1. Détection de gestes simples

* Si tous les doigts sont ouverts (`v < 0.2`) → afficher “OPEN HAND”.
* Si tous les doigts sont fermés (`v > 0.8`) → afficher “FIST”.
* Si seul l’index est tendu → afficher “POINTING”.

### 8.2. Changer la couleur de la main robotique selon l’état

* Main “relax” (peu de flexion) → couleur bleu clair.
* Main “serrée” (beaucoup de flexion) → rouge/orange.

### 8.3. Enregistrer un mini dataset

* À chaque frame, sauvegarder les valeurs de doigts dans un fichier `.csv`.
* Ça peut servir plus tard pour un **projet d’apprentissage par imitation** (LfD) :

  * chaque ligne = état des doigts à un instant t.

### 8.4. Contrôler une interface simple

* Utiliser un geste pour :

  * changer de mode (ex : “changer la couleur de fond si poing fermé”)
  * simuler des boutons (ex : lever le pouce = “play/pause”)

### 8.5. Lier à un moteur 3D

Même si ton atelier reste 2D, tu peux expliquer en conclusion :

* que la même logique (les `finger_values`) pourrait contrôler :

  * une main 3D dans Unity / Unreal / Three.js
  * ou une vraie main robotique avec des servos, via Arduino / ROS.