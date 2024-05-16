---
lab:
  title: Utiliser des kits SDK Azure OpenAI dans votre application
---

# Utiliser des API Azure OpenAI dans votre application

Avec Azure OpenAI Service, les développeurs peuvent créer des chatbots, des modèles de langage et d’autres applications qui excellent dans la compréhension du langage humain naturel. Azure OpenAI vous permet d’accéder à des modèles IA préentraînés, ainsi qu’à une suite d’API et d’outils pour personnaliser et affiner ces modèles afin de répondre aux besoins spécifiques de votre application. Dans cet exercice, vous allez apprendre à déployer un modèle dans Azure OpenAI et à l’utiliser dans votre propre application.

Dans le scénario de cet exercice, vous jouerez le rôle d'un développeur de logiciels chargé de mettre en œuvre une application capable d'utiliser l'IA générative pour fournir des suggestions de randonnée. Les techniques utilisées dans l’exercice peuvent être appliquées à n’importe quelle application qui souhaite utiliser les API Azure OpenAI.

Cet exercice prend environ **30** minutes.

## Provisionner une ressource Azure OpenAI

Si vous n’en avez pas encore, provisionnez une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au **portail Azure** à l’adresse `https://portal.azure.com`.
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : *Sélectionner un abonnement Azure approuvé pour l’accès au service Azure OpenAI*
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
    - **Modèle** : gpt-35-turbo-16k *(si le modèle 16k n'est pas disponible, choisissez gpt-35-turbo)*
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *Un nom unique de votre choix. Vous utiliserez ce nom plus loin dans le labo.*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Type de déploiement** : Standard
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Préparer le développement d’une application dans Visual Studio Code

Vous allez développer votre application Azure OpenAI à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : Si vous avez déjà cloné le dépôt **mslearn-openai**, ouvrez-le dans le code Visual Studio. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
2. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-openai` vers un dossier local (peu importe quel dossier).
3. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.

    > **Remarque** : Si Visual Studio Code affiche un message contextuel pour vous inviter à approuver le code que vous ouvrez, cliquez sur **Oui, je fais confiance aux auteurs** option dans la fenêtre contextuelle.

4. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

## Configuration de votre application

Des applications pour C# et Python sont fournies. Les deux applications présentent les mêmes fonctionnalités. Tout d’abord, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI.

1. Dans Visual Studio Code, dans le volet **Explorateur**, accédez au dossier **Labfiles/02-azure-openai-api** et développez le dossier **CSharp** ou **Python** en fonction de votre préférence de langue. Chaque dossier contient les fichiers spécifiques au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure OpenAI.
2. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code et ouvrez un terminal intégré. Installez ensuite le package du Kit de développement logiciel (SDK) Azure OpenAI en exécutant la commande appropriée pour votre préférence de langue :

    **C# :**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python** :

    ```
    pip install openai==1.13.3
    ```

3. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de configuration de votre langue préférée

    - **C#** : appsettings.json
    - **Python** : .env
    
4. Mettez à jour les valeurs de configuration pour inclure :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le **nom de déploiement** que vous avez spécifié pour votre modèle de déploiement (disponible sur la page **Déploiements** dans Azure OpenAI Studio).
5. Enregistrez le fichier de configuration.

## Ajouter du code pour utiliser le service Azure OpenAI

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) Azure OpenAI pour utiliser votre modèle déployé.

1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code de votre langue préférée et remplacez le commentaire ***Ajouter un package Azure OpenAI*** par du code pour ajouter la bibliothèque Azure OpenAI SDK :

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```
    
    **Python** : test-openai-model.py
    
    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. Dans le code d’application de votre langue, remplacez le commentaire ***Initialiser le client Azure OpenAI...*** par le code suivant pour initialiser le client et définir notre message système.

    **C#** : Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python** : test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. Remplacez le commentaire ***Ajouter du code pour envoyer une requête...*** par le code nécessaire pour générer la requête ; spécification des différents paramètres de votre modèle, tels que `messages` et `temperature`.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(inputText),
        },
        MaxTokens = 400,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Print the response
    string completion = response.Choices[0].Message.Content;
    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python** : test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. Enregistrez les modifications apportées à votre fichier de code.

## Tester votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre demande à votre modèle et observer la réponse.

1. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python test-openai-model.py`

    > **Conseil** : vous pouvez utiliser l’icône **Agrandir la taille du volet** (**^**) dans la barre d’outils du terminal pour afficher plus de texte sur la console.

1. Lorsque vous y êtes invité, entrez le texte `What hike should I do near Rainier?`.
1. Observez la sortie, notez que la réponse suit les instructions fournies dans le message système que vous avez ajouté aux *messages* tableau.
1. Fournissez l’invite `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` et observez la sortie.
1. Dans le fichier de code de votre langue préférée, modifiez la valeur du paramètre de *température* dans votre demande à **1.0** et enregistrez le fichier.
1. Réexécutez l’application à l’aide des invites ci-dessus et observez la sortie.

L'augmentation de la température entraîne souvent une variation de la réponse, même lorsque le même texte est fourni, en raison du caractère aléatoire accru. Vous pouvez l’exécuter plusieurs fois pour voir comment la sortie peut changer. Essayez d’utiliser des valeurs différentes pour votre température avec la même entrée.

## Gérer l’historique des conversations

Dans la plupart des applications réelles, la possibilité de référencer les parties précédentes de la conversation permet une interaction plus réaliste avec un agent IA. L’API Azure OpenAI est sans état par conception, mais en fournissant un historique de la conversation dans votre invite, vous activez le modèle IA pour référencer les messages passés.

1. Réexécutez l’application et fournissez l’invite `Where is a good hike near Boise?`.
1. Observez la sortie, puis invitez `How difficult is the second hike you suggested?`.
1. La réponse du modèle indiquera probablement qu'il ne comprend pas la hausse à laquelle vous faites référence. Pour résoudre ce problème, nous pouvons permettre au modèle d’avoir les messages de conversation passés pour référence.
1. Dans votre application, nous devons ajouter l’invite précédente et la réponse à l’invite future que nous envoyons. Sous la définition du **message système**, ajoutez le code suivant.

    **C#** : Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatRequestMessage>()
    {
        new ChatRequestSystemMessage(systemMessage),
    };
    ```

    **Python** : test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. Sous le commentaire ***Ajouter du code pour envoyer une requête...***, remplacez tout le code du commentaire à la fin du **tandis que** boucle avec le code suivant, puis enregistrez le fichier. Le code est principalement le même, mais maintenant en utilisant le tableau de messages pour stocker l’historique des conversations.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new ChatRequestUserMessage(inputText));

    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        MaxTokens = 1200,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Add messages to the completion options
    foreach (ChatRequestMessage chatMessage in messagesList)
    {
        chatCompletionsOptions.Messages.Add(chatMessage);
    }

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Return the response
    string completion = response.Choices[0].Message.Content;

    // Add generated text to messages list
    messagesList.Add(new ChatRequestAssistantMessage(completion));

    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python** : test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. Enregistrez le fichier. Dans le code que vous avez ajouté, notez que nous ajoutons maintenant l'entrée et la réponse précédentes au tableau d'invites, ce qui permet au modèle de comprendre l'historique de notre conversation.
1. Dans le volet terminal, entrez la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python test-openai-model.py`

1. Réexécutez l’application et fournissez l’invite `Where is a good hike near Boise?`.
1. Observez la sortie, puis invitez `How difficult is the second hike you suggested?`.
1. Vous obtiendrez probablement une réponse sur la deuxième randonnée suggérée par le modèle, qui fournit une conversation beaucoup plus réaliste. Vous pouvez poser des questions de suivi supplémentaires référençant les réponses précédentes, et chaque fois que l’historique fournit un contexte pour que le modèle réponde.

    > **Conseil** : Le nombre de jetons est défini uniquement sur 1200. Par conséquent, si la conversation continue trop longtemps, l’application manquera de jetons disponibles, ce qui entraîne une invite incomplète. En production, la limitation de la longueur de l’historique aux entrées et réponses les plus récentes permet de contrôler le nombre de jetons requis.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou la ressource entière dans le **Portail Microsoft Azure** à `https://portal.azure.com`.
