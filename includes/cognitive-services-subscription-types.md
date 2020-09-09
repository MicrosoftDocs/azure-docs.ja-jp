---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: d5ed36700e2aea623fb259816e5baddff5215361
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "70381762"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
2 つの異なるリソースを使用して、Azure Cognitive Services にアクセスすることができます:マルチサービス リソース、または単一サービス リソース。

* マルチサービス リソース:
    * 1 つのキーとエンドポイントを使用して複数の Azure Cognitive Services にアクセスします。
    * ご利用の複数のサービスの課金を統合します。
* 単一サービス リソース:
    * 作成されたサービスごとに一意のキーとエンドポイントを使用して、単一の Azure Cognitive Service にアクセスします。 
    * Free レベルを使用してサービスをお試しください。   
