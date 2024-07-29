---
lab:
  title: Utiliser le prompt engineering dans votre application
---

# Utiliser le prompt engineering dans votre application

Lors de l’utilisation d’Azure OpenAI Service, la façon dont les développeurs formulent leur prompt a un impact considérable sur la façon dont le modèle d’IA générative répond. Les modèles Azure OpenAI sont capables d’adapter et de mettre en forme le contenu, si vous le souhaitez, de manière claire et concise. Dans cet exercice, vous allez découvrir comment différents prompts pour un contenu similaire permettent d’adapter la réponse du modèle d’IA pour mieux répondre à vos besoins.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels travaillant sur une campagne marketing sur la faune. Vous explorez comment utiliser l’IA générative pour améliorer les e-mails publicitaires et catégoriser les articles qui peuvent s’appliquer à votre équipe. Les techniques d’ingénierie rapide utilisées dans l’exercice peuvent être appliquées de la même façon pour divers cas d’usage.

Cet exercice prend environ **30** minutes.

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
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Modèle** : gpt-35-turbo-16k *(si le modèle 16k n’est pas disponible, choisissez gpt-35-turbo)*
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Type de déploiement** : Standard
    - **Limite de débit de jetons par minute** : 5 000\*
    - **Filtre de contenu** : valeur par défaut
    - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Explorer les techniques d’ingénierie d’invite

Commençons par explorer quelques techniques d’ingénierie rapide dans Chat Playground.

1. Dans **Azure OpenAI Studio** à `https://oai.azure.com`, dans la section **Playground**, sélectionnez la page **Conversation** . La page du terrain de jeu **Conversation** se compose de trois sections principales :
    - **Définition** : utilisée pour définir le contexte pour les réponses du modèle.
    - **Session de conversation** : utilisée pour envoyer des messages de conversation et voir les réponses.
    - **Configuration** : utilisée pour configurer les paramètres du modèle de déploiement.
2. Dans la section **Configuration**, assurez-vous que votre modèle de déploiement est sélectionné.
3. Dans la section **Définition**, sélectionnez le modèle de message système par défaut pour définir le contexte de la session de conversation. Le message système par défaut est *Vous êtes un assistant IA qui aide les personnes à trouver des informations*.
4. Dans la **session Chat**, soumettez la requête suivante :

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    La réponse fournit une description de l’article. Toutefois, supposons que vous souhaitez un format plus spécifique pour la catégorisation d’article.

5. Dans la section **Configuration**, remplacez le message système par `You are a news aggregator that categorizes news articles.`

6. Sous le nouveau message système, dans la section **Exemples**, sélectionnez le bouton **Ajouter**. Ajoutez ensuite l’exemple suivant.

    **Utilisateur** :
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **Assistant :**
    
    ```prompt
    Sports
      ```

7. Ajoutez un autre exemple avec le texte suivant.

    **Utilisateur :**
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **Assistant :**
    
    ```prompt
    Entertainment
    ```

8. Utilisez le bouton **Appliquer les modifications** en haut de la section **Configuration** pour mettre à jour le message système.

9. Dans la section **Session de conversation**, renvoyez l'invite suivante :

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    La combinaison d’un message système plus spécifique et de quelques exemples de requêtes et de réponses attendues entraîne un format cohérent pour les résultats.

10. Dans la section **Configuration** , remplacez le message système par le modèle par défaut, qui doit être `You are an AI assistant that helps people find information.` sans exemples. Ensuite, appliquez les modifications.

11. Dans la section **session de Chat**, soumettez l'invite suivante :

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    Le modèle donnera probablement une réponse pour satisfaire le prompt, sous la forme d’une liste numérotée. Il s’agit d’une réponse appropriée, mais supposons que vous vouliez réellement que le modèle écrive un programme Python qui effectue les tâches que vous avez décrites ?

12. Modifiez le message système en `You are a coding assistant helping write python code.` et appliquez les modifications.
13. Renvoyez l’invite suivante au modèle :

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    Le modèle doit répondre correctement avec du code Python en faisant ce que les commentaires demandent.

## Préparer le développement d’une application dans Visual Studio Code

Examinons maintenant l’utilisation de l’ingénierie d’invite dans une application qui utilise le Kit de développement logiciel (SDK) du service Azure OpenAI. Vous allez développer votre application à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : Si vous avez déjà cloné le dépôt **mslearn-openai**, ouvrez-le dans Visual Studio Code. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
2. Ouvrez la palette (Maj+CTRL+P) et exécutez une commande **Git : Cloner** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-openai` vers un dossier local (peu importe quel dossier).
3. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.

    > **Remarque** : Si Visual Studio Code affiche un message contextuel qui vous invite à approuver le code que vous ouvrez, cliquez sur l’option **Oui, je fais confiance aux auteurs** dans la fenêtre contextuelle.

4. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

## Configuration de votre application

Les applications pour C# et Python ont été fournies, et les deux applications présentent les mêmes fonctionnalités. Tout d’abord, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI avec des appels d’API asynchrones.

1. Dans Visual Studio Code, dans le volet de l’**Explorateur**, accédez au dossier **Labfiles/03-prompt-engineering** et développez le dossier **CSharp** ou dossier **Python** en fonction de votre préférence de langue. Chaque dossier contient les fichiers propres au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure OpenAI.
2. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code, puis ouvrez un terminal intégré. Installez ensuite le package du SDK Azure OpenAI en exécutant la commande appropriée pour le langage de votre choix :

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
    - Le **nom de déploiement** que vous avez spécifié pour votre modèle de déploiement (disponible dans la page **Déploiements** d’Azure OpenAI Studio).
5. Enregistrez le fichier de configuration.

## Ajouter du code pour utiliser Azure OpenAI Service

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) Azure OpenAI pour utiliser votre modèle déployé.

1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code de votre langue préférée et remplacez le commentaire ***Ajouter un package Azure OpenAI*** par du code pour ajouter la bibliothèque Azure OpenAI SDK :

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python** : prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. Dans le fichier de code, recherchez le commentaire ***Configurer le client Azure OpenAI*** et ajoutez du code pour configurer le client Azure OpenAI :

    **C#** : Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python** : prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. Dans la fonction qui appelle le modèle Azure OpenAI, sous le commentaire ***Formater et envoyer la demande au modèle***, ajoutez le code au format et envoyez la requête au modèle.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python** : prompt-engineering.py

    ```python
    # Format and send the request to the model
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

1. Dans le dossier de votre langue préférée, ouvrez `system.txt` dans Visual Studio Code. Pour chacune des interactions, vous saisirez le **message système** dans ce fichier et l'enregistrerez. Chaque itération s’interrompt d’abord pour vous permettre de modifier le message système.
1. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python prompt-engineering.py`

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

    **Python** : prompt-engineering.py

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
