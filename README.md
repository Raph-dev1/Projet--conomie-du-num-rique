# Projet-économie-du-numérique

Mon projet permet d'experimenter et de tester la capacité des LLM à modéliser l'effet des propositions politiques optimisées par GPT4o, sur les intentions de vote des individus en fonction de leur profil sociodémographique et de leurs préférences.

Le code nécéssite la base de données : individus_bdd.csv


# Génération d'une base de données de 150 individus

    import numpy as np
    import pandas as pd
    import random

    # Charger la base de données "individus_bdd"
    df = pd.read_csv("individus_bdd.csv")

# Ajouter une colonne "Age" avec des valeurs aléatoires entre 18 et 99
    df['Age'] = [random.randint(18, 99) for _ in range(len(df))]

# Sélectionner 150 individus aléatoires
    def select_random_individuals(df, count=150):
        if len(df) < count:
            print("Le DataFrame contient moins de 150 individus, tous seront inclus.")
            return df
        return df.sample(n=count, random_state=42)

# Sélectionner les individus finaux
    Individus_EXP = select_random_individuals(df)

# Sauvegarder les fichiers

    Individus_EXP.to_csv("sample_individus.csv", index=False)  # Fichier avec les 150 individus sélectionnés
    print("sample_individus.csv généré.")

#Ce code génère un profil pour les 150 individus choisis au hasard
  
    df = pd.read_csv("sample_individus.csv")
    # Charger la base de données "sample_individus.csv"
# Liste des colonnes à ajouter
    colonnes_a_ajouter = [
        "statut_matrimonial",
        "niveau_education",
        "statut_professionnel",
        "quartier",
        "revenu",
        "statut_logement",
        "type_logement",
        "mode_transport",
        "distance_domicile_travail",
        "affiliation_politique",
        "priorités_politiques",
        "participation_électorale",
        "fréquence_discussions_politiques",
        "confiance_institutions",
        "engagement_politique",
        "influence_sociale",
        "taille_réseau_social",
        "activité_en_ligne",
        "opinion_immigration",
        "opinion_fiscalité",
        "opinion_énergies",
        "probabilité_changement_avis",
        "réactivité_campagnes",
        "interactions_interquartiers",
        "nombre_enfants",
    ]
# Ajouter les colonnes non remplies 
    for colonne in colonnes_a_ajouter:
        if colonne not in df.columns:
            df[colonne] = pd.NA

# Sauvegarder le fichier avec les colonnes ajoutées
    output_file_path = "sample_individus_avec_colonnes.csv"
    df.to_csv(output_file_path, index=False)
    print(f"Fichier avec colonnes ajoutées généré : {output_file_path}")       


# Ajout des variables &leatoire dans le CSV par MIstral AI
    import time
    from mistralai import Mistral

# Initialisation avec votre clé API
    api_key = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    model = "mistral-large-latest"
    client = Mistral(api_key=api_key)

    # Charger la base de données à compléter
    df = pd.read_csv("sample_individus_avec_colonnes.csv")

# Fonction pour demander à Mistral de compléter les données manquantes
    def complete_missing_data_with_mistral(data):
        prompt = f"""
        Voici un fichier CSV contenant des données d'individus avec des colonnes incomplètes. Complétez les données              manquantes de manière réaliste et cohérente en respectant les valeurs possibles suivantes :

    - Statut matrimonial : Divorce, Veuf, Marie, Celibataire.
    - Niveau education : Pas de diplome, Doctorat, Licence, Bac, Master.
    - Statut professionnel : Indépendant, Retraite, Etudiant, Salarie, Chomeur.
    - Quartier : Banlieue, Zone industrielle, Centre ville.
    - Revenu : Moins de 20k€, entre 20 et 40k€, Entre 40 et 60k€, Plus de 60k€.
    - Statut logement : Locataire, Proprietaire.
    - Type de logement : Maison, Appartement.
    - Mode de transport : Velo, Transport en commun, Voiture personnelle, Covoiturage, Teletravail, A pied.
    - Distance domicile-travail : Moins de 1 km, Entre 1 et 5 km, Entre 5 et 10 km, Plus de 10 km.
    - Affiliation politique : droite, centre, gauche.
    - Priorités politiques : Éducation, Environnement, Économie, Sécurité, Santé.
    - Participation électorale : Régulière, Occasionnelle, Jamais.
    - Fréquence de discussions politiques : Faible, Moyenne, forte.
    - Confiance dans les institutions : Faible, Moyenne, Forte.
    - Engagement politique : Oui, Non.
    - Influence sociale : Isolé, Leader, Suiveur.
    - Taille du réseau social : Petit, Moyen, Grand.
    - Activité en ligne : Actif, Passif, Inactif.
    - Opinion sur immigration : Négative, Positive, Neutre.
    - Opinion sur la fiscalité : Pour une baisse, Pour une hausse, Neutre.
    - Opinion sur les énergies : Fossiles, Renouvelables, Mixte.
    - Probabilité de changer d'avis : Faible, Moyenne, Forte.
    - Réactivité aux campagnes : Faible, Moyenne, Forte.
    - Interactions interquartiers : Rare, Moyenne, Fréquente.
    - Nombre d'enfants : 0, 1, 2, 3, 4.

    Complétez le fichier ci-dessous :
    {data}
    Fournissez un fichier CSV complété comme réponse.
    """

    try:
        response = client.chat.complete(
            model=model,
            messages=[{"role": "user", "content": prompt}],
        )
        return response.choices[0].message.content
    except Exception as e:
        print(f"Erreur lors de l'appel à Mistral : {e}")
        return None

    # Fonction pour diviser le DataFrame en lots
    def split_dataframe(df, batch_size):
        for i in range(0, len(df), batch_size):
            yield df.iloc[i:i + batch_size]

    # Taille des lots 25
    batch_size = 25

    # Initialiser un DataFrame pour les résultats
    results = []

# Traiter chaque lot
    for batch in split_dataframe(df, batch_size):
        csv_data = batch.to_csv(index=False)
        completed_csv = complete_missing_data_with_mistral(csv_data)

        if completed_csv:
            # Nettoyer et charger les données du lot
            raw_content = completed_csv.splitlines()
            csv_lines = []
            inside_csv = False

            for line in raw_content:
                if "```csv" in line:  # Début du CSV
                    inside_csv = True
                    continue
                if "```" in line and inside_csv:  # Fin du CSV
                    inside_csv = False
                    continue
                if inside_csv:
                    csv_lines.append(line.strip())

            cleaned_csv = "\n".join(csv_lines)

# Charger les données nettoyées
            from io import StringIO
            try:
                batch_df = pd.read_csv(StringIO(cleaned_csv))
                results.append(batch_df)
            except pd.errors.ParserError as e:
                print(f"Erreur de parsing CSV pour un lot : {e}")
                continue

# Combiner tous les lots en un seul DataFrame
    if results:
        final_df = pd.concat(results, ignore_index=True)
        output_file_path = "Individus_EXP_complet.csv"
        final_df.to_csv(output_file_path, index=False)
        print(f"Fichier complet généré avec succès : {output_file_path}")
    else:
        print("Aucun résultat final disponible.")


