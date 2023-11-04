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
2. Dans Azure OpenAI Studio, créez un déploiement avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : my-gpt-model

> **Remarque** : Azure OpenAI comprend plusieurs modèles, chacun optimisé pour un équilibre différent entre les fonctionnalités et les performances. Dans cet exercice, vous allez utiliser le modèle **GPT-35-Turbo**, qui est un bon modèle général pour résumer et générer du langage naturel et du code. Pour plus d’informations sur les modèles disponibles dans Azure OpenAI, consultez [Modèles](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/models) dans la documentation Azure OpenAI.

## Explorer un modèle dans le terrain de jeu Complétions

Les *terrains de jeu* sont des interfaces utiles dans Azure OpenAI Studio que vous pouvez utiliser pour expérimenter vos modèles déployés sans avoir à développer votre propre application cliente.

1. Dans Azure OpenAI Studio, dans le volet gauche sous **Terrain de jeu**, sélectionnez **Complétions**.
2. Sur la page **Saisies semi-automatiques**, vérifiez que votre déploiement **my-gpt-model** est sélectionné, puis dans la liste **Exemples**, sélectionnez **Générer un questionnaire**.

    L’exemple de texte de synthèse se compose d’une *invite* qui fournit du texte pour indiquer au modèle quel type de réponse est requis et inclure des informations contextuelles.

3. En bas de la page, notez le nombre de *tokens* détectés dans le texte. Les tokens sont les unités de base d’un prompt : essentiellement des mots ou des parties de mots dans le texte.
4. Utilisez le bouton **Générer** pour soumettre le prompt au modèle et récupérer une réponse.

    La réponse se compose d’un questionnaire basé sur l’exemple dans l’invite.

5. Utilisez le bouton **Regénérer** pour resoumettre le prompt et notez que la réponse peut varier de la réponse d’origine. Un modèle d’IA générative peut produire un nouveau texte chaque fois qu’il est appelé.
6. Utilisez le bouton **Afficher le code** pour voir le code qu’une application cliente utiliserait pour envoyer l’invite. Vous pouvez sélectionner votre langage de programmation préféré. L’invite contient le texte que vous avez soumis au modèle. La demande est envoyée à l’API *Completions* pour votre instance Azure OpenAI Service.

## Utiliser le terrain de jeu Conversation

Le terrain de jeu *Conversation* fournit une interface de chatbot pour les modèles GPT 3.5 et ultérieurs. Il utilise l’API *ChatCompletions* plutôt que l’ancienne API *Completions*.

1. Dans la section **Terrain de jeu**, sélectionnez la page **Conversation** et vérifiez que le modèle **my-gpt-model** est sélectionné dans le volet de configuration à droite.
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
9. Utilisez le bouton **Afficher le code** pour voir le code de l’interaction. L’invite se compose du message *système*, d’un petit nombre d’exemples de messages de l’*utilisateur* et de l’*assistant*, ainsi que de la séquence des messages de l’*utilisateur* et de l’*assistant* dans la session de conversation.

## Explorer les prompts et les paramètres

Vous pouvez utiliser l’invite et les paramètres pour optimiser la probabilité de générer la réponse dont vous avez besoin.

1. Dans le volet **Paramètres**, définissez les valeurs de paramètre suivantes :
    - **Température** : 0
    - **Longueur maximale (tokens)**  : 500

2. Envoyer le message suivant

    ```
    Write three multiple choice questions based on the following text.

    Most computer vision solutions are based on machine learning models that can be applied to visual input from cameras, videos, or images.*

    - Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    - Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    - Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.
    ```

3. Passez en revue les résultats, qui devraient se composer de questions à choix multiples qu’un enseignant pourrait utiliser pour évaluer les étudiants sur les sujets de vision par ordinateur de l’invite. La totalité de la réponse doit être inférieure à la longueur maximale que vous avez spécifiée en tant que paramètre.

    Observez ce qui suit concernant le prompt et les paramètres que vous avez utilisés :

    - L’invite indique spécifiquement que la sortie souhaitée doit être trois questions à choix multiples.
    - Les paramètres comprennent *Température*, qui contrôle le degré d’aléatoire inclus dans la génération de réponse. La valeur **0** utilisée dans votre soumission réduit le caractère aléatoire au minimum, ce qui donne des réponses stables et prévisibles.

## Explorer la génération de code

En plus de générer des réponses en langage naturel, vous pouvez utiliser des modèles GPT pour générer du code.

1. Dans le volet **Configuration de l’Assistant**, sélectionnez le modèle **Exemple vide** pour réinitialiser le message système.
2. Entrez le message système `You are a Python developer.`, puis enregistrez les modifications.
3. Dans le volet **Session de conversation**, sélectionnez **Effacer la conversation** pour effacer l’historique des conversations et démarrer une nouvelle session.
4. Envoyez le message utilisateur suivant :

    ```
    Write a Python function named Multiply that multiplies two numeric parameters.
    ```

5. Passez en revue la réponse, qui devrait inclure un exemple de code Python répondant à la demande de l’invite.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou l’intégralité de la ressource dans le [portail Azure](https://portal.azure.com).
