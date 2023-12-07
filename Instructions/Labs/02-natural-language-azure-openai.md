---
lab:
  title: Intégrer Azure OpenAI à votre application
---

# Intégrer Azure OpenAI à votre application

Avec Azure OpenAI Service, les développeurs peuvent créer des chatbots, des modèles de langage et d’autres applications qui excellent dans la compréhension du langage humain naturel. Azure OpenAI vous permet d’accéder à des modèles IA préentraînés, ainsi qu’à une suite d’API et d’outils pour personnaliser et affiner ces modèles afin de répondre aux besoins spécifiques de votre application. Dans cet exercice, vous allez apprendre à déployer un modèle dans Azure OpenAI et à l’utiliser dans votre propre application pour résumer du texte.

Cet exercice prend environ **30** minutes.

## Avant de commencer

Vous devez avoir un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.

- Pour obtenir un abonnement gratuit à Azure, visitez [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Pour demander l’accès à Azure OpenAI Service, visitez [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionner une ressource Azure OpenAI

Avant de pouvoir utiliser des modèles Azure OpenAI, vous devez provisionner une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : Un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.
    - **Groupe de ressources** : choisissez un groupe de ressources existant ou créez un groupe de ressources en lui attribuant le nom de votre choix.
    - **Région** : Choisissez une région disponible.
    - **Nom** : Un nom unique de votre choix.
    - **Niveau tarifaire** : Standard S0
3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.
4. Accédez à la page **Clés et point de terminaison** et enregistrez ces derniers dans un fichier texte à utiliser ultérieurement.

## Déployer un modèle

Pour utiliser l’API Azure OpenAI, vous devez d’abord déployer un modèle à utiliser via **Azure OpenAI Studio**. Une fois déployé, nous référencerons ce modèle dans notre application.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur.
2. Dans Azure OpenAI Studio, dans la page **Déploiements**, affichez vos déploiements de modèles existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo-16k
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** : activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

> **Remarque** : dans certaines régions, la nouvelle interface de déploiement de modèle n’affiche pas l’option **Version du modèle**. Dans ce cas, ne vous inquiétez pas et continuez sans définir l’option.

## Configurer une application dans Cloud Shell

Pour montrer comment intégrer un modèle Azure OpenAI, nous allons utiliser une petite application en ligne de commande qui s’exécute dans Cloud Shell sur Azure. Ouvrez un nouvel onglet de navigateur pour utiliser Cloud Shell.

1. Dans le [portail Azure](https://portal.azure.com?azure-portal=true), sélectionnez le bouton **[>_]** (*Cloud Shell*) en haut de la page, à droite de la zone de recherche. Un volet Cloud Shell va s’ouvrir dans le bas du portail.

    ![Capture d’écran du démarrage de Cloud Shell en cliquant sur l’icône à droite du haut de la zone de recherche.](../media/cloudshell-launch-portal.png#lightbox)

2. Lorsque vous ouvrez le service Cloud Shell première fois, il se peut que vous soyez invité à choisir le type d’interpréteur de commandes que vous souhaitez utiliser (*Bash* ou *PowerShell*). Sélectionnez **Bash**. Si vous ne voyez pas cette option, ignorez l’étape.  

3. Si vous êtes invité à créer un stockage pour votre Cloud Shell, sélectionnez **Afficher les paramètres avancés** et sélectionnez les paramètres suivants :
    - **Abonnement** : Votre abonnement
    - **Régions Cloud Shell** : choisissez une région disponible
    - **Afficher les paramètres d’isolation de réseau virtuel** Non sélectionné
    - **Groupe de ressources** : utilisez le groupe de ressources existant dans lequel vous avez approvisionné votre ressource Azure OpenAI
    - **Compte de stockage** : créez un compte de stockage avec un nom unique
    - **Partage de fichiers** : créez un partage de fichiers avec un nom unique

    Patientez ensuite environ une minute jusqu’à ce que le stockage soit créé.

    > **Remarque** : Si vous disposez déjà d’un cloud shell configuré dans votre abonnement Azure, vous devrez peut-être utiliser l’option **Réinitialiser les paramètres utilisateur** dans le menu ⚙️ pour vous assurer que les dernières versions de Python et du .NET Framework sont installées.

4. Vérifiez que le type de shell indiqué en haut à gauche du volet Cloud Shell est *Bash*. Si vous utilisez *PowerShell*, basculez vers *Bash* à l’aide du menu déroulant.

5. Une fois que le terminal a démarré, entrez la commande suivante pour télécharger l’exemple d’application et l’enregistrer dans un dossier appelé `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```
  
6. Les fichiers sont téléchargés dans un dossier appelé **azure-openai**. Accédez aux fichiers de labo de cet exercice à l’aide de la commande suivante.

    ```bash
   cd azure-openai/Labfiles/02-nlp-azure-openai
    ```

7. Ouvrez l’éditeur de code intégré en exécutant la commande suivante :

    ```bash
    code .
    ```

8. Dans l’éditeur de code, développez le dossier **text-files** et sélectionnez **sample-text.txt** pour afficher le texte que vous utiliserez pour résumer votre modèle.

    > **Conseil** : consultez la [documentation de l’éditeur de code Azure Cloud Shell](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) pour plus d’informations sur l’utilisation des fichiers dans l’environnement Azure Cloud Shell.

## Configuration de votre application

Pour cet exercice, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI. Des applications pour C# et Python sont fournies. Les deux applications présentent les mêmes fonctionnalités.

1. Dans l’éditeur de code, développez le dossier **CSharp** ou **Python** en fonction du langage que vous préférez.

2. Ouvrir le fichier de configuration pour votre langage

    - C#: `appsettings.json`
    - Python : `.env`
    
3. Mettez à jour les valeurs de configuration pour inclure le **point de terminaison** et la **clé** de la ressource Azure OpenAI que vous avez créée, ainsi que le nom du modèle que vous avez déployé. Enregistrez le fichier.

4. Dans le volet de la console, saisissez les commandes suivantes pour accéder au dossier de votre langage préféré et installer les packages nécessaires.

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.9
    ```

    **Python**

    ```bash
    cd Python
    pip install python-dotenv
    pip install openai==1.2.0
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
    from openai import AzureOpenAI
    ```

6. Ouvrez le code d’application pour votre langage et ajoutez le code nécessaire pour générer la demande, qui spécifie les différents paramètres de votre modèle, tels que `prompt` et `temperature`.

    **C#**

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatMessage(ChatRole.System, "You are a helpful assistant."),
            new ChatMessage(ChatRole.User, "Summarize the following text in 20 words or less:\n" + text),
        },
        MaxTokens = 120,
        Temperature = 0.7f,
        DeploymentName = oaiModelName
    };
    
    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);
    string completion = response.Choices[0].Message.Content;
    
    Console.WriteLine("Summary: " + completion + "\n");
    ```

    **Python**

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2023-05-15"
            )
    
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_model,
        temperature=0.7,
        max_tokens=120,
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": "Summarize the following text in 20 words or less:\n" + text}
        ]
    )
    
    print("Summary: " + response.choices[0].message.content + "\n")
    ```

## Exécuter votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre demande à votre modèle et observer la réponse.

1. Dans le terminal bash Cloud Shell, accédez au dossier de votre langage préféré.
1. Exécutez l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python test-openai-model.py`

1. Observez le résumé de l’exemple de fichier texte.
1. Accédez à votre fichier de code pour votre langage préféré, puis remplacez la valeur *température* par `1`. Enregistrez le fichier .
1. Réexécutez l’application et observez la sortie.

L’augmentation de la température entraîne souvent des variations du résumé, même lorsque le même texte est fourni, en raison de l’augmentation du caractère aléatoire. Vous pouvez l’exécuter plusieurs fois pour voir comment la sortie peut changer. Essayez d’utiliser des valeurs différentes pour votre température avec la même entrée.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com?azure-portal=true).
