# Projet-économie-du-numérique
Mon projet permet d'experimenter et de tester la capacité des LLM à modéliser l'impact des propositions politiques sur les intentions de vote des individus en fonction de leur profil sociodémographique et de leurs préférences.



# Génération d'une base de données de 150 individus
import pandas as pd
import numpy as np
import pandas as pd
import random

    # Charger la base de données "individus_bdd"
df = pd.read_csv("individus_bdd.csv")

    # Ajouter une colonne "Âge" avec des valeurs aléatoires entre 18 et 99
df['Âge'] = [random.randint(18, 99) for _ in range(len(df))]

    # Sélectionner 150 individus aléatoires
def select_random_individuals(df, count=150):
    if len(df) < count:
        print("Le DataFrame contient moins de 150 individus, tous seront inclus.")
        return df
    return df.sample(n=count, random_state=42)

    # Sélectionner les individus finaux
Individus_EXP = select_random_individuals(df)

    # Sauvegarder les fichiers

Individus_EXP.to_csv("individus_EXP.csv", index=False)  # Fichier avec les 150 individus sélectionnés
print("Fichier individus_finaux.csv généré.")

    #Ce code génère un sample de 150 individus, avec un age aléatoire ( 18 à 99 ans)


df = pd.DataFrame({
    'Âge': np.random.randint(18, 101, n_individus),
    'Sexe': np.random.choice(['Homme', 'Femme'], n_individus),
    'Statut matrimonial': np.random.choice(['Divorcé', 'Veuf', 'Marié', 'Célibataire'], n_individus),
    'Niveau éducation': np.random.choice(['Pas de diplôme', 'Doctorat', 'Licence', 'Bac', 'Master'], n_individus),
    'Statut professionnel': np.random.choice(['Indépendant', 'Retraité', 'Étudiant', 'Salarié', 'Chômeur'], n_individus),
    'Quartier': np.random.choice(['Banlieue', 'Zone industrielle', 'Centre-ville'], n_individus),
    'Revenu': np.random.choice(['<20k€', '20-40k€', '40-60k€', '>60k€'], n_individus),
    'Statut logement': np.random.choice(['Locataire', 'Propriétaire'], n_individus),
    'Type de logement': np.random.choice(['Maison', 'Appartement'], n_individus),
    'Mode de transport': np.random.choice(['Vélo', 'Transport en commun', 'Voiture personnelle', 'Covoiturage', 'Télétravail', 'À pied'], n_individus),
    'Distance domicile-travail': np.random.choice(['Moins de 1 km', '1-5 km', '5-10 km', 'Plus de 10 km'], n_individus),
    'Affiliation politique': np.random.choice(['Indépendant', 'Parti X', 'Non affilié', 'Parti Y'], n_individus),
    'Priorités politiques': np.random.choice(['Éducation', 'Environnement', 'Économie', 'Sécurité', 'Santé'], n_individus),
    'Participation électorale': np.random.choice(['Régulière', 'Occasionnelle', 'Jamais'], n_individus),
    'Fréquence de discussions politiques': np.random.choice(['Faible', 'Moyenne', 'Élevée'], n_individus),
    'Confiance dans les institutions': np.random.randint(1, 6, n_individus),
    'Engagement politique': np.random.choice(['Oui', 'Non'], n_individus),
    'Influence sociale': np.random.choice(['Isolé', 'Leader', 'Suiveur'], n_individus),
    'Taille du réseau social': np.random.choice(['Petit', 'Moyen', 'Grand'], n_individus),
    'Activité en ligne': np.random.choice(['Actif', 'Passif', 'Inactif'], n_individus),
    'Opinion sur l\'immigration': np.random.choice(['Négative', 'Positive', 'Neutre'], n_individus),
    'Opinion sur la fiscalité': np.random.choice(['Pour une baisse', 'Pour une hausse', 'Neutre'], n_individus),
    'Opinion sur les énergies': np.random.choice(['Fossiles', 'Renouvelables', 'Mixte'], n_individus),
    'Probabilité de changer d\'avis': np.random.choice(['Faible', 'Moyenne', 'Élevée'], n_individus),
    'Réactivité aux campagnes': np.random.choice(['Faible', 'Moyenne', 'Forte'], n_individus),
    'Interactions interquartiers': np.random.choice(['Rare', 'Moyenne', 'Fréquente'], n_individus),
    'Nombre d\'enfants': np.random.choice([0, 1, 2, 3, 4, 5], n_individus)
})

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
