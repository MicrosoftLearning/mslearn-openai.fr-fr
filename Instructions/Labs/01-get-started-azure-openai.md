---
lab:
  title: Bien démarrer avec Azure OpenAI
---

# Démarrage de Azure OpenAI Service

Azure OpenAI Service intègre les modèles d’IA générative développés par OpenAI à la plateforme Azure, ce qui vous permet de développer de puissantes solutions d’IA qui bénéficient de la sécurité, de la scalabilité et de l’intégration de services fournies par la plateforme cloud Azure. Dans cet exercice, vous allez apprendre à utiliser Azure OpenAI en provisionnant le service en tant que ressource Azure et en utilisant Azure OpenAI Studio pour déployer et explorer des modèles OpenAI.

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

## Déployer un modèle

Azure OpenAI fournit un portail web appelé **Azure OpenAI Studio**, que vous pouvez utiliser pour déployer, gérer et explorer des modèles. Vous allez commencer votre exploration d’Azure OpenAI en utilisant Azure OpenAI Studio pour déployer un modèle.

1. Sur la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Accéder à Azure OpenAI Studio** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur.
2. Dans Azure OpenAI Studio, sur la page **Déploiements**, affichez vos déploiements de modèles existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo-16k
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** : activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

> **Remarque** : dans certaines régions, la nouvelle interface de déploiement de modèle n’affiche pas l’option **Version du modèle**. Dans ce cas, ne vous inquiétez pas et continuez sans définir l’option.

## Utiliser le terrain de jeu Conversation

Le terrain de jeu *Conversation* fournit une interface de chatbot pour les modèles GPT 3.5 et ultérieurs. Il utilise l’API *ChatCompletions* plutôt que l’ancienne API *Completions*.

1. Dans la section **Terrain de jeu**, sélectionnez la page **Conversation** et vérifiez que votre modèle est sélectionné dans le volet de configuration.
2. Dans la section **Configuration de l’Assistant**, dans la zone **Message système**, remplacez le texte actuel par l’instruction suivante : `The system is an AI teacher that helps people learn about AI`.

3. Sous la zone **Message système**, cliquez sur **Ajouter des exemples en peu de coups**, puis entrez le message et la réponse suivants dans les zones désignées :

    - **Utilisateur** : `What are different types of artificial intelligence?`
    - **Assistant** : `There are three main types of artificial intelligence: Narrow or Weak AI (such as virtual assistants like Siri or Alexa, image recognition software, and spam filters), General or Strong AI (AI designed to be as intelligent as a human being. This type of AI does not currently exist and is purely theoretical), and Artificial Superintelligence (AI that is more intelligent than any human being and can perform tasks that are beyond human comprehension. This type of AI is also purely theoretical and has not yet been developed).`

    > **Remarque** : Des exemples peu nombreux (« few-shot examples ») sont utilisés pour fournir au modèle des exemples de types de réponses attendues. Le modèle tente de refléter le ton et le style des exemples dans ses propres réponses.

4. Enregistrez les modifications pour démarrer une nouvelle session et définir le contexte comportemental du système de conversation.
5. Dans la zone de la requête, en bas de la page, entrez le texte `What is artificial intelligence?`
6. Utilisez le bouton **Envoyer** pour envoyer le message et afficher la réponse.

    > **Remarque** : Vous pouvez recevoir une réponse indiquant que le déploiement de l’API n’est pas encore prêt. Si tel est le cas, patientez quelques minutes et réessayez.

7. Examinez la réponse, puis envoyez le message suivant pour poursuivre la conversation : `How is it related to machine learning?`
8. Examinez la réponse et notez que le contexte de l’interaction précédente a été retenu (de sorte que le modèle comprend que « it » fait référence à l’intelligence artificielle).
9. Utilisez le bouton **Afficher le code** pour voir le code de l’interaction. Le prompt se compose du message *système*, d’un petit nombre d’exemples de messages de l’*utilisateur* et de l’*assistant*, ainsi que de la séquence des messages de l’*utilisateur* et de l’*assistant* dans la session de conversation.

## Explorer les prompts et les paramètres

Vous pouvez utiliser le prompt et les paramètres pour optimiser la probabilité de générer la réponse dont vous avez besoin.

1. Dans le volet **Paramètres**, définissez les valeurs de paramètre suivantes :
    - **Température** : 0
    - **Réponse maximale** : 500

2. Envoyer le message suivant

    ```
    Write three multiple choice questions based on the following text.

    Most computer vision solutions are based on machine learning models that can be applied to visual input from cameras, videos, or images.*

    - Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    - Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    - Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.
    ```

3. Passez en revue les résultats, qui devraient se composer de questions à choix multiples qu’un enseignant pourrait utiliser pour évaluer les étudiants sur les sujets de vision par ordinateur du prompt. La totalité de la réponse doit être inférieure à la longueur maximale que vous avez spécifiée en tant que paramètre.

    Observez ce qui suit concernant le prompt et les paramètres que vous avez utilisés :

    - Le prompt indique spécifiquement que la sortie souhaitée doit être trois questions à choix multiples.
    - Les paramètres comprennent *Température*, qui contrôle le degré d’aléatoire inclus dans la génération de réponse. La valeur **0** utilisée dans votre soumission réduit le caractère aléatoire au minimum, ce qui donne des réponses stables et prévisibles.

## Explorer la génération de code

En plus de générer des réponses en langage naturel, vous pouvez utiliser des modèles GPT pour générer du code.

1. Dans le volet **Configuration de l’Assistant**, sélectionnez le modèle **Exemple vide** pour réinitialiser le message système.
2. Entrez le message système `You are a Python developer.`, puis enregistrez les modifications.
3. Dans le volet **Session de conversation**, sélectionnez **Effacer la conversation** pour effacer l’historique des conversations et démarrer une nouvelle session.
4. Envoyez le message utilisateur suivant :

    ```
    Write a Python function named Multiply that multiplies two numeric parameters.
    ```

5. Passez en revue la réponse, qui devrait inclure un exemple de code Python répondant à la demande du prompt.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com).
