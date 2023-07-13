---
lab:
  title: Utiliser le prompt engineering dans votre application
---

# Utiliser le prompt engineering dans votre application

Avec Azure OpenAI Service, les développeurs peuvent créer des chatbots, des modèles de langage et d’autres applications qui excellent dans la compréhension du langage humain naturel. Azure OpenAI vous permet d’accéder à des modèles IA préentraînés, ainsi qu’à une suite d’API et d’outils pour personnaliser et affiner ces modèles afin de répondre aux besoins spécifiques de votre application. Dans cet exercice, vous allez apprendre à déployer un modèle dans Azure OpenAI et à l’utiliser dans votre propre application pour résumer du texte.

Lors de l’utilisation d’Azure OpenAI Service, la façon dont les développeurs formulent leur prompt a un impact considérable sur la façon dont le modèle d’IA générative répond. Les modèles Azure OpenAI sont capables d’adapter et de mettre en forme le contenu, si vous le souhaitez, de manière claire et concise. Dans cet exercice, vous allez découvrir comment différents prompts pour un contenu similaire permettent d’adapter la réponse du modèle d’IA pour mieux répondre à vos besoins.

Imaginez que vous essayez d’envoyer des informations concernant un nouveau sauvetage d’animaux sauvages et que vous souhaitez obtenir de l’aide d’un modèle d’IA générative.

Cet exercice prend environ **25** minutes.

## Avant de commencer

Vous devez avoir un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.

- Pour obtenir un abonnement gratuit à Azure, visitez [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Pour demander l’accès à Azure OpenAI Service, visitez [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionner une ressource Azure OpenAI

Avant de pouvoir utiliser des modèles Azure OpenAI, vous devez provisionner une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : Un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.
    - **Groupe de ressources** : Créez un nouveau groupe de ressources en lui attribuant le nom de votre choix.
    - **Région** : Choisissez une région disponible.
    - **Nom** : Un nom unique de votre choix.
    - **Niveau tarifaire** : Standard S0
3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.
4. Accédez à la page **Clés et point de terminaison** et enregistrez ces derniers dans un fichier texte à utiliser ultérieurement.

## Déployer un modèle

Pour utiliser l’API Azure OpenAI, vous devez d’abord déployer un modèle à utiliser via **Azure OpenAI Studio**. Une fois déployé, nous référencerons ce modèle dans notre application.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur. Vous pouvez aussi accéder à [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) directement.
2. Dans Azure OpenAI Studio, créez un déploiement avec les paramètres suivants :
    - **Nom du modèle** : gpt-35-turbo
    - **Nom du déploiement** : text-turbo

> **Remarque** : Chaque modèle Azure OpenAI est optimisé pour un équilibre différent entre les fonctionnalités et les performances. Dans cet exercice, nous allons utiliser la série de modèles **3.5 Turbo** de la famille de modèles **GPT-3**, qui présente de grandes capacités à comprendre le langage. Cet exercice n’utilise qu’un seul modèle, mais le déploiement et l’utilisation d’autres modèles que vous déployez fonctionnent de la même manière.

## Appliquer le prompt engineering dans le terrain de jeu de conversation

Avant d’utiliser votre application, examinez comment le prompt engineering améliore la réponse du modèle dans le terrain de jeu. Dans ce premier exemple, imaginez que vous essayez d’écrire une application Python d’animaux avec des noms amusants.

1. Dans [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), accédez au terrain de jeu **de conversation** dans le volet gauche.
1. Dans la section **Configuration de l’Assistant** en haut, entrez `You are a helpful AI assistant` comme message système.
1. Dans la section **Session de conversation**, entrez le prompt suivant et appuyez sur *Entrée*.

    ```code
   1. Create a list of animals
   2. Create a list of whimsical names for those animals
   3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. Le modèle donnera probablement une réponse pour satisfaire le prompt, sous la forme d’une liste numérotée. C’est une bonne réponse, mais pas ce que nous recherchons.
1. Ensuite, mettez à jour le message système pour inclure les instructions `You are an AI assistant helping write python code. Complete the app based on provided comments`. Cliquez sur **Enregistrer les modifications**
1. Mettez en forme les instructions sous forme de commentaires Python. Envoyez le prompt suivant au modèle.

    ```code
   # 1. Create a list of animals
   # 2. Create a list of whimsical names for those animals
   # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. Le modèle devrait répondre correctement avec un code Python complet faisant ce que les commentaires demandent.
1. Nous verrons ensuite l’impact du « few-shot prompting » (c’est-à-dire avec peu d’exemples) lors d’une tentative de classification d’articles. Revenez au message système, entrez `You are a helpful AI assistant` à nouveau, puis enregistrez vos modifications. Cela crée une session de conversation.
1. Envoyez le prompt suivant au modèle.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. La réponse présentera probablement des informations sur la sécheresse en Californie. Bien que cette réponse ne soit pas mauvaise, ce n’est pas la classification que nous recherchons.
1. Dans la section **Configuration de l’Assistant** près du message système, sélectionnez le bouton **Ajouter un exemple**. Ajoutez l’exemple suivant.

    **Utilisateur :**

    ```code
   New York Baseballers Wins Big Against Chicago
   
   New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
   
   Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
   
   The Chicago Cyclones' two hits came in the 2nd and the 5th innings, but were unable to get the runner home to score.
    ```

    **Assistant :**

    ```code
   Sports
    ```

1. Ajoutez un autre exemple avec le texte suivant.

    **Utilisateur :**

    ```code
   Joyous moments at the Oscars

   The Oscars this past week where quite something!
   
   Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
   These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
   
   From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```

    **Assistant :**

    ```code
   Entertainment
    ```

1. Enregistrez ces modifications dans la configuration de l’Assistant, et envoyez le même prompt sur la sécheresse en Californie, fourni à nouveau ici par souci pratique.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. Cette fois, le modèle devrait répondre avec une classification appropriée, même sans instructions.

## Configurer une application dans Cloud Shell

Pour montrer comment intégrer un modèle Azure OpenAI, nous allons utiliser une petite application en ligne de commande qui s’exécute dans Cloud Shell sur Azure. Ouvrez un nouvel onglet de navigateur pour utiliser Cloud Shell.

1. Dans le [portail Azure](https://portal.azure.com?azure-portal=true), sélectionnez le bouton **[>_]** (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Un volet Cloud Shell va s’ouvrir dans le bas du portail.

    ![Capture d’écran du démarrage de Cloud Shell en cliquant sur l’icône à droite du haut de la zone de recherche.](../media/cloudshell-launch-portal.png#lightbox)

2. Lorsque vous ouvrez le service Cloud Shell première fois, il se peut que vous soyez invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*). Sélectionnez **Bash**. Si vous ne voyez pas cette option, ignorez l’étape.  

3. Si vous êtes invité à créer un stockage pour votre session Cloud Shell, vérifiez que votre abonnement est spécifié et sélectionnez **Créer un stockage**. Patientez ensuite environ une minute jusqu’à ce que le stockage soit créé.

4. Assurez-vous que le type d’interpréteur de commandes sélectionné en haut à gauche du volet Cloud Shell est *Bash*. Si vous utilisez *PowerShell*, basculez vers *Bash* à l’aide du menu déroulant.

5. Une fois que le terminal a démarré, entrez la commande suivante pour télécharger l’exemple d’application et l’enregistrer dans un dossier appelé `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. Les fichiers sont téléchargés dans un dossier appelé **azure-openai**. Accédez aux fichiers de labo de cet exercice à l’aide de la commande suivante.

    ```bash
   cd azure-openai/Labfiles/03-prompt-engineering
    ```

    Des applications pour C# et Python sont fournies, ainsi que des fichiers texte qui contiennent les prompts. Les deux applications présentent les mêmes fonctionnalités.

    Ouvrez l’éditeur de code intégré où vous pouvez observer les fichiers de prompts que vous allez utiliser dans `prompts`. Utilisez la commande suivante pour ouvrir les fichiers de labo dans l’éditeur de code.

    ```bash
   code .
    ```

## Configuration de votre application

Pour cet exercice, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI.

1. Dans l’éditeur de code, développez le dossier **CSharp** ou **Python** en fonction du langage que vous préférez.

2. Ouvrez le fichier de configuration pour votre langage.

    - C#: `appsettings.json`
    - Python : `.env`
    
3. Mettez à jour les valeurs de configuration pour inclure le **point de terminaison** et la **clé** de la ressource Azure OpenAI que vous avez créée, ainsi que le nom du modèle que vous avez déployé, `text-turbo`. Enregistrez le fichier .

4. Accédez au dossier de votre langage préféré et installez les packages nécessaires.

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.5
    ```

    **Python**

    ```bash
   cd Python
   pip install python-dotenv
   pip install openai
    ```

5. Accédez au dossier de votre langage préféré, sélectionnez le fichier de code et ajoutez les bibliothèques nécessaires.

    **C#**

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    ```python
   # Add OpenAI import
   import openai
    ```

5. Ouvrez le code d’application pour votre langage et ajoutez le code nécessaire à la configuration du client.

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key
    ```

6. Dans la fonction qui appelle le modèle Azure OpenAI, ajoutez le code à mettre en forme et envoyez le prompt au modèle.

    **C#**

    ```csharp
   // Create chat completion options
   var chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, systemPrompt),
          new ChatMessage(ChatRole.User, userPrompt)
       },
       Temperature = 0.7f,
       MaxTokens = 800,
   };

   // Get response from Azure OpenAI
   Response<ChatCompletions> response = await client.GetChatCompletionsAsync(
       oaiModelName,
       chatCompletionsOptions
   );

   ChatCompletions completions = response.Value;
   string completion = completions.Choices[0].Message.Content;
    ```

    **Python**

    ```python
   # Build the messages array
   messages =[
       {"role": "system", "content": system_message},
       {"role": "user", "content": user_message},
   ]

   # Call the Azure OpenAI model
   response = openai.ChatCompletion.create(
       engine=model,
       messages=messages,
       temperature=0.7,
       max_tokens=800
   )
    ```

## Exécuter votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre prompt à votre modèle et observer la réponse. Vous remarquerez que la seule différence entre les différentes options est le contenu du prompt, tous les autres paramètres (tels que le nombre de jetons et la température) restent identiques pour chaque prompt.

Chaque prompt s’affiche dans la console dès lors qu’il est envoyé pour vous permettre de voir comment des différences dans les prompts produisent des réponses différentes.

1. Dans le terminal bash Cloud Shell, accédez au dossier de votre langage préféré.
1. Exécutez l’application et développez le terminal pour occuper une grosse partie de la fenêtre de votre navigateur.

    - **C#**  : `dotnet run`
    - **Python** : `python prompt-engineering.py`

1. Choisissez l’option **1** pour le prompt le plus simple.
1. Observez l’entrée du prompt et la sortie générée. Le modèle d’IA présentera probablement une bonne introduction générale du sauvetage des animaux sauvages.
1. Ensuite, choisissez l’option **2** pour lui envoyer un prompt demandant un e-mail d’introduction avec des détails sur le sauvetage des animaux sauvages.
1. Observez l’entrée du prompt et la sortie générée. Cette fois, vous verrez probablement le format d’un e-mail avec les animaux spécifiques inclus, ainsi que l’appel aux dons.
1. Ensuite, choisissez l’option **3** pour demander un e-mail similaire au précédent, mais avec un tableau mis en forme avec d’autres animaux inclus.
1. Observez l’entrée du prompt et la sortie générée. Cette fois, vous verrez probablement un e-mail similaire avec du texte mis en forme d’une manière spécifique (dans le cas présent, un tableau vers la fin) montrant comment les modèles d’IA générative peuvent mettre en forme la sortie quand cela est demandé.
1. Enfin, choisissez l’option **4** pour demander un e-mail similaire, mais cette fois avec un ton différent dans le message système.
1. Observez l’entrée du prompt et la sortie générée. Cette fois, vous verrez probablement l’e-mail dans un format similaire, mais avec un ton beaucoup plus informel. Vous verrez probablement même des blagues !

L’augmentation de la température entraîne souvent des variations dans la réponse, même lorsque le même prompt est fourni, en raison de l’augmentation du caractère aléatoire. Vous pouvez l’exécuter plusieurs fois avec des valeurs de température ou de top_p différentes pour voir comment cela affecte la réponse au même prompt.

Si vous souhaitez voir la réponse complète d’Azure OpenAI, vous pouvez définir la variable `printFullResponse` avec la valeur `True`, puis réexécuter l’application.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com/?azure-portal=true).
