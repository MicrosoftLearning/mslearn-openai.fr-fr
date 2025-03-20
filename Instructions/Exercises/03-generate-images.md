---
lab:
  title: Générer des images avec l’IA
  description: Découvrez comment utiliser un modèle DALL-E OpenAI pour générer des images.
---

# Générer des images avec l’IA

Dans cet exercice, vous utilisez le modèle d’IA générative DALL-E OpenAI pour générer des images. Vous allez développer votre application à l’aide d’Azure AI Foundry et du service Azure OpenAI.

Cet exercice prend environ **30** minutes.

## Créer un projet Azure AI Foundry

Commençons par créer un projet Azure AI Foundry.

1. Dans un navigateur web, ouvrez le [portail Azure AI Foundry](https://ai.azure.com) à l’adresse `https://ai.azure.com` et connectez-vous en utilisant vos informations d’identification Azure. Fermez les conseils ou les volets de démarrage rapide ouverts la première fois que vous vous connectez et, si nécessaire, utilisez le logo **Azure AI Foundry** en haut à gauche pour accéder à la page d’accueil, qui ressemble à l’image suivante :

    ![Capture d’écran du portail Azure AI Foundry.](../media/ai-foundry-home.png)

1. Sur la page d’accueil, sélectionnez **+Créer un projet**.
1. Dans l’assistant **Créer un projet**, entrez un nom de projet approprié pour (par exemple, `my-ai-project`), puis passez en revue les ressources Azure qui seront automatiquement créées pour prendre en charge votre projet.
1. Sélectionnez **Personnaliser** et spécifiez les paramètres suivants pour votre hub :
    - **Nom du hub** : *nom unique, par exemple `my-ai-hub`*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *créez un groupe de ressources avec un nom unique ( par exemple, `my-ai-resources`) ou sélectionnez un groupe de ressources existant.*
    - **Emplacement** : sélectionnez **Aidez-moi à choisir**, puis sélectionnez **dalle** dans la fenêtre de l’assistant de l’emplacement et utilisez la région recommandée.\*
    - **Connecter Azure AI Services ou Azure OpenAI** : *créer une ressource AI Services avec un nom approprié (par exemple, `my-ai-services`) ou utiliser une ressource existante*
    - **Connecter la Recherche Azure AI** : ignorer la connexion

    > \* Les ressources Azure OpenAI sont limitées au niveau du locataire par quotas régionaux. Si une limite de quota est atteinte plus tard dans l’exercice, vous devrez peut-être créer une autre ressource dans une autre région.

1. Sélectionnez **Suivant** et passez en revue votre configuration. Sélectionnez **Créer** et patientez jusqu’à ce que l’opération se termine.
1. Une fois votre projet créé, fermez les conseils affichés et passez en revue la page du projet dans le portail Azure AI Foundry, qui doit ressembler à l’image suivante :

    ![Capture d’écran des détails d’un projet Azure AI dans le portail Azure AI Foundry.](../media/ai-foundry-project.png)

## Déployer un modèle DALL-E

Vous êtes maintenant prêt à déployer un modèle DALL-E pour prendre en charge la génération d’images.

1. Dans la barre d’outils située en haut à droite de votre projet Azure AI Foundry, utilisez l’icône **fonctionnalités en version préliminaires** pour activer la fonctionnalité **Déployer des modèles sur le service d’inférence de modèle Azure AI**. Cette fonctionnalité garantit que votre déploiement de modèle est disponible pour le service Inférence Azure AI, que vous utiliserez dans votre code d’application.
1. Dans le volet de gauche de votre projet, dans la section **Mes ressources**, sélectionnez la page **Modèles + points de terminaison**.
1. Sur la page **Modèles + points de terminaison**, dans l’onglet **Déploiements de modèles**, dans le menu **+ Déployer un modèle**, sélectionnez **Déployer le modèle de base**.
1. Recherchez le modèle **dall-e-3** dans la liste, puis sélectionnez-le et confirmez-le.
1. Acceptez le contrat de licence si vous y êtes invité, puis déployez le modèle avec les paramètres suivants en sélectionnant **Personnaliser** dans les détails du déploiement :
    - **Nom du déploiement** : *nom unique de votre déploiement de modèle, par exemple `dall-e-3` (n’oubliez pas que vous aurez besoin plus tard du nom que vous attribuez*)
    - **Type de déploiement** : Standard
    - **Détails du déploiement** : *utilisez les paramètres par défaut*
1. Attendez que l’état d’approvisionnement du déploiement soit **Terminé**.

## Tester le modèle dans le terrain de jeu

Avant de créer une application cliente, testons le modèle DALL-E dans le terrain de jeu.

1. Dans la page du modèle DALL-E que vous avez déployé, sélectionnez **Ouvrir dans le terrain de jeu** (ou dans la page **Terrains de jeu**, ouvrez le **terrain de jeux d’images**).
1. Vérifiez que votre déploiement de modèle DALL-E est sélectionné. Ensuite, dans la zone d’**invite**, entrez une invite telle que `Create an image of an robot eating spaghetti`.
1. Passez en revue l’image résultante dans le terrain de jeu :

    ![Capture d’écran du terrain de jeu d’images avec une image générée.](../media/images-playground.png)

1. Entrez une invite de suivi, telle que `Show the robot in a restaurant`, et passez en revue l’image résultante.
1. Continuez à tester avec de nouvelles invites pour affiner l’image jusqu’à ce que vous soyez satisfait de celle-ci.

## Créer une application cliente

Le modèle semble fonctionner dans le terrain de jeu. Vous pouvez maintenant utiliser le Kit de développement logiciel (SDK) Azure OpenAI pour l’utiliser dans une application cliente.

> **Conseil** : vous pouvez choisir de développer votre solution à l’aide de Python ou de Microsoft C#. Suivez les instructions de la section appropriée pour votre langue choisie.

### Préparer la configuration de l’application

1. Dans le portail Azure AI Foundry, affichez la page **Vue d’ensemble** de votre projet.
1. Dans la zone **Détails du projet**, notez la **chaîne de connexion du projet**. Vous utiliserez cette chaîne de connexion pour vous connecter à votre projet dans une application cliente.
1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.
1. Cliquez sur le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell***. Cloud Shell fournit une interface de ligne de commande dans un volet situé en bas du portail Azure.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    > **Conseil** : lorsque vous collez des commandes dans cloudshell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Dans le volet PowerShell, entrez les commandes suivantes pour cloner le référentiel GitHub pour cet exercice :

    ```
    rm -r mslearn-openai -f
    git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
    ```

> **Note** : suivez les étapes de votre langage de programmation choisi.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application :  

    **Python**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/Python
    ```

    **C#**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/CSharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous utiliserez, c’est-à-dire :

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    *Vous pouvez ignorer les erreurs relatives à la version de pip et au chemin local.*

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Dans le fichier de code, remplacez l’espace réservé **your_project_endpoint** par la chaîne de connexion de votre projet (copié à partir de la page **Vue d’ensemble** du projet dans le portail Azure AI Foundry) et l’espace réservé **your_model_deployment** par le nom que vous avez attribué à votre déploiement de modèle dall-e-3.
1. Une fois que vous avez remplacé les espaces réservés, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Écrire du code pour vous connecter à votre projet et converser avec votre modèle

> **Conseil** : lorsque vous ajoutez du code, veillez à conserver la mise en retrait correcte.

1. Saisissez la commande suivante pour modifier le fichier de code fourni :

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Dans le fichier de code, notez les instructions existantes qui ont été ajoutées en haut du fichier pour importer les espaces de noms SDK nécessaires. Ensuite, sous le commentaire **Ajouter des références**, ajoutez le code suivant pour référencer les espaces de noms dans les bibliothèques que vous avez installées précédemment :

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. Dans la fonction **main**, sous le commentaire **Obtenir les paramètres de configuration**, notez que le code charge les valeurs de chaîne de connexion du projet et de nom de déploiement du modèle que vous avez définies dans le fichier de configuration.
1. Sous le commentaire **Initialiser le client du projet**, ajoutez le code suivant pour vous connecter à votre projet Azure AI Foundry à l’aide des informations d’identification Azure avec lesquelles vous êtes actuellement connecté :

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Sous le commentaire **Obtenir un client OpenAI**, ajoutez le code suivant pour créer un objet client pour converser avec un modèle :

    **Python**

    ```
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. Notez que le code inclut une boucle pour permettre à un utilisateur d’entrer une invite jusqu’à ce qu’il entre « quitter ». Ensuite, dans la section de boucle, sous le commentaire **Générer une image**, ajoutez le code suivant pour envoyer l’invite et récupérer l’URL de l’image générée à partir de votre modèle :

    **Python**

    ```python
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. Notez que le code dans le reste de la fonction **main** transmet l’URL de l’image et un nom de fichier à une fonction fournie, qui télécharge l’image générée et l’enregistre sous forme de fichier .png.

1. Utilisez la commande **Ctrl+S** pour enregistrer vos modifications dans le fichier de code, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

### Exécution de l’application cliente

1. Dans le volet de ligne de commande Cloud Shell, entrez la commande suivante pour exécuter l’application :

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Lorsque vous y êtes invité, entrez une demande d’image, telle que `Create an image of a robot eating pizza`. Au bout de quelques instants, l’application doit confirmer que l’image a été enregistrée.
1. Essayez quelques invites supplémentaires. Lorsque vous avez terminé, entrez `quit` pour quitter le programme.

    > **Note** : dans cette application simple, nous n’avons pas implémenté de logique pour conserver l’historique des conversations. Par conséquent, le modèle traite chaque invite comme une nouvelle requête sans contexte de l’invite précédente.

1. Pour télécharger et afficher les images générées par votre application, dans la barre d’outils du volet Cloud Shell, utilisez le bouton **Charger/télécharger des fichiers** pour télécharger un fichier, puis ouvrez-le. Pour télécharger un fichier, complétez son chemin d’accès au fichier dans l’interface de téléchargement ; par exemple :

    **Python**

    /home/*user*`/mslearn-openai/Labfiles/03-image-generation/Python/images/image_1.png`

    **C#**

    /home/*user*`/mslearn-openai/Labfiles/03-image-generation/CSharp/images/image_1.png`

## Résumé

Dans cet exercice, vous avez utilisé Azure AI Foundry et le kit de développement logiciel (SDK) Azure OpenAI pour créer une application cliente utilisant un modèle DALL-E pour générer des images.

## Nettoyage

Si vous avez terminé d’explorer DALL-E, vous devez supprimer les ressources que vous avez créées dans cet exercice pour éviter d’entraîner des coûts Azure inutiles.

1. Revenez à l’onglet du navigateur contenant le portail Azure (ou ouvrez à nouveau le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` dans un nouvel onglet de navigateur) et affichez le contenu du groupe de ressources dans lequel vous avez déployé les ressources utilisées dans cet exercice.
1. Dans la barre d’outils, sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources et confirmez que vous souhaitez le supprimer.
