---
title: クイック スタート:Python 用 Language Understanding (LUIS) 作成クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: Python 用 Language Understanding (LUIS) クライアント ライブラリの使い方を説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  Language Understanding (LUIS) を使うと、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: f5756c479d47e905e0110c9919cf4b5be4e75099
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932129"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>クイック スタート:Python 用 Language Understanding (LUIS) 作成クライアント ライブラリ

Python 用 Language Understanding (LUIS) 作成クライアント ライブラリの使い方を説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  Language Understanding (LUIS) を使うと、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。 

Python 用 Language Understanding (LUIS) 作成クライアント ライブラリの用途は次のとおりです。

* アプリを作成する。
* 意図、エンティティ、発話の例を追加する。
* 語句リストなどの機能を追加する。
* アプリをトレーニングして公開する。

[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [作成パッケージ (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>前提条件

* Language Understanding (LUIS) ポータル アカウント: [無料で作成できます](https://www.luis.ai)。
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-authoring-key"></a>Language Understanding (LUIS) 作成キーを取得する

[作成キー](luis-how-to-account-settings.md)を取得し、そのキーについて `LUIS_AUTHORING_KEY` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)し、キーのリージョンの環境変数 (`LUIS_REGION`) を作成します。

### <a name="install-the-python-library-for-luis"></a>LUIS 用 Python ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に Python 用 Language Understanding (LUIS) 作成クライアント ライブラリをインストールします。

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* アプリ - [作成](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)、[削除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-)、[公開](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* 発話の例 - [バッチによる追加](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)、[ID による削除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* 機能 - [語句リスト](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)の管理 
* モデル - [意図](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)とエンティティの管理
* パターン - [パターン](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)の管理
* トレーニング - アプリの[トレーニング](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-)と[トレーニングの状態](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)のポーリング
* [バージョン](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - クローンによる管理、エクスポート、削除


## <a name="code-examples"></a>コード例

以下のコード スニペットは、Python 用 Language Understanding (LUIS) 作成クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [アプリの作成](#create-a-luis-app)
* [エンティティの追加](#create-entities-for-the-app)
* [意図の追加](#create-intent-for-the-app)
* [発話の例を追加する](#add-example-utterance-to-intent)
* [アプリのトレーニング](#train-the-app)
* [アプリの公開](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で、新しい Python アプリケーションを作成します。 次に、次のライブラリをインポートします。 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>クライアントを認証する

自分のキーを指定して [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) オブジェクトを作成します。

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>LUIS アプリの作成

1. 意図、エンティティ、発話の例を保持した自然言語処理 (NLP) モデルが含まれる LUIS アプリを作成します。 

1. [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) オブジェクトの [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) メソッドを作成してアプリを作成します。 名前と言語のカルチャは必須のプロパティです。 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>アプリの意図の作成
LUIS アプリのモデルの中で最も重要なオブジェクトが、意図です。 意図は、ユーザーが発話において "_意図したこと_" をグループ分けしたものに対応しています。 ユーザーが質問をしたり、言葉を発したりするのは、ボット (またはその他のクライアント アプリケーション) から "_自らが意図した_" 回答を得るためです。 意図の例は、フライトの予約、目的地の天気の確認、カスタマー サービスの連絡先情報の取得などが挙げられます。   

意図の一意の名前と共に [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) メソッドを使用し、アプリ ID とバージョン ID、新しい意図の名前を渡します。 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>アプリのエンティティの作成

必須ではないものの、ほとんどのアプリで見られるのがエンティティです。 エンティティは、ユーザーの発話からユーザーが意図した成果を達成するために必要な情報を抽出します。 [事前構築済み](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-)エンティティとカスタム エンティティにはいくつかの種類があり、それぞれが専用のデータ変換オブジェクト (DTO) モデルを備えています。  事前構築済みエンティティのうち、アプリに追加することが多いものとしては、[number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[geographyV2](luis-reference-prebuilt-geographyv2.md)、[ordinal](luis-reference-prebuilt-ordinal.md) があります。 

この **add_entities** メソッドにより、2 つのロールが設定された `Location` シンプル エンティティ、`Class` シンプル エンティティ、`Flight` 複合エンティティが作成されるほか、いくつかの事前構築済みエンティティが追加されます。

重要なのは、エンティティには 1 つの意図だけがマークされるわけではないという点です。 これらは多くの意図に適用することができますし、多くの意図に適用されるのが普通です。 特定の意図が 1 つだけマークされるのは、ユーザーの発話の例のみです。

エンティティ作成のためのメソッドは、[ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) クラスに含まれています。 エンティティは種類ごとに固有のデータ変換オブジェクト (DTO) モデルを備えています。 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>意図に対する発話の例の追加

アプリでは、発話の意図を特定し、エンティティを抽出するために、発話の例が必要になります。 例では、特定の 1 つの意図をターゲットとし、すべてのカスタム エンティティをマークする必要があります。 事前構築済みエンティティをマークする必要はありません。 

[ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) オブジェクトの一覧 (発話の例 1 件につき 1 つのオブジェクト) を作成し、発話の例を追加します。 それぞれの例では、エンティティの名前とエンティティの値から成る名前と値のペアのディクショナリを使用し、エンティティをすべてマークする必要があります。 エンティティの値は、発話の例のテキストに表示されているものと正確に一致している必要があります。 

アプリ ID、バージョン ID、例の一覧を指定して [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) を呼び出します。 この呼び出しにより、結果のリストが返されます。 モデルに対して例が正常に追加されたかどうかを確かめるには、それぞれの例の結果を確認する必要があります。 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>アプリをトレーニングする

モデルの作成を終えたら、このバージョンのモデルに合わせて LUIS アプリをトレーニングする必要があります。 トレーニングを済ませたモデルは、[コンテナー](luis-container-howto.md)で使用したり、ステージング スロットまたは製品スロットに[公開](luis-how-to-publish-app.md)したりできます。 

[train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) メソッドでは、アプリ ID とバージョン ID が必要です。 

このクイックスタートで示したような非常に小さなモデルであれば、トレーニングが短時間で完了します。 運用レベルのアプリケーションの場合には、アプリのトレーニングに、トレーニングが成功したかどうかと、そのタイミングを確認するための [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) メソッドに対するポーリング呼び出しが含まれます。 応答は、オブジェクトごとの状態を格納した [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) オブジェクトの一覧です。 トレーニングが完了したと判定されるには、オブジェクトがすべて成功している必要があります。

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding アプリの公開

[app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) メソッドを使用して、LUIS アプリを公開します。 これにより、トレーニング済みの最新バージョンがエンドポイントの指定されたスロットに公開されます。 クライアント アプリケーションではこのエンドポイントを使用して、意図の予測とエンティティの抽出の対象となるユーザー発話を送信します。

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[ユーザーの意図を特定する LUIS アプリを構築する](luis-quickstart-intents-only.md)

* [Language Understanding (LUIS) API とは](what-is-luis.md)
* [新機能](whats-new.md)
* [意図](luis-concept-intent.md)、[エンティティ](luis-concept-entity-types.md)、[発話の例](luis-concept-utterance.md)、[事前構築済みエンティティ](luis-reference-prebuilt-entities.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py) にあります。
