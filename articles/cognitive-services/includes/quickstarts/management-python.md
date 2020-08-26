---
title: クイック スタート:Python 用 Azure 管理クライアント ライブラリ
description: このクイックスタートでは、Python 用の Azure 管理クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607637"
---
[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [パッケージ (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

アプリケーションが Azure アカウントと対話できるようにするには、アクセス許可を管理するための Azure サービス プリンシパルが必要です。 [Azure サービス プリンシパルの作成](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)に関するページの手順に従ってください。

サービス プリンシパルを作成すると、それにシークレット値、ID、およびアプリケーション ID が含まれていることがわかります。 後の手順のために、アプリケーション ID とシークレットを一時的な場所に保存します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループをアカウントに用意する必要があります。 リソース グループがまだ存在しない場合は、[Azure portal](https://ms.portal.azure.com/) で作成します。

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で新しい Python アプリケーションを作成し、コンソール ウィンドウでプロジェクトに移動します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のようにして、クライアント ライブラリをインストールできます。

```console
pip install azure-mgmt-cognitiveservices
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

### <a name="import-libraries"></a>ライブラリをインポートする

Python スクリプトを開き、以下のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>クライアントを認証する

スクリプトのルートに以下のフィールドを追加し、作成したサービス プリンシパルと Azure アカウント情報を使用して値を指定します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

次に、次のコードを追加して、**CognitiveServicesManagementClient** オブジェクトを構築します。 このオブジェクトは、すべての Azure 管理操作に必要です。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

### <a name="choose-a-service-and-pricing-tier"></a>サービスと価格レベルを選択する

新しいリソースを作成するときには、使用するサービスの "種類" と、必要な[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり SKU) を把握する必要があります。 リソースを作成するときに、この情報と他の情報をパラメーターとして使用します。 次の関数は、利用可能な Cognitive Service の "種類" を一覧表示します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

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

新しい Cognitive Services リソースを作成してサブスクライブするには、**Create** 関数を使用します。 この関数で、渡したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの "種類"、その価格レベル (つまり SKU)、および Azure の場所を把握している必要があります。 次の関数は、これらのすべての引数を受け取り、リソースを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>リソースを表示する

Azure アカウントのすべてのリソース (すべてのリソース グループにわたる) を表示するには、次の関数を使用します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>リソースの削除

次の関数は、特定のリソース グループから指定されたリソースを削除します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>管理関数を呼び出す

以下のコードをスクリプトの末尾に追加して、上の関数を呼び出します。 このコードは、使用可能なリソースの一覧表示、サンプル リソースの作成、所有しているリソースの一覧表示、およびサンプル リソースの削除を行います。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>アプリケーションの実行

コマンド ラインで `python` コマンドを使用して、アプリケーションを実行します。

```console
python <your-script-name>.py
```

## <a name="see-also"></a>関連項目

* [Azure Management SDK のリファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Azure Cognitive Services とは](../../Welcome.md)
* [Azure Cognitive Services に対する要求の認証](../../authentication.md)
* [Azure portal を使用して新しいリソースを作成する](../../cognitive-services-apis-create-account.md)