---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b443728ed28a1ec288451fc7fc6fc11b496212b3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611258"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cognitive Search サービスでは、プライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search 用のサポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 Search サービスにプライベート エンドポイントをマッピングすると、データ漏えいのリスクが削減されます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search サービスではパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Azure Cognitive Search サービスが確実に露出されなくなるのでセキュリティが向上します。 プライベート エンドポイントを作成すると、Search サービスの露出を制限できます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[パブリック ネットワーク アクセスを無効にするよう Azure Cognitive Search サービスを構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9cee519f-d9c1-4fd9-9f79-24ec3449ed30) |Azure Cognitive Search サービスのパブリック ネットワーク アクセスを無効にして、パブリック インターネット経由でアクセスできないようにします。 これにより、データ漏えいのリスクを軽減することができます。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints) を参照してください。 |Modify、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PublicNetworkAccessDisabled_Modify.json) |
|[プライベート DNS ゾーンを使用するよう Azure Cognitive Search サービスを構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbc14a67-53e4-4932-abcc-2049c6706009) |プライベート DNS ゾーンを使用して、プライベート エンドポイントの DNS 解決をオーバーライドします。 プライベート DNS ゾーンをご自分の仮想ネットワークにリンクして、Azure Cognitive Search サービスに解決します。 詳細については、[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateDNSZone_DeployIfNotExists.json) |
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
