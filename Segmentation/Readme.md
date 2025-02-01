📌 Explication des étapes du TP
1️⃣ Charger une image en niveaux de gris
👉 Pourquoi ?
Une image en niveaux de gris simplifie le traitement en réduisant les informations à un seul canal (0 à 255), ce qui facilite l’application du seuillage.

👉 Comment ? (En Python avec OpenCV)

python
Copier
Modifier
import cv2

# Charger l'image en niveaux de gris
image = cv2.imread('image.jpg', cv2.IMREAD_GRAYSCALE)
2️⃣ Afficher l'image
👉 Pourquoi ?
Visualiser l’image permet de vérifier son chargement correct et d’observer ses contrastes.

👉 Comment ?

python
Copier
Modifier
import matplotlib.pyplot as plt

plt.imshow(image, cmap='gray')
plt.title("Image en niveaux de gris")
plt.axis('off')
plt.show()
3️⃣ Appliquer un seuillage manuel avec un seuil de 128
👉 Pourquoi ?
Un seuillage manuel divise l’image en deux régions :

Les pixels ≥ 128 → objets (blanc)
Les pixels < 128 → fond (noir)
👉 Comment ?

python
Copier
Modifier
_, binary_image = cv2.threshold(image, 128, 255, cv2.THRESH_BINARY)
4️⃣ Afficher l’image originale et segmentée côte à côte
👉 Pourquoi ?
Cela permet de comparer l’effet du seuillage.

👉 Comment ?

python
Copier
Modifier
fig, axs = plt.subplots(1, 2, figsize=(10, 5))

axs[0].imshow(image, cmap='gray')
axs[0].set_title("Image originale")
axs[0].axis('off')

axs[1].imshow(binary_image, cmap='gray')
axs[1].set_title("Image segmentée (seuil = 128)")
axs[1].axis('off')

plt.show()
5️⃣ Varier le seuil manuellement entre 0 et 255 et observer les résultats
👉 Pourquoi ?
Tester différents seuils permet de comprendre leur impact sur la segmentation.

👉 Comment ?
On peut créer un curseur interactif pour ajuster le seuil :

python
Copier
Modifier
import numpy as np
from ipywidgets import interact

def apply_threshold(threshold):
    _, binary_image = cv2.threshold(image, threshold, 255, cv2.THRESH_BINARY)
    plt.imshow(binary_image, cmap='gray')
    plt.title(f"Image segmentée (seuil = {threshold})")
    plt.axis('off')
    plt.show()

interact(apply_threshold, threshold=(0, 255, 1))
🔹 Remarque : Plus le seuil est élevé, plus l’image est assombrie.
Si le seuil est trop bas, l’objet et le fond peuvent fusionner.

6️⃣ Sauvegarder les images segmentées avec différents seuils
👉 Pourquoi ?
Stocker les images permet de les analyser plus tard.

👉 Comment ?

python
Copier
Modifier
cv2.imwrite('segmented_128.jpg', binary_image)
On peut aussi sauvegarder des images avec plusieurs seuils :

python
Copier
Modifier
for t in [50, 100, 150, 200]:
    _, img = cv2.threshold(image, t, 255, cv2.THRESH_BINARY)
    cv2.imwrite(f'segmented_{t}.jpg', img)
7️⃣ Utiliser un seuillage automatique avec la méthode d'Otsu
👉 Pourquoi ?
La méthode d'Otsu calcule un seuil optimal automatiquement, évitant d’avoir à choisir un seuil manuel.

👉 Comment ?

python
Copier
Modifier
_, otsu_image = cv2.threshold(image, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
8️⃣ Comparer les résultats avec le seuillage manuel
👉 Pourquoi ?
Cela permet de voir si Otsu segmente mieux l’image qu’un seuil arbitraire.

👉 Comment ?

python
Copier
Modifier
fig, axs = plt.subplots(1, 3, figsize=(15, 5))

axs[0].imshow(image, cmap='gray')
axs[0].set_title("Image originale")
axs[0].axis('off')

axs[1].imshow(binary_image, cmap='gray')
axs[1].set_title("Seuil manuel (128)")
axs[1].axis('off')

axs[2].imshow(otsu_image, cmap='gray')
axs[2].set_title("Seuil automatique (Otsu)")
axs[2].axis('off')

plt.show()
📌 Observation :

Si l’image a deux pics bien distincts dans l’histogramme, Otsu fonctionne bien.
Si l’image est bruyante ou sans fort contraste, Otsu peut échouer.
9️⃣ Écrire un court rapport analysant les résultats
Le rapport doit contenir :
✅ Les images obtenues avec différents seuils
✅ Une comparaison entre seuillage manuel et Otsu
✅ Réponses aux questions suivantes :

📜 Réponses aux Questions
📌 Quel impact a le choix du seuil sur la qualité de la segmentation ?
Un seuil trop bas → Trop d’objets détectés (mauvaise séparation entre fond et objets).
Un seuil trop haut → Détails perdus (petits objets disparaissent).
Un bon seuil dépend du contraste de l’image.
📌 Dans quelles situations la méthode d'Otsu est-elle préférable au seuillage manuel ?
Lorsque l’image a deux groupes bien distincts (bimodal histogram).
Pour éviter un choix subjectif du seuil.
Si l’image contient du bruit, Otsu peut ne pas bien fonctionner.
✅ Alternative : Utiliser Otsu + flou Gaussien pour améliorer la robustesse.

python
Copier
Modifier
blurred = cv2.GaussianBlur(image, (5, 5), 0)
_, otsu_image = cv2.threshold(blurred, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
📌 Quelles sont les limites de la segmentation par seuillage ?
Ne fonctionne pas bien si l’image a plusieurs objets avec des niveaux de gris proches.
Sensible au bruit et aux ombres.
Ne permet pas de détecter des objets complexes ou superposés.
✅ Alternatives possibles : 1️⃣ Segmentation par clustering (ex : K-means)
2️⃣ Détection de contours (ex : Canny)

python
Copier
Modifier
edges = cv2.Canny(image, 100, 200)
3️⃣ Segmentation par Watershed pour les objets imbriqués.

📌 Résumé des alternatives
Méthode	Avantages	Inconvénients
Seuillage manuel	Simple, rapide	Doit être ajusté pour chaque image
Otsu	Automatique, efficace pour histogrammes bimodaux	Échec si l’image n’a pas deux pics distincts
Flou + Otsu	Réduit le bruit avant segmentation	Peut lisser trop de détails
K-means clustering	Segmentation multi-niveaux	Plus lent
Détection de contours	Identifie les bords des objets	Perte d’information de l’objet
Watershed	Segmentation précise d’objets imbriqués	Sensible aux erreurs de prétraitement
🎯 Conclusion
Ce TP montre l’importance de choisir la bonne technique en fonction des images traitées. Pour des images complexes, il faut souvent combiner plusieurs méthodes (ex : Otsu + flou, clustering, contours).

Si tu veux un script complet pour tout automatiser, dis-moi ! 🚀