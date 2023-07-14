---
lab:
  title: Utiliser vos propres données avec Azure OpenAI
---

# Utiliser vos propres données avec Azure OpenAI

Azure OpenAI Service vous permet d’utiliser vos propres données avec l’intelligence du LLM sous-jacent. Vous pouvez limiter le modèle afin qu’il utilise uniquement vos données pour les rubriques pertinentes, ou les fusionner avec les résultats du modèle préentraîné.

Cet exercice prend environ **20** minutes.

## Avant de commencer

Vous devez avoir un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service. 

- Pour obtenir un abonnement gratuit à Azure, visitez [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Pour demander l’accès à Azure OpenAI Service, visitez [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionner une ressource Azure OpenAI

Avant de pouvoir utiliser des modèles Azure OpenAI, vous devez provisionner une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com?azure-portal=true).
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : Un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.
    - **Groupe de ressources** : Créez un nouveau groupe de ressources en lui attribuant le nom de votre choix.
    - **Région** : Choisissez une région disponible.
    - **Nom** : Un nom unique de votre choix.
    - **Niveau tarifaire** : Standard S0
3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.

## Déployer un modèle

Pour converser avec Azure OpenAI, vous devez d’abord déployer un modèle à utiliser via **Azure OpenAI Studio**. Une fois le modèle déployé, nous l’utiliserons avec le terrain de jeu et utiliserons nos données pour baser ses réponses.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur. Vous pouvez aussi accéder à [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) directement.
2. Dans Azure OpenAI Studio, créez un déploiement avec les paramètres suivants :
    - **Nom du modèle** : gpt-35-turbo
    - **Version du modèle** : *Utiliser la version par défaut*
    - **Nom du déploiement** : text-turbo

## Observer un comportement de conversation normal sans ajouter vos propres données

Avant de connecter Azure OpenAI à vos données, observez d’abord comment le modèle de base répond aux requêtes sans données de base.

1. Accédez au terrain de jeu **Conversation** et vérifiez que le modèle `gpt-35-turbo` que vous avez déployé est sélectionné dans le volet **Configuration** (il doit s’agir de la valeur par défaut, si vous n’avez qu’un seul modèle déployé).
1. Entrez les invites suivantes et observez la sortie.

    ```code
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```code
    What are some facts about New York?
    ```

1. Essayez des questions similaires sur le tourisme et les lieux de séjour pour d’autres endroits qui seront inclus dans nos données de base, comme Londres ou San Francisco. Vous obtiendrez probablement des réponses complètes sur les quartiers, et quelques faits généraux sur la ville.

## Connecter vos données dans le terrain de jeu de conversation

Ensuite, ajoutez vos données dans le terrain de jeu de conversation pour voir comment il répond avec vos données en tant que base.

1. [Téléchargez les données](https://aka.ms/own-data-brochures) que vous utiliserez à partir de GitHub. Extrayez les PDF du fichier `.zip` fourni.
1. Accédez au terrain de jeu **Conversation**, puis sélectionnez *Ajouter vos données* dans le volet Configuration de l’Assistant.
1. Sélectionnez **Ajouter une source de données** et choisissez *Charger des fichiers* dans la liste déroulante.
1. Vous devez créer un compte de stockage et une ressource Recherche cognitive Azure. Dans la liste déroulante de la ressource de stockage, sélectionnez **Créer une ressource de stockage Blob Azure**, puis créez un compte de stockage avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *même abonnement que votre ressource Azure OpenAI*
    - **Groupe de ressources** : *même groupe de ressources que votre ressource Azure OpenAI*
    - **Nom du compte de stockage** : *entrez un nom global unique*.
    - **Région** : *même région que votre ressource Azure OpenAI*
    - **Redondance** : stockage localement redondant (LRS)

1. Une fois la ressource créée, revenez à Azure OpenAI Studio et sélectionnez **Créer une ressource Recherche cognitive Azure** avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *même abonnement que votre ressource Azure OpenAI*
    - **Groupe de ressources** : *même groupe de ressources que votre ressource Azure OpenAI*
    - **Nom du service** : *entrez un nom global unique*
    - **Emplacement** : *même emplacement que votre ressource Azure OpenAI*
    - **Niveau tarifaire** : De base

1. Attendez que votre ressource de recherche ait été déployée, puis revenez à Azure AI Studio et actualisez la page.
1. Dans **Ajouter des données**, entrez les valeurs suivantes pour votre source de données.

    - **Sélectionner la source de données** : Charger des fichiers
    - **Sélectionner la ressource Stockage Blob Azure** : *choisissez la ressource de stockage que vous avez créée*
        - Activer CORS à l’invite
    - **Sélectionner la ressource Recherche cognitive Azure** : *choisissez la ressource de recherche que vous avez créée*
    - **Entrez le nom de l’index** : margiestravel
    - Cochez la case.

1. Dans la page **Charger des fichiers**, chargez les fichiers PDF que vous avez téléchargés.
1. Sélectionnez **Enregistrer et fermer** pour ajouter vos données. Cette opération peut prendre quelques minutes, pendant lesquelles vous devez laisser votre fenêtre ouverte. Une fois l’opération terminée, vous verrez la source de données, la ressource de recherche et l’index spécifiés dans le volet **Configuration de l’Assistant**.

## Discuter avec un modèle ancré dans vos données

Maintenant que vous avez ajouté vos données, posez les mêmes questions que précédemment et voyez en quoi la réponse diffère.

```code
I'd like to take a trip to New York. Where should I stay?
```

```code
What are some facts about New York?
```

Vous remarquerez une réponse très différente cette fois, avec des détails sur certains hôtels et une mention de Margie’s Travel, ainsi que des références à l’endroit d’où proviennent les informations fournies. Si vous ouvrez la référence PDF listée dans la réponse, vous verrez les mêmes hôtels que ceux fournis par le modèle.

Essayez de l’interroger sur d’autres villes incluses dans les données de base, à savoir Dubaï, Las Vegas, Londres et San Francisco.

> **Remarque** : **Ajouter vos données** est toujours en préversion, et peut ne pas toujours se comporter comme prévu pour cette fonctionnalité, par exemple fournir une référence incorrecte pour une ville non incluse dans les données de base.

## Nettoyage

Lorsque vous en avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de la supprimer dans le [portail Azure](https://portal.azure.com/?azure-portal=true). Veillez également à inclure le compte de stockage et la ressource de recherche, car ceux-ci peuvent entraîner un coût relativement élevé.
