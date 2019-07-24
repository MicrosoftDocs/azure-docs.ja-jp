---
title: Azure portal で Cognitive Services アカウントを作成する
titlesuffix: Azure Cognitive Services
description: Azure portal で Cognitive Services APIs アカウントを作成する方法。
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 6950cba5ac958233e7ea77c8dc783ca86cc5a386
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519903"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>クイック スタート:Azure portal で Cognitive Services アカウントを作成する

このクイック スタートでは、Azure Cognitive Services にサインアップし、単一サービスまたはマルチサービスのサブスクリプションを作成する方法を学習します。 これらのサービスは Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表され、Azure Cognitive Services APIs の中の 1 つまたは多くの API に接続できます。

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション。 無料で[アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Azure Cognitive Services リソースを作成してサブスクライブする

始める前に、Azure Cognitive Services サブスクリプションには 2 つの種類があることを知っておくことが重要です。 1 つ目は、Computer Vision や Speech Services などの単一サービスに対するサブスクリプションです。 単一サービスのサブスクリプションは、そのリソースに限定されます。 2 つ目は、Azure Cognitive Services のマルチサービスのサブスクリプションです。 このサブスクリプションでは、ほとんどの Azure Cognitive Services に対して単一のサブスクリプションを使用できます。 このオプションでは、請求も統合されます。 追加情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて**いません**。QnA Maker、Speech Services、Custom Vision、および Anomaly Detector。

以降のセクションでは、単一サービスまたはマルチサービスのサブスクリプションを作成する方法について説明します。


### <a name="multi-service-subscription"></a>マルチサービスのサブスクリプション

1. [Azure portal](https://portal.azure.com) にサインインし、**[+リソースの作成]** をクリックします。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 検索バーを見つけて、「**Cognitive Services**」と入力します。

    ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. **Cognitive Services** を選択します。

    ![Cognitive Services を選択する](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. **[作成]** ページで、次の情報を指定します。

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 わかりやすい名前を使用することをお勧します (*MyCognitiveServicesAccount* など)。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>単一サービスのサブスクリプション

1. [Azure portal](https://portal.azure.com) にサインインし、**[+リソースの作成]** をクリックします。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. [Azure Marketplace] で **[AI + 機械学習]** を選択します。 関心があるサービスが見つからない場合は、**[すべて]** をクリックして、Cognitive Services API のカタログ全体を表示します。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. **[作成]** ページで、次の情報を指定します。

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 わかりやすい名前を使用することをお勧します (*MyNameFaceAPIAccount* など)。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>リソースにアクセスする

> [!NOTE]
> サブスクリプションの所有者は、リソース グループとサブスクリプション用の Cognitive Services アカウントの作成を無効にすることができます。これを行うには、[Azure ポリシー](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)を適用し、[許可されていないリソースの種類] ポリシー定義を割り当て、ターゲットのリソースの種類として **Microsoft.CognitiveServices/accounts** を指定します。

リソースを作成した後、Azure ダッシュ ボードにピン留めした場合は、そこからアクセスできます。 それ以外の場合は、**[リソース グループ]** 内で検索できます。

Cognitive Services リソースの中で、**[概要]** セクションのエンドポイント URL とキーを使用して、アプリケーションでの API の呼び出しを開始できます。

![リソース画面](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Cognitive Services に対する要求の認証](authentication.md)

## <a name="see-also"></a>関連項目

* [クイック スタート: 画像から手書きのテキストを抽出する](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [チュートリアル: 画像の中にある顔を検出してフレームに収めるアプリを作成する](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [カスタム検索 Web ページを作成する](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Bot Framework を使用して Language Understanding (LUIS) とボットを統合する ](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
