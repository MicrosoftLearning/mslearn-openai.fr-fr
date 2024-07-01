---
lab:
  title: Implémenter la génération augmentée de récupération (RAG) avec Azure OpenAI Service
---

# Implémenter la génération augmentée de récupération (RAG) avec Azure OpenAI Service

Azure OpenAI Service vous permet d’utiliser vos propres données avec l’intelligence du LLM sous-jacent. Vous pouvez limiter le modèle afin qu’il utilise uniquement vos données pour les rubriques pertinentes, ou les fusionner avec les résultats du modèle préentraîné.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels qui travaille pour Margie’s Travel Agency. Vous allez découvrir comment utiliser l’IA générative pour rendre les tâches de codage plus faciles et plus efficaces. Les techniques utilisées dans l’exercice peuvent être appliquées à d’autres fichiers de code, langages de programmation et cas d’usage.

Cet exercice prend environ **20** minutes.

## Provisionner une ressource Azure OpenAI

Si vous n’en avez pas déjà une, approvisionnez une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au **portail Azure** à l’adresse `https://portal.azure.com`.
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : *Sélectionner un abonnement Azure approuvé pour l’accès à Azure OpenAI Service*
    - **Groupe de ressources** : *sélectionnez ou créez un groupe de ressources*.
    - **Région** : *Choisir de manière **aléatoire** une région parmi les suivantes*\*
        - Australie Est
        - Est du Canada
        - USA Est
        - USA Est 2
        - France Centre
        - Japon Est
        - Centre-Nord des États-Unis
        - Suède Centre
        - Suisse Nord
        - Sud du Royaume-Uni
    - **Nom** : *un nom unique de votre choix*
    - **Niveau tarifaire** : Standard S0

    > \* Les ressources Azure OpenAI sont limitées par des quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un abonnement avec d’autres utilisateurs. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.

## Déployer un modèle

Azure OpenAI fournit un portail web appelé **Azure OpenAI Studio**, que vous pouvez utiliser pour déployer, gérer et explorer des modèles. Vous allez commencer votre exploration d’Azure OpenAI en utilisant Azure OpenAI Studio pour déployer un modèle.

1. Sur la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Accéder à Azure OpenAI Studio** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur.
2. Dans Azure OpenAI Studio, sur la page **Déploiements**, affichez vos déploiements de modèles existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo-16k *(doit être ce modèle pour utiliser les fonctionnalités de cet exercice)*
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *Un nom unique de votre choix. Vous allez utiliser ce nom plus tard dans le labo.*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Type de déploiement** : Standard
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Observer un comportement de conversation normal sans ajouter vos propres données

Avant de connecter Azure OpenAI à vos données, observons d’abord comment le modèle de base répond aux requêtes sans aucune donnée de base.

1. Dans **Azure OpenAI Studio** sur `https://oai.azure.com`, dans la section **Terrain de jeu**, sélectionnez la page **Conversation**. La page du terrain de jeu **Conversation** se compose de trois sections principales :
    - **Définition** : utilisée pour définir le contexte pour les réponses du modèle.
    - **Session de conversation** : utilisée pour envoyer des messages de conversation et voir les réponses.
    - **Configuration** : utilisée pour configurer les paramètres du modèle de déploiement.
2. Dans la section **Configuration**, assurez-vous que votre modèle de déploiement est sélectionné.
3. Dans la section **Définition**, sélectionnez le modèle de message système par défaut pour définir le contexte de la session de conversation. Le message système par défaut est *Vous êtes un assistant IA qui aide les personnes à trouver des informations*.
4. Dans la **Session de conversation**, soumettez les requêtes suivantes et passez en revue les réponses :

    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    Essayez des questions similaires sur le tourisme et les lieux de séjour pour d’autres endroits qui seront inclus dans nos données de base, comme Londres ou San Francisco. Vous obtiendrez probablement des réponses complètes sur les quartiers, et quelques faits généraux sur la ville.

## Connecter vos données dans le terrain de jeu de conversation

Vous allez maintenant ajouter des données pour une agence de voyage fictive appelée *Margie’s Travel*. Vous verrez ensuite comment le modèle Azure OpenAI répond lorsqu’il utilise les brochures de Margie’s Travel comme données de base.

1. Dans un nouvel onglet de navigateur, téléchargez les données archivées d’une brochure à partir de `https://aka.ms/own-data-brochures`. Extrayez les brochures dans un dossier sur votre PC.
1. Dans Azure OpenAI Studio, dans le terrain de jeu **Conversation**, dans la section **Définition**, sélectionnez **Ajouter vos données**.
1. Sélectionnez **Ajouter une source de données** et choisissez **Charger des fichiers**.
1. Vous devez créer un compte de stockage et une ressource Recherche Azure AI. Dans la liste déroulante de la ressource de stockage, sélectionnez **Créer une ressource de stockage Blob Azure**, puis créez un compte de stockage avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Sélectionner le même groupe de ressources que celui de votre ressource Azure OpenAI*
    - **Nom du compte de stockage** : *entrez un nom unique*
    - **Région** : *Sélectionner la même région que celle de votre ressource Azure OpenAI*
    - **Redondance** : stockage localement redondant (LRS)

1. Pendant la création de la ressource du compte de stockage, revenez à Azure OpenAI Studio et sélectionnez **Créer une ressource Recherche Azure AI** avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *Sélectionner le même groupe de ressources que celui de votre ressource Azure OpenAI*
    - **Nom du service** : *entrez un nom unique*
    - **Emplacement** : *Sélectionner le même emplacement que celui de votre ressource Azure OpenAI*
    - **Niveau tarifaire** : De base

1. Attendez que votre ressource de recherche soit déployée, puis revenez à Azure AI Studio.
1. Dans **Ajouter des données**, entrez les valeurs suivantes pour votre source de données, puis sélectionnez **Suivant**.

    - **Sélectionner la source de données** : Charger des fichiers
    - **Abonnement** : votre abonnement Azure.
    - **Sélectionner la ressource Stockage Blob Azure** : *Utiliser le bouton **Actualiser** pour reremplir la liste, puis choisir la ressource de stockage que vous avez créée*
        - Activer CORS à l’invite
    - **Sélectionner la ressource Recherche Azure AI** : *Utiliser le bouton **Actualiser** pour reremplir la liste, puis choisir la ressource de recherche que vous avez créée*
    - **Entrer le nom de l’index** : `margiestravel`
    - **Ajouter la recherche vectorielle à cette ressource de recherche** : décochée
    - **Je reconnais que la connexion à un compte Recherche Azure AI entraîne l’utilisation de mon compte** : coché

1. Dans la page **Charger des fichiers**, chargez les fichiers PDF téléchargés, puis sélectionnez **Suivant**.
1. Dans la page **Gestion des données**, sélectionnez le type de recherche par **mot clé** dans la liste déroulante, puis **Suivant**.
1. Dans la page **Connexion de données**, sélectionnez **Clé API**.
1. Dans la page **Vérifier, puis terminer**, sélectionnez **Enregistrer et fermer** pour ajouter vos données. Cette opération peut prendre quelques minutes, pendant lesquelles vous devez laisser votre fenêtre ouverte. Une fois l’opération terminée, vous verrez la source de données, la ressource de recherche et l’index spécifiés dans la section **Définition**.

    > **Conseil** : Parfois, la connexion entre votre nouvel index de recherche et Azure OpenAI Studio prend trop de temps. Si vous avez attendu quelques minutes et qu’il n’a toujours pas été connecté, vérifiez vos ressources Recherche IA dans le portail Azure. Si vous voyez l’index terminé, vous pouvez déconnecter la connexion de données dans Azure OpenAI Studio et l’ajouter à nouveau en spécifiant une source de données Recherche Azure AI et en sélectionnant votre nouvel index.

## Discuter avec un modèle ancré dans vos données

Maintenant que vous avez ajouté vos données, posez les mêmes questions que précédemment et voyez en quoi la réponse diffère.

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
```

Vous remarquerez une réponse très différente cette fois, avec des détails sur certains hôtels et une mention de Margie’s Travel, ainsi que des références à l’endroit d’où proviennent les informations fournies. Si vous ouvrez la référence PDF listée dans la réponse, vous verrez les mêmes hôtels que ceux fournis par le modèle.

Essayez de l’interroger sur d’autres villes incluses dans les données de base, à savoir Dubaï, Las Vegas, Londres et San Francisco.

> **Remarque** : **Ajouter vos données** est toujours en préversion, et peut ne pas toujours se comporter comme prévu pour cette fonctionnalité, par exemple fournir une référence incorrecte pour une ville non incluse dans les données de base.

## Connectez votre application à vos propres données

Explorons maintenant comment connecter votre application pour utiliser vos propres données.

### Préparer le développement d’une application dans Visual Studio Code

Examinons maintenant l’utilisation de vos propres données dans une application qui utilise le SDK Azure OpenAI Service. Vous allez développer votre application à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : Si vous avez déjà cloné le dépôt **mslearn-openai**, ouvrez-le dans Visual Studio Code. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
2. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-openai` vers un dossier local (peu importe quel dossier).
3. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.

    > **Remarque** : Si Visual Studio Code affiche un message contextuel qui vous invite à approuver le code que vous ouvrez, cliquez sur l’option **Oui, je fais confiance aux auteurs** dans la fenêtre contextuelle.

4. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

## Configuration de votre application

Les applications pour C# et Python ont été fournies, et les deux applications présentent les mêmes fonctionnalités. Tout d’abord, vous allez compléter certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI.

1. Dans Visual Studio Code, dans le volet **Explorateur**, accédez au dossier **Labfiles/06-use-own-data**, puis développez le dossier **CSharp** ou **Python**, selon le langage que vous préférez. Chaque dossier contient les fichiers propres à chaque langage pour une application dans laquelle vous allez intégrer les fonctionnalités Azure OpenAI.
2. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code et ouvrez un terminal intégré. Installez ensuite le package du SDK Azure OpenAI en exécutant la commande appropriée pour le langage de votre choix :

    **C# :**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python** :

    ```
    pip install openai==1.13.3
    ```

3. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de configuration pour le langage de votre choix

    - **C#** : appsettings.json
    - **Python** : .env
    
4. Mettez à jour les valeurs de configuration pour inclure :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le **nom de déploiement** que vous avez spécifié pour votre modèle de déploiement (disponible dans la page **Déploiements** dans Azure OpenAI Studio).
    - Le point de terminaison de votre service de recherche (valeur **url** dans la page de vue d’ensemble de votre ressource de recherche dans le portail Azure).
    - Une **clé** de votre ressource de recherche (disponible sur la page **Clés** de votre ressource de recherche dans le portail Azure. Vous pouvez utiliser l’une des clés d’administration).
    - Le nom de l’index de recherche (qui doit être `margiestravel`).
1. Enregistrez le fichier de configuration.

### Ajouter du code pour utiliser Azure OpenAI Service

Vous êtes maintenant prêt à utiliser le SDK Azure OpenAI pour consommer votre modèle déployé.

1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code du langage de votre choix et remplacez le commentaire ***Configurer votre source de données*** par du code pour ajouter la bibliothèque Azure OpenAI SDK :

    **C#** : ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python** : ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. Passez en revue le reste du code et notez l’utilisation des *extensions* dans le corps de la requête qui est utilisée pour fournir des informations sur les paramètres de la source de données.

3. Enregistrez les modifications apportées au fichier de code.

## Exécuter votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre prompt à votre modèle et observer la réponse. Vous remarquerez que la seule différence entre les différentes options est le contenu du prompt, tous les autres paramètres (tels que le nombre de jetons et la température) restent identiques pour chaque prompt.

1. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python ownData.py`

    > **Conseil** : vous pouvez utiliser l’icône **Agrandir la taille du volet** (**^**) dans la barre d’outils du terminal pour afficher plus de texte sur la console.

2. Passez en revue la réponse au prompt `Tell me about London`, qui devrait inclure une réponse, ainsi que quelques détails des données utilisées pour fonder le prompt, qui a été obtenu à partir de votre service de recherche.

    > **Conseil** : Si vous souhaitez voir les citations de votre index de recherche, définissez la variable ***montrer les citations*** en haut du fichier de code avec la valeur **true**.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de la supprimer dans le **portail Azure** sur `https://portal.azure.com`. Veillez également à inclure le compte de stockage et la ressource de recherche, car ceux-ci peuvent entraîner un coût relativement élevé.
