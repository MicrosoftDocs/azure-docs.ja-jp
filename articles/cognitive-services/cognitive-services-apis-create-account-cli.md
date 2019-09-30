---
title: Azure CLI を使用して Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure コマンド ライン インターフェイスを使用してリソースを作成し、それをサブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: aahi
ms.openlocfilehash: cd00f49aea08e5c94a9206b64f66f4424ef3ca04
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057647"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Azure コマンド ライン インターフェイス (CLI) を使用して Cognitive Services リソースを作成する

このクイックスタートでは、[Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用した Azure Cognitive Services の基本操作について説明します。 Cognitive Services は、ご利用の Azure サブスクリプションに作成した Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表されます。 リソースの作成後、自動的に生成されたキーとエンドポイントを使用して、自分のアプリケーションの認証を行います。 


このクイックスタートでは、[Azure コマンドライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、Azure Cognitive Services にサインアップし、単一サービスまたはマルチサービスを持つアカウントを作成する方法を学習します。 これらのサービスは Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表され、Azure Cognitive Services APIs の中の 1 つ以上の API に接続できます。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - 無料[アカウントを作成](https://azure.microsoft.com/free/)します。
* [Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI をインストールしてサインインする 

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。 CLI のローカル インストールにサインインするには、[az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) コマンドを実行します。

```console
az login
```

また、緑色の **[使ってみる]** ボタンを使用して、お使いのブラウザーでこれらのコマンドを実行することもできます。
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>新しい Azure Cognitive Services リソース グループを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループを用意する必要があります。 新しいリソースを作成するときに、新しいリソース グループを作成するか、既存のものを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

### <a name="choose-your-resource-group-location"></a>リソース グループの場所を選択する

リソースを作成するには、お使いのサブスクリプションに使用できるいずれかの Azure の場所が必要です。 使用できる場所の一覧を取得するには、[az account list-locations](/cli/azure/account#az-account-list-locations) コマンドを使用します。 ほとんどの Cognitive Services は複数の場所からアクセスできます。 お客様に最も近いものを選択するか、サービスに使用できる場所を確認します。

> [!IMPORTANT]
> * Azure Cognitive Services を呼び出すときに必要になるので、Azure の場所は記憶しておいてください。
> * 一部の Cognitive Services を使用できるかどうかは、リージョンによって異なります。 詳細については、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)」を参照してください。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure の場所が決まったら、Azure CLI で [az group create](/cli/azure/group#az-group-create) コマンドを使用して新しいリソース グループを作成します。

次の例では、Azure の場所 `westus2` を、サブスクリプションで使用できる Azure の場所の 1 つに置き換えます。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Cognitive サービスと価格レベルを選択する

新しいリソースを作成するときには、必要な [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり sku) と共に、使用するサービスの "種類" を把握する必要があります。 リソースを作成するときは、この情報と他の情報をパラメーターとして使用します。

> [!NOTE]
> 多くの Cognitive サービスには、サービスを試すために使用できる無料レベルがあります。 無料レベルを使用するには、リソースの sku として `F0` を使用します。

### <a name="vision"></a>視覚

| サービス                    | 種類                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Prediction | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face API                   | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Search

| サービス            | 種類                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing Custom Search | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing Spell Check   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>音声

| サービス            | 種類                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 音声認識 | `SpeakerRecognition` |

### <a name="language"></a>言語

| サービス            | 種類                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Text Translation   | `TextTranslation`   |

### <a name="decision"></a>決定

| サービス           | 種類               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

使用できる Cognitive Services の "種類" の一覧を確認するには、[az cognitiveservices account list-kinds](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) コマンドを使用します。

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>リソース グループに新しいリソースを追加する

新しい Cognitive Services リソースを作成してサブスクライブするには、[az cognitiveservices account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) コマンドを使用します。 このコマンドで、以前に作成したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの種類、その価格レベル (つまり sku)、および Azure の場所を把握している必要があります。

次のコマンドで、`anomaly-detector-resource` という名前の Anomaly Detector 用の F0 (無料) リソースを作成できます。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

コマンドライン インターフェイス (CLI) のローカル インストールにログインするには、[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) コマンドを使用します。

```console
az login
```

Cognitive Service リソースのキーを取得するには、[az cognitiveservices account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) コマンドを使用します。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効にされるサービス機能。
* 事前に定義されたトランザクションの量のコスト。 この量を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services リソースをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

リソース グループとそれに関連付けられているリソースを削除するには、az group delete コマンドを使用します。

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>関連項目

* [Azure Cognitive Services に対する要求の認証](authentication.md)
* [Azure Cognitive Services とは](Welcome.md)
* [自然言語のサポート](language-support.md)
* [Docker コンテナーのサポート](cognitive-services-container-support.md)
