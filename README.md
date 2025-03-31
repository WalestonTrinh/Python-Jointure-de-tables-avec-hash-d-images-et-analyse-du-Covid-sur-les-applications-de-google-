# Python-Jointure-de-tables-avec-hash-d-images-et-analyse-du-Covid-sur-les-applications-de-google-
Python : Jointure de tables de données avec hash d'images et analyse du Covid sur les applications de google play store


# En juillet 2020, nous avons commencé à travailler sur le secteur des applications mobiles. Il est rapidement devenu évident que deux informations clés étaient nécessaires pour réaliser des analyses pertinentes : le titre de l'application et le nom de l'éditeur. En collectant les données des reçus de l'App Store (iOS) et du Play Store (Android), nous avons pu obtenir des informations précieuses sur les achats in-app (IAP), c'est-à-dire les transactions effectuées à l'intérieur des applications.

Cependant, les reçus de l'App Store fournissent peu d'informations : seul le nom de l'IAP, l'URL du logo de l'application et le prix sont disponibles. En utilisant des données externes, nous avons constitué une base de données des URL de logos associées aux noms des applications et des éditeurs.

Tâche :

Nous disposons de deux ensembles de données :

    Un extrait contenant les données d'IAP.

    Une source externe qui associe les URL de logos aux noms d'applications et des éditeurs.

Notre mission est de :

    Enrichir les données d'IAP en ajoutant le titre de l'application et le nom de l'éditeur, en faisant correspondre les URL de logos avec celles de la source externe.

    Analyser les données enrichies pour tirer des insights et créer une présentation brève (maximum 3 diapositives) à destination des éditeurs d'applications, en se concentrant sur l'impact du confinement dû au Covid en France.
