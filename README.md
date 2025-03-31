# Python-Jointure-de-tables-avec-hash-d-images-et-analyse-du-Covid-sur-les-applications-de-google-
Python : Jointure de tables de données avec hash d'images et analyse du Covid sur les applications de google play store


En juillet 2020, nous avons commencé à travailler sur le secteur des applications mobiles. Il est rapidement devenu évident que deux informations clés étaient nécessaires pour réaliser des analyses pertinentes : le titre de l'application et le nom de l'éditeur. En collectant les données des reçus de l'App Store (iOS) et du Play Store (Android), nous avons pu obtenir des informations précieuses sur les achats in-app (IAP), c'est-à-dire les transactions effectuées à l'intérieur des applications.

Cependant, les reçus de l'App Store fournissent peu d'informations : seul le nom de l'IAP, l'URL du logo de l'application et le prix sont disponibles. En utilisant des données externes, nous avons constitué une base de données des URL de logos associées aux noms des applications et des éditeurs.

Tâche :

Nous disposons de deux ensembles de données :

    Un extrait contenant les données d'IAP. : technical_test_external_source_extract.csv

    Une source externe qui associe les URL de logos aux noms d'applications et des éditeurs. : technical_test_table_extract.csv

Notre mission est de :

    Enrichir les données d'IAP en ajoutant le titre de l'application et le nom de l'éditeur, en faisant correspondre les URL de logos avec celles de la source externe.

    Analyser les données enrichies pour tirer des insights et créer une présentation brève (maximum 3 diapositives) à destination des éditeurs d'applications, en se concentrant sur l'impact du confinement dû au Covid en France.



 # Problematique 1

Problématique :
Étant donné que plusieurs URL de logos existent pour une même application et que les URL ne peuvent pas être directement mises en correspondance avec celles de la source externe par une simple jointure, il est nécessaire d’utiliser une approche basée sur les images pour effectuer une correspondance plus robuste.

Solution :
Une méthode efficace pour résoudre ce problème consiste à utiliser un code Python qui télécharge les images à partir des URL, génère des empreintes (ou hashes) pour chaque image, et compare ces empreintes pour déterminer quelle image dans le jeu de données externe correspond à une image dans les données IAP.

Un aperçu du code Python qui permet de résoudre ce problème est le code : (Final.ipynb) en pièce jointe.

# Explication (Final.ipynb) : 

Téléchargement des images :
Le code utilise la bibliothèque requests pour télécharger les images des URL présentes dans les colonnes product_url_img et icon des deux datasets. Une fois l'image téléchargée, elle est transformée en une empreinte numérique (hash) avec imagehash, en utilisant l'algorithme de phash (Perceptual Hashing). Cela permet de générer des identifiants uniques pour chaque image.

Calcul des distances de Hamming :
La distance de Hamming est utilisée pour mesurer la différence entre deux empreintes d'image. Plus la distance est petite, plus les images sont similaires. Ce code utilise une fonction hamming_distance pour comparer les empreintes.

Correspondance des données :
Pour chaque ligne du jeu de données contenant les informations des IAP, le code génère un hash pour l'URL de l'image et cherche la meilleure correspondance dans le jeu de données externe en comparant les distances de Hamming entre les empreintes d'images. Si la distance est inférieure ou égale à un seuil (par défaut 5), la correspondance est considérée comme valide.

Enrichissement des données :
Une fois la correspondance trouvée, les informations supplémentaires comme le titre de l'application et le nom de l'éditeur sont ajoutées à chaque ligne des données IAP.

Export des résultats :
Après avoir enrichi les données, le code génère un fichier CSV final contenant les informations sur les applications, les éditeurs, et les URL des images correspondantes. Une fonction Xlookup permet de joindre facilement les deux tableaux.

Fichier Excel final : technical_test_external_source_extract


# Problematique 2

Nos deux tableaux sont maintenants enrichis, une analyse directe est maintenant possible.

Problématique : Pour une analyse plus poussé, rajouter des colonnes pertinentes supplémentaires pourrait servir pour classer les analyses par catégories.

Solution :

A l'aide de PowerQuery, il est possible avec un peu d'imagination d'ajouter des colonnes supplémentaires grace à des filtres

•	Une colonne categorie (Gaming, Dating, Streaming)

![image](https://github.com/user-attachments/assets/db367c2f-77d1-4786-af68-3984bb8856cb)

•	Une colonne Type (abonnement or achat unique)

![image](https://github.com/user-attachments/assets/eff4fa7d-5fde-4438-b7dd-1a04fb6e5256)


Une analyse complete est maintenant disponible avec des recommendations pour chaque type de produit (Gaming, Dating, Streaming) sur Analyse Python - PowerBI.





