---
lab:
  title: Implémenter la génération augmentée de récupération (RAG) avec Azure OpenAI Service
---

# Implémenter la génération augmentée de récupération (RAG) avec Azure OpenAI Service

Azure OpenAI Service vous permet d’utiliser vos propres données avec l’intelligence du LLM sous-jacent. Vous pouvez limiter le modèle afin qu’il utilise uniquement vos données pour les rubriques pertinentes, ou les fusionner avec les résultats du modèle préentraîné.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels qui travaille pour Margie’s Travel Agency. Vous allez découvrir comment utiliser Recherche Azure AI pour indexer vos propres données et les utiliser avec Azure OpenAI pour augmenter les invites.

Cet exercice prend environ **30** minutes.

## Approvisionner des ressources Azure

Pour effectuer cet exercice, vous avez besoin des éléments suivants :

- Ressource Azure OpenAI.
- Une ressource de Recherche Azure AI.
- Une ressource de compte de stockage Azure

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

3. Pendant que la ressource Azure OpenAI est approvisionnée, créez une ressource **Recherche Azure AI** avec les paramètres suivants :
    - **Abonnement** : *abonnement dans lequel vous avez approvisionné votre ressource Azure OpenAI*
    - **Groupe de ressources** : *groupe de ressources dans lequel vous avez provisionné votre ressource Azure OpenAI*
    - **Nom du service** : *nom unique de votre choix*
    - **Emplacement** : *région dans laquelle vous avez approvisionné votre ressource Azure OpenAI*
    - **Niveau tarifaire** : De base
4. Pendant que la ressource Recherche Azure AI est approvisionnée, créez une ressource **Compte de stockage** avec les paramètres suivants :
    - **Abonnement** : *abonnement dans lequel vous avez approvisionné votre ressource Azure OpenAI*
    - **Groupe de ressources** : *groupe de ressources dans lequel vous avez provisionné votre ressource Azure OpenAI*
    - **Nom du compte de stockage** : *nom unique de votre choix*
    - **Région** : *région dans laquelle vous avez approvisionné votre ressource Azure OpenAI*
    - **Performances** : standard
    - **Redondance** : stockage localement redondant (LRS)
5. Une fois les trois ressources déployées avec succès dans votre abonnement Azure, passez-les en revue dans le portail Azure et collectez les informations suivantes (dont vous aurez besoin plus loin dans l’exercice) :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le point de terminaison de votre service Recherche Azure AI (valeur **url** dans la page de vue d’ensemble de votre ressource Recherche Azure AI dans le portail Azure).
    - Une **clé d’administration principale** pour votre ressource Recherche Azure AI (disponible sur la page **Clés** de votre ressource Recherche Azure AI dans le portail Azure).

## Charger vos données

Vous allez ancrer les invites que vous utilisez avec un modèle d’IA générative à l’aide de vos propres données. Dans cet exercice, les données se composent d’une collection de brochures de voyage de la société fictive *Margies Travel*.

1. Dans un nouvel onglet de navigateur, téléchargez les données archivées d’une brochure à partir de `https://aka.ms/own-data-brochures`. Extrayez les brochures dans un dossier sur votre PC.
1. Dans le portail Azure, accédez à votre compte de stockage et consultez la page **Navigateur de stockage**.
1. Sélectionnez **Conteneurs d’objets blob**, puis ajoutez un conteneur appelé `margies-travel`.
1. Sélectionnez le conteneur **margies-travel**, puis chargez les brochures .pdf que vous avez extraites précédemment dans le dossier racine du conteneur d’objets blob.

## Déployer des modèles IA

Vous allez utiliser deux modèles IA dans cet exercice :

- Un modèle d’incorporation de texte pour *vectoriser* le texte des brochures afin qu’il puisse être indexé efficacement pour une utilisation dans les invites d’ancrage
- Un modèle GPT que votre application peut utiliser pour générer des réponses aux invites qui sont ancrées dans vos données

Pour déployer ces modèles, vous allez utiliser AI Studio.

1. Dans le portail Azure, accédez à votre ressource Azure OpenAI. Utilisez ensuite le lien pour ouvrir votre ressource dans **Azure AI Studio**.
1. Dans Azure AI Studio, sur la page **Déploiements**, affichez vos déploiements de modèles existants. Créez ensuite un nouveau déploiement de base du modèle **text-embedding-ada-002** avec les paramètres suivants :
    - **Nom du déploiement** : text-embedding-ada-002
    - **Modèle** : text-embedding-ada-002
    - **Version du modèle** : *utiliser la version par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit de jetons par minute** : 5 000\*
    - **Filtre de contenu** : valeur par défaut
    - **Activer le quota dynamique** :activé
1. Une fois le modèle d’incorporation de texte déployé, revenez à la page **Déploiements** et créez un nouveau déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Nom du déploiement** : gpt-35-turbo-16k
    - **Modèle** : gpt-35-turbo-16k *(si le modèle 16k n’est pas disponible, choisissez gpt-35-turbo)*
    - **Version du modèle** : *utiliser la version par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit de jetons par minute** : 5 000\*
    - **Filtre de contenu** : valeur par défaut
    - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Création d'un index

Pour faciliter l’utilisation de vos propres données dans une invite, vous allez les indexer à l’aide de Recherche Azure AI. Vous allez utiliser le modèle d’incorporation de texte que vous avez déployé précédemment pendant le processus d’indexation pour *vectoriser* les données de texte (suite à ce processus, chaque jeton de texte de l’index est représenté par des vecteurs numériques, ce qui les rend compatibles avec la façon dont un modèle d’IA générative représente le texte)

1. Dans le portail Azure, accédez à votre ressource Recherche Azure AI.
1. Dans la page **Vue d’ensemble**, sélectionnez **Importation et vectorisation des données**.
1. Dans la page **Configurer votre connexion de données**, sélectionnez **Stockage Blob Azure** et configurez la source de données avec les paramètres suivants :
    - **Abonnement** : abonnement Azure dans lequel vous avez approvisionné votre compte de stockage
    - **Compte de stockage d’objets blob** : sélectionnez le compte de stockage que vous avez créé précédemment
    - **Conteneur d’objets blob** : margies-travel
    - **Dossier d’objets blob** : *laisser vide*
    - **Activer le suivi des suppressions** : non sélectionné
    - **S’authentifier avec une identité managée** : non sélectionné
1. Dans la page **Vectoriser votre texte**, sélectionnez les paramètres suivants :
    - **Type** : Azure OpenAI
    - **Abonnement** : abonnement Azure dans lequel vous avez approvisionné votre service Azure OpenAI
    - **Azure OpenAI Service** : votre ressource Azure OpenAI Service
    - **Modèle de déploiement** : text-embedding-ada-002
    - **Type d’authentification** : clé API
    - **Je reconnais que la connexion à un service Azure OpenAI entraîne des coûts supplémentaires pour mon compte** : sélectionné
1. Sur la page suivante, <u>ne sélectionnez pas</u> l’option permettant de vectoriser des images ou d’extraire des données avec des compétences en IA.
1. Sur la page suivante, activez le classement sémantique et planifiez l’exécution de l’indexeur une seule fois.
1. Sur la page finale, définissez le **préfixe du nom d’objets** sur `margies-index`, puis créez l’index.

## Préparer le développement d’une application dans Visual Studio Code

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
    - Le **nom de déploiement** que vous avez spécifié pour le déploiement de votre modèle gpt-35-turbo (disponible sur la page **Deployments** dans Azure AI Studio).
    - Le point de terminaison de votre service de recherche (valeur **url** dans la page de vue d’ensemble de votre ressource de recherche dans le portail Azure).
    - Une **clé** de votre ressource de recherche (disponible sur la page **Clés** de votre ressource de recherche dans le portail Azure. Vous pouvez utiliser l’une des clés d’administration).
    - Le nom de l’index de recherche (qui doit être `margies-index`).
5. Enregistrez le fichier de configuration.

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

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer les ressources dans le **portail Azure** sur `https://portal.azure.com`. Veillez également à inclure le compte de stockage et la ressource de recherche, car ceux-ci peuvent entraîner un coût relativement élevé.
