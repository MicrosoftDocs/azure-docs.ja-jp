---
title: Azure portal で Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure portal でリソースを作成し、サブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: d0b2c9c7450b8a44ca93a9d9fa8770b7ab3d9282
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743844"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Azure portal を使用して Cognitive Services リソースを作成する

このクイックスタートを使用して、Azure Cognitive Services の使用を開始します。 Azure portal で Cognitive Services リソースを作成すると、アプリケーションの認証を行うためのエンドポイントとキーが得られます。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>新しい Azure Cognitive Services リソースを作成する

1. リソースを作成します。

    #### <a name="multi-service-resourcetabmultiservice"></a>[マルチサービス リソース](#tab/multiservice)
    
    マルチサービス リソースには、ポータルで **Cognitive Services** という名前が付けられます。 [Cognitive Services リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。
    
    現在、マルチサービス リソースでは次のコグニティブ サービスにアクセスできます。
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Face               | Language Understanding (LUIS) | Text Analytics   |
    | Translator Text  | Bing Search v7 <br>(Web、画像、ニュース、ビデオ、ビジュアル) | Bing Custom Search | Bing Entity Search            | Bing Autosuggest |
    | Bing Spell Check |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[単一サービス リソース](#tab/singleservice)

    以下のリンクを使用して、利用可能なコグニティブ サービスのリソースを作成します。

    | 視覚                      | 音声                  | 言語                          | 決定             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Computer Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [イマーシブ リーダー](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing Custom Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entity Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing Spell Check](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Autosuggest](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. **[作成]** ページで、次の情報を指定します。

    #### <a name="multi-service-resourcetabmultiservice"></a>[マルチサービス リソース](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 例: *MyCognitiveServicesResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 Azure Cognitive Services を呼び出すときに必要になることがあるので、Azure の場所は記憶しておいてください。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    **Create** をクリックしてください。

    #### <a name="single-service-resourcetabsingleservice"></a>[単一サービス リソース](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 例: *TextAnalyticsResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 Azure Cognitive Services を呼び出すときに必要になることがあるので、Azure の場所は記憶しておいてください。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    **Create** をクリックしてください。

    ***

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

1. リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** をクリックします。

    ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 開かれたクイックスタート ウィンドウで、キーとエンドポイントにアクセスできます。

    ![キーとエンドポイントを取得する](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソースが含まれているリソース グループを見つけます
3. リソース グループの一覧を右クリックします。 **[リソース グループの削除]** を選択し、確認します。

## <a name="see-also"></a>関連項目

* [Azure Cognitive Services に対する要求の認証](authentication.md)
* [Azure Cognitive Services とは](Welcome.md)
* [自然言語のサポート](language-support.md)
* [Docker コンテナーのサポート](cognitive-services-container-support.md)
