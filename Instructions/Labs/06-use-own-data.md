---
lab:
  title: Utiliser vos propres données avec Azure OpenAI
---

# Utiliser vos propres données avec Azure OpenAI

Azure OpenAI Service vous permet d’utiliser vos propres données avec l’intelligence du LLM sous-jacent. Vous pouvez limiter le modèle afin qu’il utilise uniquement vos données pour les rubriques pertinentes, ou les fusionner avec les résultats du modèle préentraîné.

Cet exercice prend environ **20** minutes.

## Avant de commencer

Vous devez avoir un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service. 

- Pour obtenir un abonnement gratuit à Azure, visitez [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Pour demander l’accès à Azure OpenAI Service, visitez [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Provisionner une ressource Azure OpenAI

Avant de pouvoir utiliser des modèles Azure OpenAI, vous devez provisionner une ressource Azure OpenAI dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com?azure-portal=true).
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : Un abonnement Azure qui a été approuvé pour pouvoir accéder à Azure OpenAI Service.
    - **Groupe de ressources** : choisissez un groupe de ressources existant ou créez un groupe de ressources en lui attribuant le nom de votre choix.
    - **Région** : Choisissez une région disponible.
    - **Nom** : Un nom unique de votre choix.
    - **Niveau tarifaire** : Standard S0
3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.

## Déployer un modèle

Pour converser avec Azure OpenAI, vous devez d’abord déployer un modèle à utiliser via **Azure OpenAI Studio**. Une fois le modèle déployé, nous l’utiliserons avec le terrain de jeu et utiliserons nos données pour baser ses réponses.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur. Vous pouvez aussi accéder à [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true) directement.
2. Dans Azure OpenAI Studio, sur la page **Déploiements**, affichez vos déploiements de modèles existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo-16k
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

> **Remarque** : dans certaines régions, la nouvelle interface de déploiement de modèle n’affiche pas l’option **Version du modèle**. Dans ce cas, ne vous inquiétez pas et continuez sans définir l’option.

## Observer un comportement de conversation normal sans ajouter vos propres données

Avant de connecter Azure OpenAI à vos données, observez d’abord comment le modèle de base répond aux requêtes sans données de base.

1. Accédez au terrain de jeu **Conversation** et vérifiez que le modèle que vous avez déployé est sélectionné dans le volet **Configuration** (il doit s’agir de la valeur par défaut, si vous n’avez qu’un seul modèle déployé).
1. Entrez les invites suivantes et observez la sortie.

    ```code
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```code
    What are some facts about New York?
    ```

1. Essayez des questions similaires sur le tourisme et les lieux de séjour pour d’autres endroits qui seront inclus dans nos données de base, comme Londres ou San Francisco. Vous obtiendrez probablement des réponses complètes sur les quartiers, et quelques faits généraux sur la ville.

## Connecter vos données dans le terrain de jeu de conversation

Ensuite, ajoutez vos données dans le terrain de jeu de conversation pour voir comment il répond avec vos données en tant que base.

1. [Téléchargez les données](https://aka.ms/own-data-brochures) que vous utiliserez à partir de GitHub. Extrayez les PDF du fichier `.zip` fourni.
1. Accédez au terrain de jeu **Conversation**, puis sélectionnez *Ajouter vos données* dans le volet Configuration de l’Assistant.
1. Sélectionnez **Ajouter une source de données** et choisissez *Charger des fichiers* dans la liste déroulante.
1. Vous devez créer un compte de stockage et une ressource Recherche Azure AI. Dans la liste déroulante de la ressource de stockage, sélectionnez **Créer une ressource de stockage Blob Azure**, puis créez un compte de stockage avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *même abonnement que votre ressource Azure OpenAI*
    - **Groupe de ressources** : *même groupe de ressources que votre ressource Azure OpenAI*
    - **Nom du compte de stockage** : *entrez un nom global unique*.
    - **Région** : *même région que votre ressource Azure OpenAI*
    - **Redondance** : stockage localement redondant (LRS)

1. Une fois la ressource créée, revenez à Azure OpenAI Studio et sélectionnez **Créer une ressource Recherche Azure AI** avec les paramètres suivants. Pour tout ce qui n’est pas spécifié, conservez les paramètres par défaut.

    - **Abonnement** : *même abonnement que votre ressource Azure OpenAI*
    - **Groupe de ressources** : *même groupe de ressources que votre ressource Azure OpenAI*
    - **Nom du service** : *entrez un nom global unique*
    - **Emplacement** : *même emplacement que votre ressource Azure OpenAI*
    - **Niveau tarifaire** : De base

1. Attendez que votre ressource de recherche ait été déployée, puis revenez à Azure AI Studio et actualisez la page.
1. Dans **Ajouter des données**, entrez les valeurs suivantes pour votre source de données, puis sélectionnez **Suivant**.

    - **Sélectionner la source de données** : Charger des fichiers
    - **Sélectionner la ressource Stockage Blob Azure** : *choisissez la ressource de stockage que vous avez créée*
        - Activer CORS à l’invite
    - **Sélectionner la ressource Recherche Azure AI** : *choisissez la ressource de recherche que vous avez créée*
    - **Entrez le nom de l’index** : margiestravel
    - **Ajouter la recherche vectorielle à cette ressource de recherche** : décochée
    - **Je reconnais que la connexion à un compte Recherche Azure AI entraîne l’utilisation de mon compte** : coché

1. Dans la page **Charger des fichiers**, chargez les fichiers PDF téléchargés, puis sélectionnez **Suivant**.
1. Dans la page **Gestion des données**, sélectionnez le type de recherche par **mot clé** dans la liste déroulante, puis **Suivant**.
1. Dans la page **Vérifier, puis terminer**, sélectionnez **Enregistrer et fermer** pour ajouter vos données. Cette opération peut prendre quelques minutes, pendant lesquelles vous devez laisser votre fenêtre ouverte. Une fois l’opération terminée, vous verrez la source de données, la ressource de recherche et l’index spécifiés dans le volet **Configuration de l’Assistant**.

## Discuter avec un modèle ancré dans vos données

Maintenant que vous avez ajouté vos données, posez les mêmes questions que précédemment et voyez en quoi la réponse diffère.

```
I'd like to take a trip to New York. Where should I stay?
```

```
What are some facts about New York?
```

Vous remarquerez une réponse très différente cette fois, avec des détails sur certains hôtels et une mention de Margie’s Travel, ainsi que des références à l’endroit d’où proviennent les informations fournies. Si vous ouvrez la référence PDF listée dans la réponse, vous verrez les mêmes hôtels que ceux fournis par le modèle.

Essayez de l’interroger sur d’autres villes incluses dans les données de base, à savoir Dubaï, Las Vegas, Londres et San Francisco.

> **Remarque** : **Ajouter vos données** est toujours en préversion, et peut ne pas toujours se comporter comme prévu pour cette fonctionnalité, par exemple fournir une référence incorrecte pour une ville non incluse dans les données de base.

## Connectez votre application à vos propres données

Découvrez ensuite comment connecter votre application pour qu’elle utilise vos propres données.

### Configurer une application dans Cloud Shell

Pour montrer comment connecter une application Azure OpenAI à vos propres données, nous allons utiliser une petite application en ligne de commande qui s’exécute dans Cloud Shell sur Azure. Ouvrez un nouvel onglet de navigateur pour utiliser Cloud Shell.

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
    cd azure-openai/Labfiles/06-use-own-data
    ```

7. Ouvrez l’éditeur de code intégré en exécutant la commande suivante :

    ```bash
    code .
    ```

    > **Conseil** : consultez la [documentation de l’éditeur de code Azure Cloud Shell](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) pour plus d’informations sur l’utilisation des fichiers dans l’environnement Azure Cloud Shell.

## Configuration de votre application

Pour cet exercice, vous allez effectuer certaines parties clés de l’application pour activer l’utilisation de votre ressource Azure OpenAI. Des applications pour C# et Python sont fournies. Les deux applications présentent les mêmes fonctionnalités.

1. Dans l’éditeur de code, développez le dossier **CSharp** ou **Python** en fonction du langage que vous préférez.

2. Ouvrez le fichier de configuration pour votre langage.

    - C#: `appsettings.json`
    - Python : `.env`
    
3. Mettez à jour les valeurs de configuration pour inclure :
    - Le **point de terminaison** et une **clé** de la ressource Azure OpenAI que vous avez créée (disponible sur la page **Clés et point de terminaison** de votre ressource Azure OpenAI dans le portail Azure).
    - Le nom que vous avez spécifié pour votre déploiement de modèle (disponible dans la page **Déploiements** dans Azure OpenAI Studio).
    - Le point de terminaison de votre service de recherche (valeur **url** dans la page de vue d’ensemble de votre ressource de recherche dans le portail Azure).
    - Une **clé** de votre ressource de recherche (disponible sur la page **Clés** de votre ressource de recherche dans le portail Azure. Vous pouvez utiliser l’une des clés d’administration).
    - Le nom de l’index de recherche (qui doit être `margiestravel`).
    
4. Enregistrez le fichier de configuration mis à jour.

5. Dans le volet de la console, saisissez les commandes suivantes pour accéder au dossier de votre langage préféré et installer les packages nécessaires.

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

6. Dans l’éditeur de code, accédez au dossier de votre langage préféré, sélectionnez le fichier de code et ajoutez les bibliothèques nécessaires.

    **C#**  : OwnData.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python** : ownData.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

7. Passez en revue le fichier de code, en particulier les valeurs de recherche utilisées lors de la définition des paramètres de l’appel d’API.

## Exécuter votre application

Maintenant que votre application a été configurée, exécutez-la pour envoyer votre demande à votre modèle et observer la réponse. Vous remarquerez que la réponse est maintenant fondée sur vos données de la même façon que l’expérience Studio.

1. Dans le terminal bash Cloud Shell, accédez au dossier de votre langage préféré.
1. Développez le terminal pour occuper une grosse partie de la fenêtre de votre navigateur et exécutez l’application.

    - **C#**  : `dotnet run`
    - **Python** : `python ownData.py`

1. Envoyez l’invite `Tell me about London`. Vous devez alors voir la réponse référençant vos données.

## Nettoyage

Lorsque vous en avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de la supprimer dans le [portail Azure](https://portal.azure.com/?azure-portal=true). Veillez également à inclure le compte de stockage et la ressource de recherche, car ceux-ci peuvent entraîner un coût relativement élevé.
