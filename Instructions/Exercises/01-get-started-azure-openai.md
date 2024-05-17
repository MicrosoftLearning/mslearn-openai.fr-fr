---
lab:
  title: Démarrage de Azure OpenAI Service
---

# Démarrage de Azure OpenAI Service

Azure OpenAI Service intègre les modèles d’IA générative développés par OpenAI à la plateforme Azure, ce qui vous permet de développer de puissantes solutions d’IA qui bénéficient de la sécurité, de la scalabilité et de l’intégration de services fournies par la plateforme cloud Azure. Dans cet exercice, vous allez apprendre à utiliser Azure OpenAI en provisionnant le service en tant que ressource Azure et en utilisant Azure OpenAI Studio pour déployer et explorer des modèles d’IA générative.

Dans le scénario de cet exercice, vous allez jouer le rôle d’un développeur de logiciels qui a été chargé d’implémenter un agent d’IA capable d’utiliser l’IA générative pour aider une organisation marketing à améliorer son efficacité pour atteindre les clients et lancer la publicité de nouveaux produits. Les techniques utilisées dans l’exercice peuvent être appliquées à n’importe quel scénario dans lequel une organisation souhaite utiliser des modèles d’IA générative pour aider les employés à être plus efficaces et productifs.

Cet exercice prend environ **30** minutes.

## Provisionner une ressource Azure OpenAI

Si vous n’avez pas encore de ressource Azure OpenAI dans votre abonnement Azure, provisionnez-en une.

1. Connectez-vous au **portail Azure** à l’adresse `https://portal.azure.com`.
2. Créez une ressource **Azure OpenAI** avec les paramètres suivants :
    - **Abonnement** : *Sélectionner un abonnement Azure approuvé pour l’accès au service Azure OpenAI*
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

Le service Azure OpenAI fournit un portail web appelé **Azure OpenAI Studio**, que vous pouvez utiliser pour déployer, gérer et explorer des modèles. Vous allez commencer votre exploration d’Azure OpenAI en utilisant Azure OpenAI Studio pour déployer un modèle.

> **Remarque** : Lorsque vous utilisez Azure OpenAI Studio, des boîtes de message qui suggèrent des tâches à effectuer peuvent être affichées. Vous pouvez les fermer et suivre les étapes de cet exercice.

1. Dans le portail Azure, sur la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Accéder à Azure OpenAI Studio** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur.

    Une fois le nouvel onglet ouvert, vous pouvez fermer toutes les notifications de bannière pour les nouveaux services en préversion qui s’affichent en haut de la page Azure OpenAI Studio.

1. Dans Azure OpenAI Studio, dans le panneau de gauche, sélectionnez la page **Déploiements** et affichez vos modèles de déploiement existants. Si vous n’en avez pas encore, créez un déploiement du modèle **gpt-35-turbo-16k** avec les paramètres suivants :
    - **Modèle** : gpt-35-turbo-16k *(si le modèle 16k n’est pas disponible, choisissez gpt-35-turbo)*
    - **Version du modèle** : mise à jour automatique avec la valeur par défaut
    - **Nom du déploiement** : *nom unique de votre choix*
    - **Options avancées**
        - **Filtre de contenu** : valeur par défaut
        - **Type de déploiement** : Standard
        - **Limite de débit de jetons par minute** : 5 000\*
        - **Activer le quota dynamique** :activé

    > \* Une limite de débit de 5 000 jetons par minute est plus que suffisante pour effectuer cet exercice tout permettant à d’autres personnes d’utiliser le même abonnement.

## Utiliser le terrain de jeu Conversation

Maintenant que vous avez déployé un modèle, vous pouvez l’utiliser pour générer des réponses basées sur des invites en langage naturel. Le terrain de jeu *Conversation* dans Azure OpenAI Studio fournit une interface de chatbot pour les modèles GPT 3.5 et ultérieurs.

> **Remarque :** Le terrain de jeu *Conversation* utilise l’API *ChatCompletions* plutôt que l’ancienne API *Completions* utilisée par le terrain de jeu d’*achèvements*. Le terrain de jeu d’achèvements est fourni pour la compatibilité avec les modèles plus anciens.

1. Dans la section **Terrain de jeu**, sélectionnez la page **Conversation**. La page du terrain de jeu **Conversation** se compose de trois panneaux principaux (qui peuvent être disposés horizontalement de droite à gauche ou verticalement de haut en bas en fonction de la résolution de l’écran) :
    - **Configuration** : utilisée pour définir le contexte des réponses du modèle.
    - **Session de conversation** : utilisée pour envoyer des messages de conversation et afficher les réponses.
    - **Configuration** : utilisée pour configurer les paramètres du modèle de déploiement.
1. Dans le panneau **Configuration**, vérifiez que votre modèle de déploiement gpt-35-turbo-16k est sélectionné.
1. Dans le panneau **Configuration**, passez en revue le** message système** par défaut, qui doit être *Vous êtes un assistant IA qui aide les personnes à trouver des informations.* Le message système est inclus dans les invites envoyées au modèle et fournit un contexte pour les réponses du modèle ; définition des attentes quant à la façon dont un agent d’IA basé sur le modèle doit interagir avec l’utilisateur.
1. Dans le panneau de **session de conversation**, entrez la requête utilisateur `How can I use generative AI to help me market a new product?`.

    > **Remarque** : Vous pouvez recevoir une réponse indiquant que le déploiement de l’API n’est pas encore prêt. Si tel est le cas, patientez quelques minutes et réessayez.

1. Passez en revue la réponse, en notant que le modèle a généré une réponse cohérente en langage naturel qui est pertinente pour la requête avec laquelle il a été invité.
1. Entrez la requête d’utilisateur `What skills do I need if I want to develop a solution to accomplish this?`.
1. Passez en revue la réponse, en notant que la session de conversation a conservé le contexte conversationnel (donc « cela » est interprété comme une solution d’IA générative pour le marketing). Cette contextualisation est obtenue en incluant l’historique des conversations récentes dans chaque envoi d’invite successif, de sorte que l’invite envoyée au modèle pour la deuxième requête incluait la requête et la réponse d’origine ainsi que la nouvelle entrée utilisateur.
1. Dans la barre d’outils du panneau de la **session de conversation**, sélectionnez la **suppression de la conversation** et confirmez que vous souhaitez redémarrer la session de conversation.
1. Entrez la requête `Can you help me find resources to learn those skills?` et passez en revue la réponse, qui doit être une réponse de langage naturel valide, mais puisque l’historique des conversations précédents a été perdu, la réponse est susceptible de porter sur la recherche de ressources de compétences génériques plutôt que d’être liée aux compétences spécifiques nécessaires pour créer une solution marketing d’IA générative.

## Tester avec des messages système, des invites et des exemples à quelques captures

Jusqu’à présent, vous avez engagé une conversation instantanée avec votre modèle basée sur le message système par défaut. Vous pouvez personnaliser la configuration du système pour avoir plus de contrôle sur les types de réponses générés par votre modèle.

1. Dans le panneau **Configuration**, sous **Utiliser un modèle de message système**, sélectionnez le modèle **Assistant d’écriture marketing** et confirmez que vous souhaitez mettre à jour le message système.
1. Passez en revue le nouveau message système, qui décrit comment un agent d’IA doit utiliser le modèle pour répondre.
1. Dans le panneau de la **session de conversation**, entrez la requête utilisateur `Create an advertisement for a new scrubbing brush`.
1. Passez en revue la réponse, qui doit inclure une copie publicitaire pour une brosse de nettoyage. La copie peut être assez étendue et créative.

    Dans un scénario réel, un professionnel du marketing connaîtrait probablement déjà le nom du produit de la brosse de nettoyage, et aurait aussi quelques idées sur les caractéristiques clés qui devraient être mises en évidence dans une publicité. Pour obtenir les résultats les plus utiles d’un modèle d’IA générative, les utilisateurs doivent concevoir leurs invites pour inclure autant d’informations pertinentes que possible.

1. Entrez l’invite `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`.
1. Passez en revue la réponse, qui doit tenir compte des informations supplémentaires que vous avez fournies sur le produit de la brosse de nettoyage.

    La réponse doit maintenant être plus utile, mais pour avoir encore plus de contrôle sur le résultat du modèle, vous pouvez fournir un ou plusieurs exemples *à quelques captures* sur lesquels les réponses doivent être basées.

1. Dans le panneau **Configuration**, sous **Exemples**, sélectionnez **Ajouter**. Tapez ensuite le message et la réponse suivants dans les zones désignées :

    **Utilisateur** :
    
    ```
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```
    
    **Assistant :**
    
    ```
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. Utilisez le bouton **Appliquer les modifications** pour enregistrer les exemples et démarrer une nouvelle session.
1. Dans la section de la **session de conversation**, entrez la requête utilisateur `Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`.
1. Passez en revue la réponse, qui doit être une nouvelle publicité pour le « Scrubadub 2000 » modélisé sur l’exemple « Ultramop » fourni dans la configuration du système.

## Tester avec des paramètres

Vous avez exploré la façon dont le message système, les exemples et les invites peuvent aider à affiner les réponses retournées par le modèle. Vous pouvez également utiliser des paramètres pour contrôler le comportement du modèle.

1. Dans le panneau **Configuration**, sélectionnez l’onglet **Paramètres** et définissez les valeurs de paramètres suivantes :
    - **Réponse maximale** : 1000
    - **Temperature** : 1

1. Dans la section de la **session de conversation**, utilisez le bouton **Effacer la conversation** pour réinitialiser la session de conversation. Entrez ensuite la requête utilisateur `Create an advertisement for a cleaning sponge` et passez en revue la réponse. La copie publicitaire résultante doit inclure un maximum de 1 000 jetons de texte et inclure certains éléments créatifs, par exemple le modèle peut avoir inventé un nom de produit pour l’éponge et avoir donné certaines indications sur ses caractéristiques.
1. Utilisez le bouton **Effacer la conversation** pour réinitialiser la session de conversation, puis entrez à nouveau la même requête que précédemment (`Create an advertisement for a cleaning sponge`) et passez en revue la réponse. La réponse peut être différente de la réponse précédente.
1. Dans le panneau **Configuration**, sous l’onglet **Paramètres**, remplacez la valeur du paramètre **Température** par 0.
1. Dans la section de la **session de conversation**, utilisez le bouton **Effacer la conversation** pour réinitialiser la session de conversation, puis entrez à nouveau la même requête que précédemment (`Create an advertisement for a cleaning sponge`) et passez en revue la réponse. Cette fois, la réponse peut ne pas être assez créative.
1. Utilisez le bouton **Effacer la conversation** pour réinitialiser la session de conversation une fois de plus, puis entrez à nouveau la même requête que précédemment (`Create an advertisement for a cleaning sponge`) et passez en revue la réponse, qui doit être très similaire (sinon identique) à la réponse précédente.

    Le paramètre **Température** contrôle le degré de créativité du modèle dans sa génération d’une réponse. Une valeur faible entraîne une réponse cohérente avec peu de variation aléatoire, tandis qu’une valeur élevée encourage le modèle à ajouter des éléments créatifs à son résultat , ce qui peut affecter la précision et le réalisme de la réponse.

## Déployer votre modèle sur une application web

Maintenant que vous avez exploré certaines des fonctionnalités d’un modèle d’IA générative dans le terrain de jeu Azure OpenAI Studio, vous pouvez déployer une application web Azure pour fournir une interface d’agent d’IA de base par le biais de laquelle les utilisateurs peuvent discuter avec le modèle.

1. En haut à droite de la page du terrain de jeu de **conversation**, dans le menu **Déployer sur**, sélectionnez **Une nouvelle application web**.
1. Dans la boîte de dialogue **Déployer sur une application web**, créez une application web avec les paramètres suivants :
    - **Nom** : *Nom unique*
    - **Abonnement** : *votre abonnement Azure*
    - **Groupe de ressources** : *groupe de ressources dans lequel vous avez provisionné votre ressource Azure OpenAI*
    - **Emplacements** : *région dans laquelle vous avez provisionné votre ressource Azure OpenAI*
    - **Plan tarifaire** : gratuit (F1) - *S’il n’est pas disponible, sélectionnez Basique (B1)*
    - **Activez l’historique des conversations dans l’application web** : <u>Dé</u>coché
    - **Je reconnais que les applications web entraîneront l’utilisation de mon compte** : sélectionné
1. Déployez la nouvelle application web et attendez que le déploiement se termine (ce qui peut prendre 10 minutes environ)
1. Une fois que votre application web a été déployée avec succès, utilisez le bouton en haut à droite de la page du terrain de jeu **Conversation** pour lancer l’application web. Le lancement de l’application peut prendre plusieurs minutes. Si vous y êtes invité, acceptez la demande d’autorisations.
1. Dans l’application web, entrez le message de conversation suivant :

    ```
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. Vérifiez la réponse.

    > **Remarque** : Vous avez déployé le *modèle* sur une application web, mais ce déploiement n’inclut pas les paramètres système que vous avez définis dans le terrain de jeu. Par conséquent, la réponse peut ne pas refléter les exemples que vous avez spécifiés dans le terrain de jeu. Dans un scénario réel, vous devez ajouter une logique à votre application pour modifier l’invite afin qu’elle inclue les données contextuelles appropriées pour les types de réponse que vous souhaitez générer. Ce type de personnalisation dépasse le cadre de cet exercice d’introduction, mais vous pouvez en savoir plus sur les techniques d’ingénierie rapides et les API Azure OpenAI dans d’autres exercices et documentations produits.

1. Une fois que vous avez terminé d’expérimenter votre modèle dans l’application web, fermez l’onglet de l’application web dans votre navigateur pour revenir à Azure OpenAI Studio.

## Nettoyage

Lorsque vous avez terminé avec votre ressource Azure OpenAI, n’oubliez pas de supprimer le déploiement ou la ressource entière dans le **Portail Azure** à `https://portal.azure.com`.
