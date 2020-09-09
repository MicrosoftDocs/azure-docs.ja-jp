---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bb257e1b8bd4cae328d8de0723ef88bb76c1db27
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487746"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid domains should use private links (Azure Event Grid ドメインではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid ドメインを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid topics should use private links (Azure Event Grid トピックではプライベート リンクを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |1 つ以上の承認されたプライベート エンドポイント接続がない Azure Event Grid トピックを監査します。 仮想ネットワーク内のクライアントは、プライベート リンク経由でプライベート エンドポイント接続があるリソースに安全にアクセスできます。 詳細については、[https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) を参照してください。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
