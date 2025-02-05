---
lab:
  title: "Développement d’applications avec Azure\_OpenAI\_Service"
---

# Développement d’applications avec Azure OpenAI Service

Avec Azure OpenAI Service, les développeurs peuvent créer des chatbots et d’autres applications qui excellent dans la compréhension du langage humain naturel à travers l’utilisation d’API REST ou de kits SDK spécifiques au langage. Lors de l’utilisation de ces modèles de langage, la façon dont les développeurs formulent leur invite a un impact considérable sur la façon dont le modèle d’IA générative répond. Les modèles Azure OpenAI sont capables d’adapter et de mettre en forme le contenu, si vous le souhaitez, de manière claire et concise. Dans cet exercice, vous allez découvrir comment connecter votre application à Azure OpenAI et comment différents prompts pour un contenu similaire permettent d’adapter la réponse du modèle d’IA pour mieux répondre à vos besoins.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels travaillant sur une campagne marketing sur la vie sauvage. Vous explorez comment utiliser l’IA générative pour améliorer les e-mails publicitaires et catégoriser les articles qui peuvent s’appliquer à votre équipe. Les techniques d’ingénierie rapide utilisées dans l’exercice peuvent être appliquées de la même façon pour divers cas d’usage.

Cet exercice prend environ **30** minutes.

## Cloner le référentiel pour cette formation

Si vous ne l’avez pas déjà fait, vous devez cloner le référentiel de code pour ce cours :

1. Démarrez Visual Studio Code.
2. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-openai` vers un dossier local (peu importe quel dossier).
3. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.
4. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

## Provisionner une ressource Azure OpenAI

Si vous n’en avez pas déjà une, approvisionnez une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au **portail Azure** à l’adresse `https://portal.azure.com`.

1. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : *Sélectionner un abonnement Azure approuvé pour l’accès à Azure OpenAI Service*
    - **Groupe de ressources** : *sélectionnez ou créez un groupe de ressources*.
    - **Région** : *Choisir de manière **aléatoire** une région parmi les suivantes*\*
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

Ensuite, vous allez déployer une ressource de modèle Azure OpenAI à partir de l’interface CLI. Reportez-vous à cet exemple et remplacez les variables suivantes par vos propres valeurs ci-dessus :

```dotnetcli
az cognitiveservices account deployment create \
   -g *Your resource group* \
   -n *Name of your OpenAI service* \
   --deployment-name gpt-35-turbo \
   --model-name gpt-35-turbo \
   --model-version 0125  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 5,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.

> [!NOTE]
> Si vous voyez des avertissements indiquant que le framework net7.0 n’est pas pris en charge, vous pouvez les ignorer pour cet exercice.

## Configuration de votre application

Les applications pour C# et Python ont été fournies, et les deux applications présentent les mêmes fonctionnalités. Tout d’abord, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI avec des appels d’API asynchrones.

1. Dans Visual Studio Code, dans le volet **Explorateur**, accédez au dossier **Labfiles/01-app-develop**, puis développez le dossier **CSharp** ou **Python**, selon le langage que vous préférez. Chaque dossier contient les fichiers propres au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure OpenAI.
2. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code, puis ouvrez un terminal intégré. Installez ensuite le package du SDK Azure OpenAI en exécutant la commande appropriée pour le langage de votre choix :

    **C# :**

    ```
    dotnet add package Azure.AI.OpenAI --version 2.0.0
    ```

    **Python** :

    ```
    pip install openai==1.54.3
    ```

3. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de configuration pour le langage de votre choix

    - **C#** : appsettings.json
    - **Python** : .env
    
4. Mettez à jour les valeurs de configuration pour inclure :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le **nom du déploiement** que vous avez spécifié pour votre déploiement de modèle.
5. Enregistrez le fichier de configuration.

## Ajouter du code pour utiliser Azure OpenAI Service

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) Azure OpenAI pour utiliser votre modèle déployé.

1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code de votre langue préférée et remplacez le commentaire ***Ajouter un package Azure OpenAI*** par du code pour ajouter la bibliothèque Azure OpenAI SDK :

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python** : application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. Dans le fichier de code, recherchez le commentaire ***Configurer le client Azure OpenAI*** et ajoutez du code pour configurer le client Azure OpenAI :

    **C#** : Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    ```

    **Python** : application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. Dans la fonction qui appelle le modèle Azure OpenAI, sous le commentaire ***Obtenir la réponse d’Azure OpenAI***, ajoutez le code à mettre en forme et envoyez la requête au modèle.

    **C#** : Program.cs

    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(userMessage)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python** : application.py

    ```python
    # Get response from Azure OpenAI
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. Enregistrez les modifications apportées au fichier de code.

## Exécuter votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre prompt à votre modèle et observer la réponse. Vous remarquerez que la seule différence entre les différentes options est le contenu du prompt, tous les autres paramètres (tels que le nombre de jetons et la température) restent identiques pour chaque prompt.

1. Dans le dossier de votre langue préférée, ouvrez `system.txt` dans Visual Studio Code. Pour chacune des interactions, vous saisirez le **message système** dans ce fichier et l’enregistrerez. Chaque itération s’interrompt d’abord pour vous permettre de modifier le message système.
1. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python application.py`

    > **Conseil** : vous pouvez utiliser l’icône **Agrandir la taille du volet** (**^**) dans la barre d’outils du terminal pour afficher plus de texte sur la console.

1. Pour la première itération, entrez les invites suivantes :

    **Message système**

    ```prompt
    You are an AI assistant
    ```

    **Message utilisateur :**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. Observez la sortie. Le modèle d’IA présentera probablement une bonne introduction générale du sauvetage des animaux sauvages.
1. Ensuite, saisissez les invites suivantes qui spécifient un format pour la réponse :

    **Message système**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Message utilisateur :**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

    > **Conseil** : la saisie automatique dans la machine virtuelle ne fonctionne pas correctement avec les invites multilignes. Si vous rencontrez ce problème, copiez l’invite entière, puis collez-la dans Visual Studio Code.

1. Observez la sortie. Cette fois, vous verrez probablement le format d’un e-mail avec les animaux spécifiques inclus, ainsi que l’appel aux dons.
1. Ensuite, entrez les invites suivantes qui spécifient également le contenu :

    **Message système**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Message utilisateur :**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observez la sortie et découvrez comment l’e-mail a changé en fonction de vos instructions claires.
1. Ensuite, entrez les invites suivantes dans lesquelles nous ajoutons des détails sur le ton au message système :

    **Message système**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **Message utilisateur :**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observez la sortie. Cette fois, vous verrez probablement l’e-mail dans un format similaire, mais avec un ton beaucoup plus informel. Vous verrez probablement même des blagues !
1. Pour la dernière itération, nous nous écartons de la génération d'e-mails et explorons le *contexte de base*. Ici, vous fournissez un message système simple et modifiez l’application pour fournir le contexte de base au début de l’invite de l’utilisateur. L’application ajoute ensuite l’entrée de l’utilisateur et extrait des informations du contexte de base pour répondre à notre invite utilisateur.
1. Ouvrez le fichier `grounding.txt` et lisez brièvement le contexte de base que vous allez insérer.
1. Dans votre application, immédiatement après le commentaire ***Formater et envoyer la demande au modèle*** et avant tout code existant, ajoutez l'extrait de code suivant pour lire le texte du `grounding.txt` afin d'augmenter l'invite utilisateur avec le contexte de base.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python** : application.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. Enregistrez le fichier et réexécutez votre application.
1. Entrez les invites suivantes (le **message système** étant toujours saisi et enregistré dans `system.txt`).

    **Message système**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **Message utilisateur :**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **Conseil** : Si vous souhaitez voir la réponse complète d’Azure OpenAI, vous pouvez définir la variable **printFullResponse** sur `True` et réexécuter l’application.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou la ressource entière dans le **Portail Azure** à `https://portal.azure.com`.
