---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461516"
---
## <a name="azure-cognitive-service-subscription-types"></a>Azure Cognitive Services サブスクリプションの種類

> [!NOTE]
> サブスクリプションの所有者は、リソース グループとサブスクリプション用の Cognitive Services アカウントの作成を無効にすることができます。これを行うには、[Azure ポリシー](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)を適用し、[許可されていないリソースの種類] ポリシー定義を割り当て、ターゲットのリソースの種類として **Microsoft.CognitiveServices/accounts** を指定します。

2 つの異なるサブスクリプションを使用して、Azure Cognitive Services にアクセスすることができます:マルチサービス サブスクリプション、または単一サービス サブスクリプション。 これらのサブスクリプションでは、一度に 1 つのサービスまたは複数のサービスに接続できます。

### <a name="multi-service-subscription"></a>マルチサービスのサブスクリプション

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて**いません**。QnA Maker、Speech Services、Custom Vision、および Anomaly Detector。

Azure Cognitive Services のマルチサービス サブスクリプションでは、ほとんどの Azure Cognitive Services で単一サブスクリプションと Azure リソースを使用でき、利用するサービスからの課金を統合することができます。 追加情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

### <a name="single-service-subscription"></a>単一サービスのサブスクリプション

Computer Vision や Speech Services などの単一サービスに対するサブスクリプション。 単一サービスのサブスクリプションは、そのリソースに限定されます。 
