---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53bf01b66d6638dbcdd96a543b5a40ba53150845
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298616"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cognitive Services アカウントでデータ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2bdd0062-9d75-436e-89df-487dd8e4b3c7) |このポリシーでは、データ暗号化を使用していない Cognitive Services アカウントを監査します。 ストレージがある Cognitive Services アカウントについてはそれぞれ、カスタマー マネージドまたは Microsoft マネージドいずれかのキーによるデータ暗号化を有効にする必要があります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_Encryption_Audit.json) |
|[Cognitive Services アカウントでカスタマー マネージド キーによるデータ暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |このポリシーでは、カスタマー マネージド キーによるデータ暗号化を使用していない Cognitive Services アカウントを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[Cognitive Services アカウントでネットワーク アクセスを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみが Cognitive Services アカウントにアクセスできるように、ネットワーク ルールを構成します。 特定のインターネットまたはオンプレミスのクライアントからの接続を許可するため、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services アカウントで顧客所有のストレージを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46aa9b05-0e60-4eae-a88b-1e9d374fa515) |このポリシーでは、顧客所有のストレージを使用していない Cognitive Services アカウントを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_UserOwnedStorage_Audit.json) |
|[Cognitive Services アカウントで、顧客所有のストレージを使用するか、データ暗号化を有効にする必要がある。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11566b39-f7f7-4b82-ab06-68d8700eb0a4) |このポリシーでは、顧客所有のストレージもデータ暗号化も使用していない Cognitive Services アカウントを監査します。 ストレージがある Cognitive Services アカウントについてはそれぞれ、顧客所有のストレージを使用するか、データ暗号化を有効にします。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_BYOX_Audit.json) |
|[Cognitive Services アカウントでは公衆ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |このポリシーは、環境内で公衆ネットワーク アクセスが有効になっている Cognitive Services アカウントを監査します。 プライベート エンドポイントからの接続のみが許可されるように、公衆ネットワーク アクセスを無効にする必要があります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
