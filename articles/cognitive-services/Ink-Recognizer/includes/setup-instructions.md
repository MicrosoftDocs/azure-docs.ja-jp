---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303996"
---
>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) を使用して、Ink Recognizer 用のリソースを作成します。

リソースの作成後、[Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) でリソースを開き、 **[クイック スタート]** をクリックして、エンドポイントとキーを取得します。

以下の 2 つの[環境変数](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を作成します。

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 要求を認証するためのサブスクリプション キー。 

* `INK_RECOGNITION_ENDPOINT` - リソースのエンドポイント。 以下のようになります。 <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
