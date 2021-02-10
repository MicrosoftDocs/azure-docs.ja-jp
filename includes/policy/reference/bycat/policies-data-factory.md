---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a70bbf434084faca5c8648f8901ec77675ca5fe
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220465"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure データ ファクトリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |カスタマー マネージド キー (CMK) を使用して、Azure データ ファクトリの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすためには一般に、CMK が必要です。 CMK を使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 CMK 暗号化の詳細については、[https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
