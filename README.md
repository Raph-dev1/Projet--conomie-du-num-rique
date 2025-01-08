# Projet-économie-du-numérique

Mon projet permet d'experimenter et de tester la capacité des LLM à modéliser l'effet des propositions politiques optimisées par GPT4o, sur les intentions de vote des individus en fonction de leur profil sociodémographique et de leurs préférences.


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

        #Charger la base de données "sample_individus.csv"
        df = pd.read_csv("sample_individus.csv")

        #Liste des colonnes à ajouter
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

# Ajouter les colonnes non remplies avec des valeurs NaN
    for colonne in colonnes_a_ajouter:
        if colonne not in df.columns:
            df[colonne] = pd.NA

# Sauvegarder le fichier avec les colonnes ajoutées
    output_file_path = "sample_individus_avec_colonnes.csv"
    df.to_csv(output_file_path, index=False)
    print(f"Fichier avec colonnes ajoutées généré : {output_file_path}")

# Ajout des variables &leatoire dans le CSV par MIstral AI
    #
# Exécution de la simulation Monte Carlo
iterations = 150
monte_carlo_results = monte_carlo_simulation(iterations, df, propositions_df, scenarios)

# Agrégation des résultats
final_results = {}
for scenario, results in monte_carlo_results.items():
    aggregated = pd.DataFrame(results).mean().round(0).astype(int)
    final_results[scenario] = aggregated

# Affichage des résultats
for scenario, result in final_results.items():
    print(f"Résultats moyens pour {scenario} :")
    print(result.to_string())
    print("\n")
