# Python-Jointure-de-tables-avec-hash-d-images-et-analyse-du-Covid-sur-les-applications-de-google-
Python : Jointure de tables de données avec hash d'images et analyse du Covid sur les applications de google play store


En juillet 2020, nous avons commencé à travailler sur le secteur des applications mobiles. Il est rapidement devenu évident que deux informations clés étaient nécessaires pour réaliser des analyses pertinentes : le titre de l'application et le nom de l'éditeur. En collectant les données des reçus de l'App Store (iOS) et du Play Store (Android), nous avons pu obtenir des informations précieuses sur les achats in-app (IAP), c'est-à-dire les transactions effectuées à l'intérieur des applications.

Cependant, les reçus de l'App Store fournissent peu d'informations : seul le nom de l'IAP, l'URL du logo de l'application et le prix sont disponibles. En utilisant des données externes, nous avons constitué une base de données des URL de logos associées aux noms des applications et des éditeurs.

Tâche :

Nous disposons de deux ensembles de données :

    Un extrait contenant les données d'IAP.

    Une source externe qui associe les URL de logos aux noms d'applications et des éditeurs.

Notre mission est de :

    Enrichir les données d'IAP en ajoutant le titre de l'application et le nom de l'éditeur, en faisant correspondre les URL de logos avec celles de la source externe.

    Analyser les données enrichies pour tirer des insights et créer une présentation brève (maximum 3 diapositives) à destination des éditeurs d'applications, en se concentrant sur l'impact du confinement dû au Covid en France.



 # Problematique 1

Problématique :
Étant donné que plusieurs URL de logos existent pour une même application et que les URL ne peuvent pas être directement mises en correspondance avec celles de la source externe par une simple jointure, il est nécessaire d’utiliser une approche basée sur les images pour effectuer une correspondance plus robuste.

Solution :
Une méthode efficace pour résoudre ce problème consiste à utiliser un code Python qui télécharge les images à partir des URL, génère des empreintes (ou hashes) pour chaque image, et compare ces empreintes pour déterminer quelle image dans le jeu de données externe correspond à une image dans les données IAP.

Voici un aperçu du code Python qui permet de résoudre ce problème (Final.ipynb) :

    import pandas as pd
import requests
import imagehash
from PIL import Image
from io import BytesIO

# Chemins des fichiers
table_file = r'C:\Users\wawa1\Downloads\ENTRETIEN TECHNIQUE\technical_test_table_extract.csv'
external_file = r'C:\Users\wawa1\Downloads\ENTRETIEN TECHNIQUE\technical_test_external_source_extract.csv'
output_file = r'C:\Users\wawa1\Downloads\ENTRETIEN TECHNIQUE\FINAL.csv'

# Chargement des jeux de données
table_df = pd.read_csv(table_file)
external_df = pd.read_csv(external_file)

# Fonction pour télécharger et générer un hash d'image
def get_image_hash(url):
    try:
        response = requests.get(url, timeout=20)
        if response.status_code == 200:
            image = Image.open(BytesIO(response.content)).convert("RGB")
            return str(imagehash.phash(image))
        else:
            print(f"Échec du téléchargement de {url}: Code de statut {response.status_code}")
    except Exception as e:
        print(f"Erreur lors du téléchargement de {url}: {e}")
    return None

# Fonction pour calculer la distance de Hamming
def hamming_distance(hash1, hash2):
    return sum(c1 != c2 for c1, c2 in zip(hash1, hash2))

# Fonction pour trouver la meilleure correspondance basée sur la distance de Hamming
def find_best_match(table_hash, external_df, threshold=5):
    best_match = None
    min_distance = float('inf')
    
    for _, row in external_df.iterrows():
        external_hash = row["image_hash"]
        if pd.isna(external_hash):
            continue
        distance = hamming_distance(table_hash, external_hash)
        if distance < min_distance and distance <= threshold:
            min_distance = distance
            best_match = row
    
    return best_match

# Calcul des hashes pour les images
table_df["image_hash"] = table_df["product_url_img"].apply(get_image_hash)
external_df["image_hash"] = external_df["icon"].apply(get_image_hash)

# Suppression des lignes avec des hashes manquants
table_df.dropna(subset=["image_hash"], inplace=True)
external_df.dropna(subset=["image_hash"], inplace=True)

# Correspondance des lignes de table_df avec celles d'external_df en utilisant la distance de Hamming
matched_data = []
for _, table_row in table_df.iterrows():
    table_hash = table_row["image_hash"]
    best_match = find_best_match(table_hash, external_df)
    
    if best_match is not None:
        matched_data.append({
            "product_url_img": table_row["product_url_img"],
            "title": best_match["title"],
            "editor": best_match["editor"]
        })
    else:
        matched_data.append({
            "product_url_img": table_row["product_url_img"],
            "title": None,
            "editor": None
        })

# Création d'un DataFrame avec les données correspondantes
final_df = pd.DataFrame(matched_data)

# Sauvegarde des données enrichies
if "title" in final_df.columns and "editor" in final_df.columns:
    final_df.to_csv(output_file, index=False)
    print(f"Ensemble de données enrichi sauvegardé sous {output_file}")
else:
    print("Aucune correspondance trouvée ! Vérifiez les hashes des images ou le format des jeux de données.")
