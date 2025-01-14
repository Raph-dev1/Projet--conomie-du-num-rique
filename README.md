# Projet économie du numérique 2025
Base de données nécessaire : individus_bdd.csv

Ce projet vous permet d'explorer et tester la capacité des modèles de langage (LLM) à modéliser l’effet de propositions politiques optimisées sur les intentions de vote des individus, en fonction de leur profil sociodémographique et de leurs préférences.

Le projet s’appuie sur GPT-4o pour optimiser des propositions politiques, puis utilise l’API du LLM Mistral pour simuler et analyser les intentions de vote de 150 individus. Ces simulations mettent en interaction des profils détaillés (ajustables facilement) avec des propositions issues de programmes politiques français réels, ainsi que des versions optimisées pour maximiser leur attractivité et les intentions de votes.

# Méthodologie :
    
Le projet suit une approche en trois étapes principales :

Préparation des données :
Une base de données contenant 150 individus est générée. Chaque individu est caractérisé par un profil détaillé, comprenant des variables (vides) telles que l'âge, le revenu, les opinions politiques, et d'autres caractéristiques sociodémographiques pertinentes.

Complétion des données :
Les variables dans ces profils sont complétées par l'API Mistral, à l’aide de distributions réalistes et cohérentes définies dans un prompt structuré.

Simulation des votes :
3 scénarios politiques sont simulés :

Propositions initiales : Interaction des individus avec les propositions originales des programmes politiques.

Propositions optimisées : Interaction avec des propositions revisitées et optimisées par GPT-4o, contre les propositions initiales.
-Scénario 2.1 - La Gauche applique ses propositions revisitées contre les initiales des autres.
-Scénario 2.2 - Le Centre applique ses propositions revisitées contre les initiales des autres.
-Scénario 2.3 - La Droite applique ses propositions revisitées contre les initiales des autres.

Propositions utopiques : Par exemple, une proposition très ambitieuse pour un parti spécifique.
Les simulations évaluent comment ces propositions influencent les intentions de vote, en tenant compte des caractéristiques individuelles.
Permet d'oberserver des variations en cas de proposititon utopique/irréaliste, ==> illustre la perception plus générale d'un partis en particullier,

Analyse des résultats
Les résultats sont agrégés pour chaque scénario, en calculant :

Le nombre et la part de votants pour chaque parti.
L’impact différentiel des propositions initiales et optimisées.
La performance de l’API Mistral dans la modélisation et l’influence des intentions de vote.
Ce projet constitue une expérimentation unique sur l’interaction entre profils sociodémographiques, propositions politiques, et outils d’IA, tout en mettant en lumière les dynamiques entre optimisation algorithmique et comportement électoral.

# Explications des parties du code :

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
        Voici un fichier CSV contenant des données d'individus avec des colonnes incomplètes. Complétez les données manquantes de manière réaliste et cohérente en respectant les valeurs possibles suivantes :

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

# tests des scénarios politiques 
    import os
    import pandas as pd
    import numpy as np
    from mistralai import Mistral
    from io import StringIO
    import json

    # Initialisation avec votre clé API
    api_key = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    model = "mistral-large-latest"
    client = Mistral(api_key=api_key)

    # Charger la base de données des individus
    df = pd.read_csv("Individus_EXP_complet.csv")
    batch_size = 10  # Nombre d'individus par lot
    num_batches = int(np.ceil(len(df) / batch_size))

    # Définir les propositions initiales et revisitées
    propositions_data = {
        "Courants politiques": ["Gauche", "Gauche", "Gauche", "Centre", "Centre", "Centre", "Droite", "Droite", "Droite"],
        "Valeurs principales": [
            "Égalité sociale", "Solidarité", "Progressisme", 
            "Pragmatisme", "Compromis", "Modération",
            "Autorité", "Tradition", "Liberté individuelle"
        ],
        "Propositions Initiales": [
            "Augmentation du SMIC à 1 500 € net",
            "Création d'un revenu universel d'existence",
            "Légalisation du cannabis",
            "Réduction du nombre de fonctionnaires",
            "Introduction de la proportionnelle",
            "Suppression de la taxe d’habitation",
            "Réduction de l’immigration légale",
            "Défense des valeurs familiales traditionnelles",
            "Allégement de la fiscalité sur les successions"
        ],
        "Propositions Revisitées": [
            "Revaloriser immédiatement le SMIC à 1 500 € net pour répondre à l’inflation. Inclut des soutiens aux entreprises.",
            "Mise en place d’un revenu progressif ciblant les jeunes et retraités précaires.",
            "Encadrer et légaliser le cannabis pour investir dans la prévention et l’éducation.",
            "Réduction progressive des effectifs non essentiels avec numérisation des services.",
            "Introduction de la proportionnelle pour une meilleure représentation citoyenne.",
            "Suppression totale avec une réforme fiscale équitable.",
            "Mise en œuvre d’une politique d’intégration pour garantir cohésion et sécurité.",
            "Augmentation des allocations pour familles nombreuses.",
            "Réduction drastique des droits de succession"
        ]
    }
    propositions_df = pd.DataFrame(propositions_data)

    # Définir les scénarios
    scenarios = {
        "Scénario 1": "Propositions initiales réelles pour tous les partis.",
        "Scénario 2 - Gauche": "Optimisation par parti : Gauche applique ses propositions revisitées contre les initiales des autres.",
        "Scénario 2 - Centre": "Optimisation par parti : Centre applique ses propositions revisitées contre les initiales des autres.",
        "Scénario 2 - Droite": "Optimisation par parti : Droite applique ses propositions revisitées contre les initiales des autres.",
        "Scénario 3": "Proposition utopique pour la Gauche (SMIC à 6000 €)."
    }
    #Il est possible de modifier les variables d'influence pour l'intention de vote à la ligne 268
    # Fonction pour préparer le prompt et exécuter la simulation avec Mistral
    def mistral_simulation(individuals, propositions, scenarios, retries=3, cooldown=30):
        prompt = f"""
        Vous devez simuler les intentions de vote pour les individus de la base de données fournies en fonction des scénarios politiques.
        Voici les données :
    
        ### INDIVIDUS ###
        Les individus sont décrits avec les caractéristiques suivantes :
        {individuals.to_dict(orient='records')}
    
        ### PROPOSITIONS ###
        Voici les propositions initiales et revisitées pour chaque parti :
        {propositions.to_dict(orient='records')}
    
        ### SCÉNARIOS ###
        Les scénarios à simuler sont :
        {scenarios}
    
        ### INSTRUCTIONS ###
        Pour chaque individu, simulez une interaction avec chaque proposition dans chaque scénario. 
        - Les intentions de vote doivent être ajustées en fonction de toutes les caractéristiques des individus :âge, niveau_education, revenu, statut_professionnel, quartier, affiliation_politique, priorités_politiques, participation_électorale, confiance_institutions, opinion_immigration, opinion_fiscalité, opinion_énergies.Simulez les interactions (tracts, débats, réseaux sociaux) pour influencer les intentions de vote.
        - Dans le Scénario 1, à chaque individus tu dois presenter les propositons initiales contres les propositions initiales des autres.
        - Dans le Scénario 2, présente les propositions revisitées pour chaque parti contre les propositions initiales des autres. 
        - Dans le Scénario 3, présente une proposition utopique pour la Gauche (SMIC à 6000 €), contre les propositions initiales des autres.
        Retournez les résultats sous la forme d'une agrégation des intentions de vote pour chaque parti à chaque scénario (Gauche, Centre, Droite, Blanc/Nul), conserve le meme format de sorties a chaque batsh, ### FORMAT DE RÉPONSE ATTENDU ###
    Retournez les résultats des intentions de vote sous ce format exact. Respectez strictement la structure, les titres, et l'alignement des colonnes pour chaque scénario.
    
    Format imperatif attendu :
    
    ### RÉSULTATS DES SIMULATIONS INTENTIONS DE VOTES  ###
    avec Calculer la moyenne pondérée = strictement nombre de votants partis/ 10 par scenario par batsh pour les pourcentages dans les tableaux,
              
    #### Scénario 1 Propositions initiales
    | Parti      | Nombre de votants | Pourcentage |
    |------------|-------------------|-------------|
    | Gauche     | XX                | XX%         |
    | Centre     | XX                | XX%         |
    | Droite     | XX                | XX%         |
    | Blanc/Nul  | XX                | XX%         |
    
    #### Scénario 2 - Gauche applique ses propositions revisitées
    | Parti      | Nombre de votants | Pourcentage |
    |------------|-------------------|-------------|
    | Gauche     | XX                | XX%         |
    | Centre     | XX                | XX%         |
    | Droite     | XX                | XX%         |
    | Blanc/Nul  | XX                | XX%         |
    
    #### Scénario 2 - Centre applique ses propositions revisitées
    | Parti      | Nombre de votants | Pourcentage |
    |------------|-------------------|-------------|
    | Gauche     | XX                | XX%         |
    | Centre     | XX                | XX%         |
    | Droite     | XX                | XX%         |
    | Blanc/Nul  | XX                | XX%         |
    
    #### Scénario 2 - Droite applique ses propositions revisitées
    || Parti      | Nombre de votants | Pourcentage |
    |------------|-------------------|-------------|
    | Gauche     | XX                | XX%         |
    | Centre     | XX                | XX%         |
    | Droite     | XX                | XX%         |
    | Blanc/Nul  | XX                | XX%         |
    
    #### Scénario 3
    | Parti      | Nombre de votants | Pourcentage |
    |------------|-------------------|-------------|
    | Gauche     | XX                | XX%         |
    | Centre     | XX                | XX%         |
    | Droite     | XX                | XX%         |
    | Blanc/Nul  | XX                | XX%         |
    
    ne pas integrer de ### Détails des simulations.
    
    puis print le resultat du batsh suivant.
    
        """
        
        for attempt in range(retries):
            try:
                response = client.chat.complete(
                    model=model,
                    messages=[{"role": "user", "content": prompt}],
                )
                return response.choices[0].message.content
            except Exception as e:
                print(f"Erreur d'appel à Mistral : {e}")
                if "429" in str(e):  # Gestion de la limite d'appels
                    print(f"Limite atteinte, attente de {cooldown} secondes...")
                    time.sleep(cooldown)
                else:
                    break
        return None  # En cas d'échec

    # Lancer les simulations par lots
    all_results = []
    for i in range(num_batches):
        print(f"Traitement du lot {i+1}/{num_batches}...")
        batch = df.iloc[i * batch_size:(i + 1) * batch_size]
        response = mistral_simulation(batch, propositions_df, scenarios)
        
        if response:
            try:
                # Convertir la réponse JSON en DataFrame
                results = pd.read_json(StringIO(response))
                all_results.append(results)
            except ValueError as e:
                print(f"Erreur dans le traitement de la réponse de Mistral pour le lot {i+1} : {e}")
                print("Résultats :", response)
        else:
            print(f"Aucune réponse valide obtenue pour le lot {i+1}.")

--------------------------
#Copiez l'output de ce dernier scipt dans le press papier
--------------------------
## Agréger tous les résultats en collant l'output à l'emplacement indiqué
    import pandas as pd
    import re
    from collections import defaultdict
    
    # Fonction pour extraire les données des 15 lots à partir d'un texte donné
    def parse_simulation_output(simulation_output):
        """
        Analyse les résultats des simulations intention de votes à partir d'une chaîne de caractères.
        :param simulation_output: Texte contenant les résultats des simulations.
        :return: Dictionnaire structuré des résultats pour chaque scénario.
        """
        scenarios = {
            "Scénario 1": "Propositions initiales",
            "Scénario 2 - Gauche": "Gauche applique ses propositions revisitées",
            "Scénario 2 - Centre": "Centre applique ses propositions revisitées",
            "Scénario 2 - Droite": "Droite applique ses propositions revisitées",
            "Scénario 3": "Proposition utopique pour la Gauche"
        }
    
        # Stockage des résultats agrégés
        results = defaultdict(lambda: defaultdict(int))
    
        # Extraction des blocs par scénario
        for scenario, description in scenarios.items():
            # Recherche du bloc correspondant au scénario
            pattern = rf"#### {scenario}.*?\| Gauche\s+\|\s+(\d+).*?\| Centre\s+\|\s+(\d+).*?\| Droite\s+\|\s+(\d+).*?\| Blanc/Nul\s+\|\s+(\d+)"
            matches = re.findall(pattern, simulation_output, re.DOTALL)
    
            for match in matches:
                results[scenario]["Gauche"] += int(match[0])
                results[scenario]["Centre"] += int(match[1])
                results[scenario]["Droite"] += int(match[2])
                results[scenario]["Blanc/Nul"] += int(match[3])
    
        return results
    
    # Exemple de simulation_output à remplacer par le texte réel
    def calculate_totals_from_text(simulation_output):
        """
        Calcule les totaux pour chaque scénario à partir du texte donné.
        :param simulation_output: Texte contenant les résultats des simulations.
        """
        data = parse_simulation_output(simulation_output)
    
        # Affichage des résultats avec validation
        for scenario, result in data.items():
            total_votes = sum(result.values())
            print(f"### {scenario} ###")
            print("| Parti      | Nombre de votants |")
            print("|------------|-------------------|")
            for party, votes in result.items():
                print(f"| {party:<10} | {votes:<17} |")
            print(f"\nTotal des votants : {total_votes}\n")
    
            # Vérification si le total dépasse 150
            if total_votes > 150:
                print(f"⚠️  Anomalie détectée : Le total des votes ({total_votes}) dépasse 150 pour {scenario}.")
    
    # Placez ici le texte/output des simulations entre """ """
    simulation_output = """ """
    
    # Exécution de l'analyse et calcul des totaux
    calculate_totals_from_text(simulation_output)
    

