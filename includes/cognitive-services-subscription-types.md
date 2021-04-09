---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95554932"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
2 つの異なるリソースを使用して、Azure Cognitive Services にアクセスすることができます:マルチサービス リソース、または単一サービス リソース。

* マルチサービス リソース:
    * 1 つのキーとエンドポイントを使用して複数の Azure Cognitive Services にアクセスします。
    * ご利用の複数のサービスの課金を統合します。
* 単一サービス リソース:
    * 作成されたサービスごとに一意のキーとエンドポイントを使用して、単一の Azure Cognitive Service にアクセスします。 
    * Free レベルを使用してサービスをお試しください。