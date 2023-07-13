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
    - **Groupe de ressources** : Créez un nouveau groupe de ressources en lui attribuant le nom de votre choix.
    - **Région** : Choisissez une région disponible.
    - **Nom** : Un nom unique de votre choix.
    - **Niveau tarifaire** : Standard S0
3. Attendez la fin du déploiement. Accédez ensuite à la ressource Azure OpenAI déployée dans le portail Azure.

## Déployer un modèle

Azure OpenAI fournit un portail web appelé **Azure OpenAI Studio**, que vous pouvez utiliser pour déployer, gérer et explorer des modèles. Vous allez commencer votre exploration d’Azure OpenAI en utilisant Azure OpenAI Studio pour déployer un modèle.

1. Dans la page **Vue d’ensemble** de votre ressource Azure OpenAI, utilisez le bouton **Explorer** pour ouvrir Azure OpenAI Studio sous un nouvel onglet du navigateur.
2. Dans Azure OpenAI Studio, créez un déploiement avec les paramètres suivants :
    - **Nom du modèle** : text-davinci-003
    - **Nom du déploiement** : text-davinci

> **Remarque** : Azure OpenAI comprend plusieurs modèles, chacun optimisé pour un équilibre différent entre les fonctionnalités et les performances. Dans cet exercice, vous allez commencer par le modèle **Davinci** de la famille **GPT-3** de modèles de génération de texte. **text-davinci-003** est un bon modèle général pour résumer et générer du langage naturel. Pour plus d’informations sur les modèles disponibles dans Azure OpenAI, consultez [Modèles](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/models) dans la documentation Azure OpenAI.

## Explorer un modèle dans le terrain de jeu Complétions

Les *terrains de jeu* sont des interfaces utiles dans Azure OpenAI Studio que vous pouvez utiliser pour expérimenter vos modèles déployés sans avoir à développer votre propre application cliente.

1. Dans Azure OpenAI Studio, dans le volet gauche sous **Terrain de jeu**, sélectionnez **Complétions**.
2. Dans la page **Complétions**, vérifiez que votre déploiement **text-davinci** est sélectionné, puis dans la liste **Exemples**, sélectionnez **Résumer un article (abstrait)** .

    L’exemple de texte de synthèse se compose d’un *prompt* qui fournit du texte commençant par la ligne **Fournir un résumé du texte ci-dessous...** . Le fait de commencer le prompt avec cette phrase indique au modèle de résumer le bloc de texte suivant.

3. En bas de la page, notez le nombre de *tokens* détectés dans le texte. Les tokens sont les unités de base d’un prompt : essentiellement des mots ou des parties de mots dans le texte.
4. Utilisez le bouton **Générer** pour soumettre le prompt au modèle et récupérer une réponse.

    La réponse se compose d’un résumé du texte original. Le résumé doit communiquer les points clés du texte original avec moins de détails.

5. Utilisez le bouton **Regénérer** pour resoumettre le prompt et notez que la réponse peut varier de la réponse d’origine. Un modèle d’IA générative peut produire un nouveau texte chaque fois qu’il est appelé.
6. Sous la réponse résumée, ajoutez une nouvelle ligne et entrez le texte suivant :

    *Quels progrès l’IA a-t-elle réalisés ?*

7. Utilisez le bouton **Générer** pour soumettre le nouveau prompt et passer en revue la réponse. Le prompt et la réponse précédents fournissent un contexte dans un dialogue continu avec le modèle, ce qui permet au modèle de générer une réponse appropriée à votre question.
8. Remplacez la totalité du contenu du prompt par le texte suivant :

    *Fournir un résumé du texte ci-dessous qui capture son idée principale.* 
    
    *Azure OpenAI Service fournit à l’API REST un accès aux puissants modèles de langage d’OpenAI, y compris les séries de modèles GPT-4, Codex et Embeddings. Ces modèles peuvent être facilement adaptés à votre tâche spécifique, notamment la génération de contenu, la synthèse, la recherche sémantique et la traduction de langage naturel en code. Les utilisateurs peuvent accéder au service via les API REST, le SDK Python ou notre interface web dans Azure OpenAI Studio.*

9. Utilisez le bouton **Générer** pour soumettre le nouveau prompt et vérifier que le modèle résume correctement le texte.

## Utiliser un modèle pour classifier du texte

Jusqu’à présent, vous avez vu comment utiliser un modèle pour résumer du texte. Toutefois, les modèles génératifs dans Azure OpenAI peuvent prendre en charge différents types de tâches. Explorons un autre exemple : la *classification de texte*.

1. Dans la page **Complétions**, vérifiez que votre déploiement **text-davinci** est sélectionné, puis dans la liste **Exemples**, sélectionnez **Classifier le texte**.

    L’exemple de prompt de classification de texte décrit le contexte du modèle sous la forme d’une instruction permettant de classifier un article de presse dans l’une des catégories. Il fournit ensuite le texte de l’article de presse (préfixé par *Article de presse :* ) et se termine par *Catégorie classifiée :* . L’intention est que le modèle « complète » la dernière ligne du prompt en prédisant la catégorie appropriée.

2. Utilisez le bouton **Générer** pour soumettre le prompt au modèle et récupérer une réponse. Le modèle doit prédire une catégorie appropriée pour l’article de presse.
3. Sous la catégorie prédite, ajoutez le texte suivant :

    *article de presse : Microsoft sort Azure OpenAI Service. Microsoft Corporation publie un service Azure qui met les modèles OpenAI à disposition des développeurs d’applications qui créent des applications et des services dans le cloud Azure.*

    *Catégorie classifiée :*

4. Utilisez le bouton **Générer** pour continuer le dialogue avec le modèle et générer une catégorisation appropriée pour le nouvel article de presse.

## Explorer les prompts et les paramètres

Jusqu’à présent, vous avez basé vos prompts sur des exemples fournis dans Azure OpenAI Studio. Essayons quelque chose de différent.

1. Remplacez tout le texte dans la zone du prompt par le texte suivant :

    *Vous êtes enseignant et vous créez un contrôle d’évaluation pour vos élèves.*

    *Écrire trois questions à choix multiples en fonction du texte suivant.*

    *La plupart des solutions de vision par ordinateur sont basées sur des modèles Machine Learning qui peuvent être appliqués à l’entrée visuelle des caméras, des vidéos ou des images.*

    *\- La classification d’images implique l’entraînement d’un modèle Machine Learning pour classifier les images en fonction de leur contenu. Par exemple, dans une solution de surveillance de la circulation, vous pouvez utiliser un modèle de classification d’images pour classifier les images en fonction du type de véhicule qu’elles contiennent, comme les taxis, les bus, les cyclistes, etc.*

    *\- Les modèles Machine Learning de détection d’objets sont entraînés pour classifier des objets individuels au sein d’une image et identifier leur emplacement avec un encadré. Par exemple, une solution de surveillance de la circulation peut utiliser la détection d’objets pour identifier l’emplacement de différentes classes de véhicules.*

    *\- La segmentation sémantique est une technique de Machine Learning avancée où chaque pixel de l’image est classifié en fonction de l’objet auquel il appartient. Par exemple, une solution de surveillance de la circulation peut superposer des images de circulation avec des couches « masques » pour mettre en évidence différents véhicules à l’aide de couleurs spécifiques.*

2. Dans le volet **Paramètres**, définissez les valeurs de paramètre suivantes :
    - **Température** : 0
    - **Longueur maximale (tokens)**  : 500
    - **Texte de pré-réponse** : questions générées automatiquement. Valider avant utilisation dans un contrôle d’évaluation :
3. Utilisez le bouton **Générer** pour soumettre le prompt et passer en revue les résultats, qui doivent se composer de la valeur dans le paramètre de *texte de pré-réponse*, suivie de questions à choix multiples qu’un enseignant peut utiliser pour évaluer les élèves sur les sujets de vision par ordinateur du prompt. La totalité de la réponse doit être inférieure à la longueur maximale que vous avez spécifiée en tant que paramètre.

    Observez ce qui suit concernant le prompt et les paramètres que vous avez utilisés :

    - Le prompt contient des informations de contexte en langage naturel qui indiquent au modèle comment se comporter. Plus précisément, il indique que le modèle doit assumer le rôle d’un enseignant qui crée un contrôle d’évaluation pour les élèves.
    - Les paramètres comprennent *Température*, qui contrôle le degré d’aléatoire inclus dans la génération de réponse. La valeur **0** utilisée dans votre soumission réduit le caractère aléatoire au minimum, ce qui donne des réponses stables et prévisibles.

4. Utilisez le bouton **Regénérer** pour regénérer la réponse. Elle doit être similaire à la réponse précédente.
5. Remplacez la valeur du paramètre **Température** par **0,9**, puis utilisez le bouton **Regénérer** pour regénérer la réponse. Cette fois, l’augmentation du degré d’aléatoire devrait entraîner une réponse différente. Toutefois, les questions sont plus susceptibles de contenir des inexactitudes que celles de la réponse générée précédemment.

## Explorer la génération de code

Le modèle **text-davinci** que vous avez déployé est un bon modèle général qui peut gérer la plupart des tâches. Toutefois, dans certains cas, il est préférable de choisir un modèle optimisé pour un type de tâche spécifique. Par exemple, les modèles Azure OpenAI peuvent être utilisés pour générer du code informatique plutôt que du texte en langage naturel. Certains modèles sont d’ailleurs optimisés pour cette tâche.

1. Dans Azure OpenAI Studio, affichez la page **Modèles** qui contient la liste de tous les modèles disponibles dans votre ressource Azure OpenAI Service.
2. Sélectionnez le modèle **code-davinci-002** et utilisez le bouton **Déployer le modèle** pour le déployer avec le nom de déploiement **code-davinci**.
3. Une fois le déploiement terminé, dans Azure OpenAI Studio, consultez la page **Déploiements** qui contient la liste des modèles que vous avez déployés.
4. Sélectionnez le déploiement du modèle **code-davinci** et utilisez le bouton **Ouvrir dans le terrain de jeu** pour l’ouvrir dans le terrain de jeu.
5. Dans la page **Complétions**, vérifiez que votre déploiement **code-davinci** est sélectionné, puis dans la liste **Exemples**, sélectionnez **Langage naturel en SQL**.

    L’exemple de prompt de traduction de langage naturel en SQL fournit les détails des tables d’une base de données, ainsi qu’une description de la requête requise, suivie du mot clé `SELECT`. L’intention est que le modèle complète l’instruction `SELECT` pour créer une requête répondant à ce qui est demandé.

6. Utilisez le bouton **Générer** pour soumettre le prompt au modèle et récupérer une réponse qui se compose d’une requête `SELECT` SQL.
7. Remplacez l’intégralité du prompt et de la réponse par le nouveau prompt suivant :

    *# Python 3*

    *# Créer une fonction pour imprimer « Hello » et une chaîne spécifiée*

    *def print_hello(s):*

8. Utilisez le bouton **Générer** pour soumettre le prompt et afficher le code qui est généré. Le prompt comprenait une indication du langage de programmation à générer (Python 3), un commentaire décrivant la fonctionnalité souhaitée et la première partie de la définition de la fonction. Le modèle **code-davinci** doit avoir complété la fonction avec le code Python approprié.

## Explorer les modèles de conversation

ChatGPT est un chatbot développé par OpenAI qui peut fournir une grande variété de réponses en langage naturel dans un scénario conversationnel. Le modèle utilisé par ChatGPT et les API pour son utilisation sont inclus dans Azure OpenAI.

1. Dans Azure OpenAI Studio, affichez la page **Modèles** qui contient la liste de tous les modèles disponibles dans votre ressource Azure OpenAI Service.
2. Sélectionnez le modèle **gpt-35-turbo** et utilisez le bouton **Déployer le modèle** pour le déployer avec le nom de déploiement **gpt-chat**.
3. Une fois le modèle déployé, dans la section **Terrain de jeu**, sélectionnez la page **Conversation** et vérifiez que le modèle **gpt-chat** est sélectionné dans le volet de droite.
4. Dans la section **Configuration de l’Assistant**, dans la zone **Message système**, remplacez le texte actuel par ce qui suit :

    *Le système est un enseignant d’IA qui aide les gens à apprendre l’IA*

5. Sous la zone **Message système**, cliquez sur **Ajouter des exemples en peu de coups**, puis entrez le message et la réponse suivants dans les zones désignées :

    Utilisateur : *Quels sont les différents types d’intelligence artificielle ?*

    Assistant : *Il existe trois principaux types d’intelligence artificielle : l’IA étroite ou faible (par exemple, les assistants virtuels comme Siri ou Alexa, les logiciels de reconnaissance d’images et les filtres de courrier indésirable), l’IA générale ou forte (IA conçue pour être aussi intelligente qu’un être humain). Ce type d’IA n’existe pas actuellement et est purement théorique), et la superintelligence artificielle (IA qui est plus intelligente que tout être humain et qui peut effectuer des tâches qui dépassent la compréhension humaine. Ce type d’IA est également purement théorique et n’a pas encore été développé).*

    > **Remarque** : Des exemples peu nombreux (« few-shot examples ») sont utilisés pour fournir au modèle des exemples de types de réponses attendues. Le modèle tente de refléter le ton et le style des exemples dans ses propres réponses.

6. Enregistrez les modifications pour démarrer une nouvelle session et définir le contexte comportemental du système de conversation.

7. Dans la zone de requête en bas de la page, entrez le texte suivant :

    *Qu’est-ce que l’intelligence artificielle ?*

8. Utilisez le bouton **Envoyer** pour envoyer le message et afficher la réponse.

    > **Remarque** : Vous pouvez recevoir une réponse indiquant que le déploiement de l’API n’est pas encore prêt. Si tel est le cas, patientez quelques minutes et réessayez.

9. Examinez la réponse, puis envoyez le message suivant pour poursuivre la conversation :

    *Quel rapport a-t-elle avec le machine learning ?*

10. Examinez la réponse et notez que le contexte de l’interaction précédente a été retenu (de sorte que le modèle comprend que « elle » fait référence à l’intelligence artificielle).

Dans cet exercice, vous avez appris à provisionner Azure OpenAI Service dans un abonnement Azure et à utiliser Azure OpenAI Studio pour déployer et explorer des modèles.

En tant que développeur, vous pouvez utiliser les API REST spécifiques au langage pour créer des applications et des services qui consomment des modèles Azure OpenAI, ce qui vous permet de tirer parti des modèles d’IA générative dans vos propres applications. Le codage sur Azure OpenAI est abordé dans d’autres exercices.
