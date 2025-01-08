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
# Charger la base de données "sample_individus.csv"
df = pd.read_csv("sample_individus.csv")

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

# Ajouter les colonnes non remplies avec des valeurs NaN
for colonne in colonnes_a_ajouter:
    if colonne not in df.columns:
        df[colonne] = pd.NA

# Sauvegarder le fichier avec les colonnes ajoutées
output_file_path = "sample_individus_avec_colonnes.csv"
df.to_csv(output_file_path, index=False)
print(f"Fichier avec colonnes ajoutées généré : {output_file_path}")

# Ajout des scénarios politiques
propositions_data = {
    "Courant politique": ["Gauche", "Centre", "Droite"],
    "Valeur principale": ["Égalité sociale", "Modération", "Tradition"],
    "Propositions Initiales": [
        "Augmentation du SMIC à 1500 € net",
        "Introduction de la proportionnelle",
        "Réduction de l'immigration légale"
    ],
    "Propositions Revisitées": [
        "SMIC à 1500 € + aides aux entreprises",
        "Réforme proportionnelle ciblée",
        "Politique d'immigration avec intégration"
    ]
}
propositions_df = pd.DataFrame(propositions_data)

# Scénarios
scenarios = {
    "Scénario 1": "Propositions initiales réelles pour tous les partis.",
    "Scénario 2 - optimisé Gauche": "Optimisation par parti : Gauche applique ses propositions revisitées contre les initiales des autres.",
    "Scénario 2 - optimisé Centre": "Optimisation par parti : Centre applique ses propositions revisitées contre les initiales des autres.",
    "Scénario 2 - optimisé Droite": "Optimisation par parti : Droite applique ses propositions revisitées contre les initiales des autres.",
    "Scénario 3": "Proposition utopique pour la Gauche (SMIC à 6000 €)."
}

# Fonction d'ajustement des intentions de vote prenant en compte toutes les variables
def adjust_vote_intentions(individual, proposition, is_optimized=False, utopian=False):
    score = 0

    # Influence par le revenu
    if proposition["Courant politique"] == "Gauche" and individual["Revenu"] in ["<20k€", "20-40k€"]:
        score += 3
    elif proposition["Courant politique"] == "Centre" and individual["Revenu"] in ["40-60k€"]:
        score += 3
    elif proposition["Courant politique"] == "Droite" and individual["Revenu"] in [">60k€"]:
        score += 3

    # Influence par l'âge
    if proposition["Courant politique"] == "Gauche" and individual["Âge"] < 30:
        score += 3
    elif proposition["Courant politique"] == "Centre" and 30 <= individual["Âge"] < 50:
        score += 3
    elif proposition["Courant politique"] == "Droite" and individual["Âge"] >= 50:
        score += 3

    # Influence par le niveau d'éducation
    if individual["Niveau éducation"] in ["Pas de diplôme", "Bac"] and proposition["Courant politique"] == "Gauche":
        score += 2
    elif individual["Niveau éducation"] in ["Master", "Doctorat"] and proposition["Courant politique"] == "Droite":
        score += 2

    # Influence par le statut professionnel
    if individual["Statut professionnel"] == "Étudiant" and proposition["Courant politique"] == "Gauche":
        score += 2
    elif individual["Statut professionnel"] == "Retraité" and proposition["Courant politique"] == "Droite":
        score += 2

    # Influence par les opinions
    if individual["Opinion sur l'immigration"] == "Positive" and proposition["Courant politique"] == "Gauche":
        score += 3
    elif individual["Opinion sur l'immigration"] == "Négative" and proposition["Courant politique"] == "Droite":
        score += 3

    # Influence par les priorités politiques
    if individual["Priorités politiques"] == "Santé" and proposition["Courant politique"] == "Gauche":
        score += 3
    elif individual["Priorités politiques"] == "Économie" and proposition["Courant politique"] == "Centre":
        score += 3
    elif individual["Priorités politiques"] == "Sécurité" and proposition["Courant politique"] == "Droite":
        score += 3

    # Influence par la réactivité aux campagnes
    if individual["Réactivité aux campagnes"] == "Forte":
        score += 2

    # Influence par la confiance dans les institutions
    if individual["Confiance dans les institutions"] >= 4 and proposition["Courant politique"] == "Centre":
        score += 3

    # Bonus pour propositions revisitées
    if is_optimized:
        score += 5

    # Bonus pour une proposition utopique
    if utopian:
        score += 8

    return score

# Simulations Monte Carlo avec votes Blanc/Nul dynamiques
def monte_carlo_simulation(iterations, df, propositions_df, scenarios):
    monte_carlo_results = {scenario: [] for scenario in scenarios}

    for _ in range(iterations):
        for scenario, description in scenarios.items():
            vote_intentions = {"Gauche": np.zeros(len(df)), "Centre": np.zeros(len(df)), "Droite": np.zeros(len(df)), "Blanc/Nul": np.zeros(len(df))}

            for index, row in propositions_df.iterrows():
                if scenario == f"Scénario 2 - {row['Courant politique']}":
                    proposition = {"Courant politique": row["Courant politique"], "Propositions Initiales": row["Propositions Revisitées"]}
                    is_optimized = True
                    utopian = False
                elif scenario == "Scénario 3" and row["Courant politique"] == "Gauche":
                    proposition = {"Courant politique": "Gauche", "Propositions Initiales": "SMIC à 6000 €"}
                    is_optimized = False
                    utopian = True
                else:
                    proposition = row
                    is_optimized = False
                    utopian = False

                for i, individual in df.iterrows():
                    score = adjust_vote_intentions(individual, proposition, is_optimized, utopian)
                    if individual["Confiance dans les institutions"] < 3 and np.random.rand() < 0.5:
                        vote_intentions["Blanc/Nul"][i] += 1
                    else:
                        vote_intentions[proposition["Courant politique"]][i] += score

            votes = []
            for i in range(len(df)):
                scores = {party: vote_intentions[party][i] for party in vote_intentions}
                votes.append(max(scores, key=scores.get))

            vote_counts = pd.Series(votes).value_counts().reindex(["Gauche", "Centre", "Droite", "Blanc/Nul"], fill_value=0)
            monte_carlo_results[scenario].append(vote_counts)

    return monte_carlo_results

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
