---
title: クイック スタート:.NET 用 Azure 管理クライアント ライブラリ
description: このクイックスタートでは、.NET 用の Azure 管理クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607627"
---
[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

アプリケーションが Azure アカウントと対話できるようにするには、アクセス許可を管理するための Azure サービス プリンシパルが必要です。 [Azure サービス プリンシパルの作成](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)に関するページの手順に従ってください。

サービス プリンシパルを作成すると、それにシークレット値、ID、およびアプリケーション ID が含まれていることがわかります。 後の手順のために、アプリケーション ID とシークレットを一時的な場所に保存します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループをアカウントに用意する必要があります。 リソース グループがまだ存在しない場合は、先に進む前に、[Azure portal](https://ms.portal.azure.com/) で作成します。

## <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

新しい .NET Core アプリケーションを作成します。 コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`azure-management-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

```console
dotnet new console -n azure-management-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

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

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Azure Management クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

### <a name="import-libraries"></a>ライブラリをインポートする

*program.cs* を開き、ファイルの先頭に次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>クライアントを認証する

*program.cs* のルートに以下のフィールドを追加し、作成したサービス プリンシパルと Azure アカウント情報を使用して値を指定します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

次に、**Main** メソッドでこれらの値を使用して、**CognitiveServicesManagementClient** オブジェクトを構築します。 このオブジェクトは、すべての Azure 管理操作に必要です。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>管理メソッドを呼び出す

**Main** メソッドに次のコードを追加して、使用可能なリソースの一覧表示、サンプル リソースの作成、所有しているリソースの一覧表示、およびサンプル リソースの削除を行います。 これらのメソッドは、以降の手順で定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

### <a name="choose-a-service-and-pricing-tier"></a>サービスと価格レベルを選択する

新しいリソースを作成するときには、使用するサービスの "種類" と、必要な[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり SKU) を把握する必要があります。 リソースを作成するときに、この情報と他の情報をパラメーターとして使用します。 使用できる Cognitive Service の "種類" の一覧を確認するには、スクリプトで次のメソッドを呼び出します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

SKU と価格の情報の一覧については、以下を参照してください。 

#### <a name="multi-service"></a>マルチサービス

| サービス                    | 種類                      |
|----------------------------|---------------------------|
| 複数のサービス。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/)に関するページを参照してください。            | `CognitiveServices`     |


#### <a name="vision"></a>視覚

| サービス                    | 種類                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Prediction | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

#### <a name="search"></a>検索

| サービス            | 種類                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing Custom Search | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing Spell Check   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>音声

| サービス            | 種類                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 音声認識 | `SpeakerRecognition` |

#### <a name="language"></a>言語

| サービス            | 種類                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Text Translation   | `TextTranslation`   |

#### <a name="decision"></a>決定

| サービス           | 種類               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効にされるサービス機能。
* 事前に定義された数のトランザクションのコスト。 この数を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。

> [!NOTE]
> Cognitive Services の多くには、サービスを試すために使用できる無料レベルがあります。 無料レベルを使用するには、リソースの SKU として `F0` を使用します。

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

新しい Cognitive Services リソースを作成してサブスクライブするには、**Create** メソッドを使用します。 このメソッドで、渡したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの "種類"、その価格レベル (つまり SKU)、および Azure の場所を把握している必要があります。 次のメソッドは、これらのすべてを引数として受け取り、リソースを作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>リソースを表示する

Azure アカウントのすべてのリソース (すべてのリソース グループにわたる) を表示するには、次のメソッドを使用します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>リソースの削除

次のメソッドは、特定のリソース グループから指定されたリソースを削除します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
```

## <a name="see-also"></a>関連項目

* [Azure Management SDK のリファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Azure Cognitive Services とは](../../Welcome.md)
* [Azure Cognitive Services に対する要求の認証](../../authentication.md)
* [Azure portal を使用して新しいリソースを作成する](../../cognitive-services-apis-create-account.md)