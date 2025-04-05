---
lab:
  title: Générer et améliorer du code avec Azure OpenAI Service
  status: stale
---

# Générer et améliorer du code avec Azure OpenAI Service

Les modèles Azure OpenAI Service peuvent générer du code pour vous à l’aide de prompts en langage naturel, corriger des bogues dans le code terminé et fournir des commentaires de code. Ces modèles peuvent également expliquer et simplifier le code existant pour vous aider à comprendre ce qu’il fait et à l’améliorer.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels qui étudie comment utiliser l’IA générative pour rendre les tâches de codage plus faciles et plus efficaces. Les techniques utilisées dans l’exercice peuvent être appliquées à d’autres fichiers de code, d’autres langages de programmation et d’autres cas d’usage.

Cet exercice prend environ **25** minutes.

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

    > \* Les ressources Azure OpenAI sont limitées par des quotas régionaux. Les régions répertoriées incluent le quota par défaut pour les types de modèle utilisés dans cet exercice. Le choix aléatoire d’une région réduit le risque d’atteindre sa limite de quota dans les scénarios où vous partagez un abonnement avec d’autres utilisateurs. Dans le cas où une limite de quota serait atteinte plus tard dans l’exercice, il peut être nécessaire de créer une autre ressource dans une région différente.

3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.

## Déployer un modèle

Azure fournit un portail web appelé **Azure AI Foundry**, que vous pouvez utiliser pour déployer, gérer et explorer des modèles. Vous allez commencer votre exploration d’Azure OpenAI en utilisant le portail Azure AI Foundry pour déployer un modèle.

> **Remarque** : lorsque vous utilisez le portail Azure AI Foundry, des boîtes de message qui suggèrent des tâches à effectuer peuvent être affichées. Vous pouvez les fermer et suivre les étapes de cet exercice.

1. Dans le portail Azure, sur la page **Vue d’ensemble** de votre ressource Azure OpenAI, faites défiler jusqu’à la section **Démarrer** et sélectionnez le bouton permettant d’accéder au **portail AI Foundry** (anciennement AI Studio).
1. Dans le portail Azur AI Foundry, dans le panneau de gauche, sélectionnez la page **Déploiements** et affichez vos modèles de déploiement existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-4o** avec les paramètres suivants :
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Modèle** : gpt-4o
    - **Model version** : *utiliser la version par défaut*
    - **Type de déploiement** : Standard
    - **Limite de débit de jetons par minute** : 5 000\*
    - **Filtre de contenu** : valeur par défaut
    - **Enable dynamic quota** : désactivé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Générer du code dans le terrain de jeu de conversation

Avant de l’utiliser dans votre application, examinez comment Azure OpenAI peut générer et expliquer du code dans le terrain de jeu de conversation.

1. Dans la section **Terrain de jeu**, sélectionnez la page **Conversation**. La page du terrain de jeu **Chat** se compose d’une rangée de boutons et de deux panneaux principaux (qui peuvent être disposés horizontalement de droite à gauche ou verticalement de haut en bas en fonction de la résolution de l’écran) :
    - **Configuration** : utilisée pour sélectionner votre déploiement, définir le message système et définir des paramètres pour interagir avec votre déploiement.
    - **Session de conversation** : utilisée pour envoyer des messages de conversation et voir les réponses.
1. Sous **Déploiements**, vérifiez que votre déploiement de modèle est sélectionné.
1. Dans la zone **Message système**, définissez le message système sur `You are a programming assistant helping write code` et appliquez les modifications.
1. Dans la **session de conversation**, soumettez la requête suivante :

    ```prompt
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    Le modèle répondra probablement avec une fonction, en expliquant ce que la fonction fait et comment l’appeler.

1. Ensuite, envoyez le prompt `Do the same thing, but this time write it in C#`.

    Le modèle a probablement répondu de la même façon que la première fois, mais cette fois avec du code en C#. Vous pouvez faire une autre demande pour un autre langage de votre choix ou une fonction visant à effectuer une autre tâche, comme inverser la chaîne d’entrée.

1. Nous allons ensuite explorer l’utilisation de l’IA pour comprendre le code. Soumettez le prompt suivant comme message utilisateur.

    ```prompt
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    Le modèle doit décrire ce que fait la fonction, qui est de multiplier deux nombres en utilisant une boucle.

1. Soumettez le prompt `Can you simplify the function?`.

    Le modèle doit écrire une version plus simple de la fonction.

1. Soumettez le prompt : `Add some comments to the function.`

    Le modèle ajoute des commentaires au code.

## Préparer le développement d’une application dans Visual Studio Code

Examinons maintenant comment créer une application personnalisée qui utilise le service Azure OpenAI pour générer du code. Vous allez développer votre application à l’aide de Visual Studio Code. Les fichiers de code de votre application ont été fournis dans un référentiel GitHub.

> **Conseil** : Si vous avez déjà cloné le dépôt **mslearn-openai**, ouvrez-le dans Visual Studio Code. Dans le cas contraire, procédez comme suit pour le cloner dans votre environnement de développement.

1. Démarrez Visual Studio Code.
2. Ouvrez la palette de commandes (Maj+CTRL+P) ou **Affichage** > **Palette de commandes...** et exécutez une commande **Git: Clone** pour cloner le référentiel `https://github.com/MicrosoftLearning/mslearn-openai` vers un dossier local (peu importe quel dossier).
3. Lorsque le référentiel a été cloné, ouvrez le dossier dans Visual Studio Code.

    > **Remarque** : Si Visual Studio Code affiche un message contextuel qui vous invite à approuver le code que vous ouvrez, cliquez sur l’option **Oui, je fais confiance aux auteurs** dans la fenêtre contextuelle.

4. Attendez que des fichiers supplémentaires soient installés pour prendre en charge les projets de code C# dans le référentiel.

    > **Remarque** : si vous êtes invité à ajouter des ressources requises pour générer et déboguer, sélectionnez **Not Now** (Pas maintenant).

## Configuration de votre application

Des applications pour C# et Python sont fournies, ainsi qu’un exemple de fichier texte que vous utiliserez pour tester le résumé. Les deux applications présentent les mêmes fonctionnalités. Tout d’abord, vous allez compléter certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI.

1. Dans Visual Studio Code, dans le volet **Explorateur**, accédez au dossier **Labfiles/04-code-generation**, puis développez le dossier **CSharp** ou **Python**, selon le langage que vous préférez. Chaque dossier contient les fichiers propres au langage d’une application dans laquelle vous allez intégrer des fonctionnalités Azure OpenAI.
2. Cliquez avec le bouton droit sur le dossier **CSharp** ou **Python** contenant vos fichiers de code, puis ouvrez un terminal intégré. Installez ensuite le package du SDK Azure OpenAI en exécutant la commande appropriée pour le langage de votre choix :

    **C# :**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    ```

    **Python** :

    ```powershell
    pip install openai==1.65.2
    ```

3. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de configuration pour le langage de votre choix

    - **C#** : appsettings.json
    - **Python** : .env

4. Mettez à jour les valeurs de configuration pour inclure :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le **nom de déploiement** que vous avez spécifié pour votre modèle de déploiement (disponible sur la page **Déploiements** dans le portail Azure AI Foundry).
5. Enregistrez le fichier de configuration.

## Ajouter du code pour utiliser votre modèle de service Azure OpenAI

Vous êtes maintenant prêt à utiliser le Kit de développement logiciel (SDK) Azure OpenAI pour consommer votre modèle déployé.

1. Dans le volet **Explorateur**, dans le dossier **CSharp** ou **Python**, ouvrez le fichier de code pour le langage de votre choix. Dans la fonction qui appelle le modèle Azure OpenAI, sous le commentaire ***Format and send the request to the model*** (Mettre en forme et envoyer la requête au modèle), ajoutez le code pour mettre en forme et envoyer la requête au modèle.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };
    
    // Get response from Azure OpenAI
    ChatCompletion response = await chatClient.CompleteChatAsync(
        [
            new SystemChatMessage(systemPrompt),
            new UserChatMessage(userPrompt),
        ],
        chatCompletionsOptions);
    ```

    **Python** : code-generation.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

1. Enregistrez les modifications apportées au fichier de code.

## Exécution de l'application

Maintenant que votre application est configurée, exécutez-la pour essayer de générer du code pour chaque cas d’usage. Le cas d’usage est numéroté dans l’application et peut être exécuté dans n’importe quel ordre.

> **Remarque** : Certains utilisateurs peuvent rencontrer une limitation du débit si le modèle est trop souvent appelé. Si vous rencontrez une erreur concernant une limite de débit de jetons, attendez une minute, puis réessayez.

1. Dans le volet **Explorateur**, développez le dossier **Labfiles/04-code-generation/sample-code**, puis examinez la fonction et l’application *go-fish* pour votre langage. Ces fichiers seront utilisés pour les tâches dans l’application.
2. Dans le volet de terminal interactif, vérifiez que le contexte du dossier est le dossier correspondant à votre langue préférée. Exécutez ensuite la commande suivante pour exécuter l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python code-generation.py`

    > **Conseil** : vous pouvez utiliser l’icône **Agrandir la taille du volet** (**^**) dans la barre d’outils du terminal pour afficher plus de texte sur la console.

3. Choisissez l’option **1** pour ajouter des commentaires à votre code, puis entrez le prompt suivant. Notez que la réponse risque de prendre quelques secondes pour chacune de ces tâches.

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    Les résultats seront placés dans **result/app.txt**. Ouvrez ce fichier et comparez-le au fichier de fonction dans **sample-code**.

4. Ensuite, choisissez l’option **2** afin d’écrire des tests unitaires pour cette même fonction, puis entrez le prompt suivant.

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    Les résultats vont remplacer ce qui était dans **result/app.txt** et détaillent quatre tests unitaires pour cette fonction.

5. Ensuite, choisissez l’option **3** afin de corriger les bogues dans une application pour jouer à Go Fish. Entrez le prompt suivant.

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    Les résultats vont remplacer ce qui était dans **result/app.txt** et devraient avoir un code très similaire avec quelques éléments corrigés.

    - **C#**  : Les corrections sont apportées aux lignes 30 et 59
    - **Python** : Les corrections sont apportées aux lignes 18 et 31

    L’application pour Go Fish dans **sample-code** peut être exécutée si vous remplacez les lignes contenant les bogues par la réponse d’Azure OpenAI. Si vous l’exécutez sans les corrections, elle ne fonctionnera pas bien.

    > **Remarque** : Il est important de noter que même si des éléments de syntaxe du code de cette application Go Fish ont été corrigés, il ne s’agit pas d’une représentation strictement exacte du jeu. Si vous regardez de près, il y a des problèmes comme l’absence de vérification si le paquet est vide lors du tirage des cartes, l’absence de suppression des paires de la main des joueurs lorsqu’ils ont une paire, et quelques autres bogues qui demandent de comprendre les jeux de cartes pour les détecter. Il s’agit d’un très bon exemple montrant comment les modèles d’IA générative peuvent être utiles dans la génération de code, mais ils ne peuvent pas être considérés d’emblée comme corrects et doivent être vérifiés par le développeur.

    Si vous voulez voir la réponse complète d’Azure OpenAI, vous pouvez définir la variable **printFullResponse** sur `True`, puis réexécuter l’application.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou la ressource entière dans le **Portail Azure** à `https://portal.azure.com`.
