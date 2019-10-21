---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996900"
---
>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) を使用して、Ink Recognizer 用のリソースを作成します。 

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手することもできます。 これとエンドポイントは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。

リソースの作成後、[Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) でリソースを開き、 **[クイック スタート]** をクリックして、エンドポイントとキーを取得します。

以下の 2 つの[環境変数](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を作成します。

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - リソースのエンドポイント。 以下のようになります。 <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` - 要求を認証するためのサブスクリプション キー。   
