---
lab:
  title: Générer et améliorer du code avec Azure OpenAI Service
---

# Générer et améliorer du code avec Azure OpenAI Service

Les modèles Azure OpenAI Service peuvent générer du code pour vous à l’aide de prompts en langage naturel, corriger des bogues dans le code terminé et fournir des commentaires de code. Ces modèles peuvent également expliquer et simplifier le code existant pour vous aider à comprendre ce qu’il fait et à l’améliorer.

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

Pour utiliser l’API Azure OpenAI pour générer du code, vous devez d’abord déployer un modèle à utiliser via **Azure OpenAI Studio**. Une fois déployé, nous allons utiliser le modèle avec le terrain de jeu et référencer ce modèle dans notre application.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur. Vous pouvez aussi accéder à [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) directement.
2. Dans Azure OpenAI Studio, créez un déploiement avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo
    - **Version du modèle** : *Utiliser la version par défaut*
    - **Nom du déploiement** : 35turbo

> **Remarque** : Chaque modèle Azure OpenAI est optimisé pour un équilibre différent entre les fonctionnalités et les performances. Dans cet exercice, nous allons utiliser la série de modèles **3.5 Turbo** de la famille de modèles **GPT-3**, qui présente de grandes capacités à comprendre le langage et le code.

## Générer du code dans le terrain de jeu de conversation

Avant de l’utiliser dans votre application, examinez comment Azure OpenAI peut générer et expliquer du code dans le terrain de jeu de conversation.

1. Dans [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), accédez au terrain de jeu **de conversation** dans le volet gauche.
1. Dans la section **Configuration de l’Assistant** en haut, sélectionnez le modèle de message système **Par défaut**.
1. Dans la section **Session de conversation**, entrez le prompt suivant et appuyez sur *Entrée*.

    ```code
   Write a function in python that takes a character and string as input, and returns how many times that character appears in the string
    ```

1. Le modèle répondra probablement avec une fonction, en expliquant ce que la fonction fait et comment l’appeler.
1. Ensuite, envoyez le prompt `Do the same thing, but this time write it in C#`.
1. Observez la sortie. Le modèle a probablement répondu de la même façon que la première fois, mais cette fois avec du code en C#. Vous pouvez faire une autre demande pour un autre langage de votre choix ou une fonction visant à effectuer une autre tâche, comme inverser la chaîne d’entrée.
1. Ensuite, nous allons explorer l’utilisation de l’IA pour comprendre le code avec cet exemple de fonction aléatoire que vous avez vu écrite en Ruby. Envoyez le prompt suivant comme message utilisateur.

    ```code
    What does the following function do?  
    ---  
    def random_func(n)
      start = [0, 1]
      (n - 2).times do
        start << start[-1] + start[-2]
      end
      start.shuffle.each do |num|
        puts num
      end
    end
    ```

1. Observez la sortie, qui explique ce que la fonction fait en langage naturel. Essayez de demander au modèle de le réécrire dans un langage que vous connaissez.

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
   cd azure-openai/Labfiles/04-code-generation
    ```

    Des applications pour C# et Python sont fournies, ainsi qu’un exemple de code que nous allons utiliser dans ce labo.

    Ouvrez l’éditeur de code intégré où vous pouvez observer les fichiers de code que nous allons utiliser dans `sample-code`. Utilisez la commande suivante pour ouvrir les fichiers de labo dans l’éditeur de code.

    ```bash
   code .
    ```

## Configuration de votre application

Pour cet exercice, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI.

1. Dans l’éditeur de code, développez le dossier du langage de votre choix.

2. Ouvrez le fichier de configuration pour votre langage.

    - **C#**  : `appsettings.json`
    - **Python** : `.env`

3. Mettez à jour les valeurs de configuration pour inclure le **point de terminaison** et la **clé** de la ressource Azure OpenAI que vous avez créée, ainsi que le nom de votre déploiement, `35turbo`. Enregistrez le fichier .

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

5. Sélectionnez le fichier de code dans ce dossier correspondant à votre langage et ajoutez les bibliothèques nécessaires.

    **C#**

    `Program.cs`

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    `code-generation.py`

    ```python
   # Add OpenAI import
   import openai
    ```

6. Ajoutez le code nécessaire pour configurer le client.

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
   openai.api_version = "2023-05-15"
   openai.api_key = azure_oai_key
    ```

7. Dans la fonction qui appelle le modèle Azure OpenAI, ajoutez le code à mettre en forme et envoyez la demande au modèle.

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
        MaxTokens = 1000,
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
       max_tokens=1000
   )
    ```

## Exécuter votre application

Maintenant que votre application est configurée, exécutez-la pour essayer de générer du code pour chaque cas d’usage. Le cas d’usage est numéroté dans l’application et peut être exécuté dans n’importe quel ordre.

> **Remarque** : Certains utilisateurs peuvent rencontrer une limitation du débit si le modèle est trop souvent appelé. Si vous rencontrez une erreur concernant une limite de débit de jetons, attendez une minute, puis réessayez.

1. Dans l’éditeur de code, développez le dossier `sample-code` et observez rapidement la fonction et l’application correspondant à votre langage. Ces fichiers seront utilisés pour les tâches dans l’application.
1. Dans le terminal bash Cloud Shell, accédez au dossier de votre langage préféré.
1. Exécutez l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python code-generation.py`

1. Choisissez l’option **1** pour ajouter des commentaires à votre code. Notez que la réponse risque de prendre quelques secondes pour chacune de ces tâches.
1. Les résultats seront mis dans `result/app.txt`. Ouvrez ce fichier et comparez-le au fichier de fonction dans `sample-code`.
1. Ensuite, choisissez l’option **2** afin d’écrire des tests unitaires pour cette même fonction.
1. Les résultats remplacent ce qui était dans `result/app.txt` et détaillent quatre tests unitaires pour cette fonction.
1. Ensuite, choisissez l’option **3** afin de corriger les bogues dans une application pour jouer à Go Fish.
1. Les résultats remplacent ce qui était dans `result/app.txt` et devraient avoir un code très similaire avec quelques éléments corrigés.

    - **C#**  : Les corrections sont apportées aux lignes 30 et 59
    - **Python** : Les corrections sont apportées aux lignes 18 et 31

L’application pour Go Fish dans `sample-code` peut être exécutée si vous remplacez les lignes contenant les bogues par la réponse d’Azure OpenAI. Si vous l’exécutez sans les corrections, elle ne fonctionnera pas bien.

Il est important de noter que même si le code de cette application Go Fish a été corrigé sur une certaine syntaxe, il ne s’agit pas d’une représentation strictement exacte du jeu. Si vous regardez de près, il y a des problèmes comme l’absence de vérification si le paquet est vide lors du tirage des cartes, l’absence de suppression des paires de la main des joueurs lorsqu’ils ont une paire, et quelques autres bogues qui demandent de comprendre les jeux de cartes pour les détecter. Il s’agit d’un très bon exemple montrant comment les modèles d’IA générative peuvent être utiles dans la génération de code, mais ils ne peuvent pas être considérés d’emblée comme corrects et doivent être vérifiés par le développeur.

Si vous souhaitez voir la réponse complète d’Azure OpenAI, vous pouvez définir la variable `printFullResponse` avec la valeur `True`, puis réexécuter l’application.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com/?azure-portal=true).
