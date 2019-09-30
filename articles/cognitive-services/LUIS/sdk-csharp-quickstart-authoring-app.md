---
title: クイック スタート:.NET 用 Language Understanding (LUIS) 作成クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: .NET 用 Language Understanding (LUIS) クライアント ライブラリの使い方を説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  Language Understanding (LUIS) を使うと、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: d7668f49df1caca6b4261424b0d2c025a640d572
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258776"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>クイック スタート:.NET 用 Language Understanding (LUIS) 作成クライアント ライブラリ

.NET 用 Language Understanding (LUIS) 作成クライアント ライブラリの使い方を説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  Language Understanding (LUIS) を使うと、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話に適用して、全体の意味を予測し、関連性のある詳細な情報を引き出すことができます。 

.NET 用 Language Understanding (LUIS) 作成クライアント ライブラリの用途は次のとおりです。

* アプリを作成する
* 意図、エンティティ、発話の例を追加する
* 語句リストなどの機能を追加する
* アプリをトレーニングして公開する

[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [作成パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>前提条件

* Language Understanding (LUIS) ポータル アカウント - [無料で作成できます](https://www.luis.ai)。
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-starter-key"></a>Language Understanding (LUIS) スターター キーを取得する

[スターター キー](luis-how-to-azure-subscription.md#starter-key)を取得し、そのキーについて `COGNITIVESERVICE_AUTHORING_KEY` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。 

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet `new` コマンドを使用し、`language-understanding-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. 新しく作成されたアプリ フォルダーにディレクトリを変更します。 

1. 次を使用してアプリケーションをビルドできます。

    ```console
    dotnet build
    ```

    ビルドの出力に警告やエラーが含まれないようにする必要があります。 
    
    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Language Understanding (LUIS) 作成クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。


## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* アプリ - [作成](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet)、[削除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet)、[公開](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* 発話の例 - [バッチによる追加](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet)、[ID による削除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* 機能 - [語句リスト](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)の管理 
* モデル - [意図](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)とエンティティの管理
* パターン - [パターン](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)の管理
* トレーニング - アプリの[トレーニング](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet)と[トレーニングの状態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)のポーリング
* [バージョン](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - クローンによる管理、エクスポート、削除


## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Language Understanding (LUIS) 作成クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [アプリの作成](#create-a-luis-app)
* [エンティティの追加](#create-entities-for-the-app)
* [意図の追加](#create-intent-for-the-app)
* [発話の例を追加する](#add-example-utterance-to-intent)
* [アプリのトレーニング](#train-the-app)
* [アプリの公開](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で **Program.cs** ファイルを開きます。 既存の `using` コードを次の `using` ディレクティブに置き換えます。

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>クライアントを認証する

1. `COGNITIVESERVICES_AUTHORING_KEY` という名前の環境変数から取得した作成キーを管理するための変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは後で作成します。

1. 作成リージョンとエンドポイントを保持する変数を作成します。 作成キーのリージョンは、作成を行う場所によって異なります。 [作成リージョンは次の 3 つです](luis-reference-regions.md)。

    * オーストラリア - `australiaeast`
    * ヨーロッパ - `westeurope`
    * 米国およびその他のリージョン - `westus` (既定)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. 自分のキーを指定して [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) オブジェクトを作成します。

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS アプリの作成

1. 意図、エンティティ、発話の例を保持した自然言語処理 (NLP) モデルが含まれる LUIS アプリを作成します。 

1. [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet) を作成します。 名前と言語のカルチャは必須のプロパティです。 

1. [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) メソッドを呼び出します。 応答はアプリ ID です。 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>アプリの意図の作成
LUIS アプリのモデルの中で最も重要なオブジェクトが、意図です。 意図は、ユーザーが発話において "_意図したこと_" をグループ分けしたものに対応しています。 ユーザーが質問をしたり、言葉を発したりするのは、ボット (またはその他のクライアント アプリケーション) から "_自らが意図した_" 回答を得るためです。 意図の例は、フライトの予約、目的地の天気の確認、カスタマー サービスの連絡先情報の取得などが挙げられます。   

一意の意図の名前を備えた [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) を作成し、[Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) メソッドにアプリ ID、バージョン ID、ModelCreateObject を渡します。 応答は意図 ID です。

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>アプリのエンティティの作成

必須ではないものの、ほとんどのアプリで見られるのがエンティティです。 エンティティは、ユーザーの発話からユーザーが意図した成果を達成するために必要な情報を抽出します。 [事前構築済み](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet)エンティティとカスタム エンティティにはいくつかの種類があり、それぞれが専用のデータ変換オブジェクト (DTO) モデルを備えています。  事前構築済みエンティティのうち、アプリに追加することが多いものとしては、[number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[geographyV2](luis-reference-prebuilt-geographyv2.md)、[ordinal](luis-reference-prebuilt-ordinal.md) があります。 

この **AddEntities** メソッドにより、2 つのロールが設定された `Location` シンプル エンティティ、`Class` シンプル エンティティ、`Flight` 複合エンティティが作成されるほか、いくつかの事前構築済みエンティティが追加されます。

重要なのは、エンティティには 1 つの意図だけがマークされるわけではないという点です。 これらは多くの意図に適用することができますし、多くの意図に適用されるのが普通です。 特定の意図が 1 つだけマークされるのは、ユーザーの発話の例のみです。

エンティティ作成のためのメソッドは、[Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) クラスに含まれています。 エンティティは種類ごとに固有のデータ変換オブジェクト (DTO) モデルを備えており、その [Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) 名前空間には通常 `model` という語が含まれています。 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>意図に対する発話の例の追加

アプリでは、発話の意図を特定し、エンティティを抽出するために、発話の例が必要になります。 例では、特定の 1 つの意図をターゲットとし、すべてのカスタム エンティティをマークする必要があります。 事前構築済みエンティティをマークする必要はありません。 

[ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) オブジェクトの一覧 (発話の例 1 件につき 1 つのオブジェクト) を作成し、発話の例を追加します。 それぞれの例では、エンティティの名前とエンティティの値から成る名前と値のペアのディクショナリを使用し、エンティティをすべてマークする必要があります。 エンティティの値は、発話の例のテキストに表示されているものと正確に一致している必要があります。 

アプリ ID、バージョン ID、例の一覧を指定して [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) を呼び出します。 この呼び出しにより、結果のリストが返されます。 モデルに対して例が正常に追加されたかどうかを確かめるには、それぞれの例の結果を確認する必要があります。 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
**CreateUtterance** と **CreateLabel** メソッドは、オブジェクトの作成に役立つユーティリティ メソッドです。

## <a name="train-the-app"></a>アプリをトレーニングする

モデルの作成を終えたら、このバージョンのモデルに合わせて LUIS アプリをトレーニングする必要があります。 トレーニングを済ませたモデルは、[コンテナー](luis-container-howto.md)で使用したり、ステージング スロットまたは製品スロットに[公開](luis-how-to-publish-app.md)したりできます。 

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) メソッドでは、アプリ ID とバージョン ID が必要です。 

このクイックスタートで示したような非常に小さなモデルであれば、トレーニングが短時間で完了します。 運用レベルのアプリケーションの場合には、アプリのトレーニングに、トレーニングが成功したかどうかと、そのタイミングを確認するための [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) メソッドに対するポーリング呼び出しが含まれます。 応答は、オブジェクトごとの状態を格納した [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) オブジェクトの一覧です。 トレーニングが完了したと判定されるには、オブジェクトがすべて成功している必要があります。

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding アプリの公開

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) メソッドを使用して、LUIS アプリを公開します。 これにより、トレーニング済みの最新バージョンがエンドポイントの指定されたスロットに公開されます。 クライアント アプリケーションではこのエンドポイントを使用して、意図の予測とエンティティの抽出の対象となるユーザー発話を送信します。

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから、dotnet `run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クリーンアップが必要な場合には、LUIS アプリを削除できます。 アプリを削除するには、[Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) メソッドを使用します。 このほか、[LUIS ポータル](https://www.luis.ai)からアプリを削除することもできます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[.Net SDK を使用して予測エンドポイントに対してクエリを実行する](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [Language Understanding (LUIS) API とは](what-is-luis.md)
* [新機能](whats-new.md)
* [意図](luis-concept-intent.md)、[エンティティ](luis-concept-entity-types.md)、[発話の例](luis-concept-utterance.md)、[事前構築済みエンティティ](luis-reference-prebuilt-entities.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs) にあります。
