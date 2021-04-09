---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b71ec6158b50e3e95bc858b2f300406804fdfd6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611066"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service エンドポイントは有効な HTTPS URI である必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |データは送信中に改ざんされる可能性があります。 誤用や改ざんの問題に対処する暗号化を提供するプロトコルが存在します。 確実に暗号化されたチャンネルでのみボットが通信を行うように、エンドポイントを有効な HTTPS URI に設定してください。 そうすることで、HTTPS プロトコルを使用して転送中のデータが確実に暗号化されます。また、これは多くの場合、規制や業界標準に準拠するための要件でもあります。 [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) を参照してください。 |audit、deny、disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service は、カスタマー マネージド キーを使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service は、リソースを自動的に暗号化してデータを保護し、組織のセキュリティとコンプライアンスのコミットメントを満たします。 既定では、Microsoft マネージド暗号化キーが使用されます。 キーの管理やサブスクリプションへのアクセスの制御の柔軟性を高めるには、カスタマー マネージド キーを選択します。これは、Bring Your Own Key (BYOK) とも呼ばれます。 Azure Bot Service 暗号化の詳細については、[https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) を参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
