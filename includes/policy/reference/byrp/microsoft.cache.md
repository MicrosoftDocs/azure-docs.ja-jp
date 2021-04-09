---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: abd6de6c935af58e30f09b2f2091b45e61f6d3de
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608880"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache for Redis でパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F470baccb-7e51-4549-8b1a-3e5be069f663) |パブリック ネットワーク アクセスを無効にすると、パブリック インターネットに Azure Cache for Redis が露出されないのでセキュリティが向上します。 代わりにプライベート エンドポイントを作成することによって Azure Cache for Redis の露出を制限することができます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PublicNetworkAccess_AuditDeny.json) |
|[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network のデプロイでは、Azure Cache for Redis のためにセキュリティと分離が強化され、アクセスをさらに制限するためのサブネットやアクセス制御ポリシーなどの機能が提供されます。Azure Cache for Redis インスタンスが仮想ネットワークで構成されている場合、パブリック アドレスの指定ができず、仮想ネットワーク内の仮想マシンとアプリケーションからのみアクセスできます。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Azure Cache for Redis インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが削減されます。 詳細については、[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[パブリック ネットワーク アクセスを無効にするように Azure Cache for Redis を構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30b3dfa5-a70d-4c8e-bed6-0083858f663d) |Azure Cache for Redis リソースのパブリック ネットワーク アクセスを無効にして、パブリック インターネット経由でアクセスできないようにします。 これは、データ漏えいのリスクからキャッシュを保護するのに役立ちます。 |Modify、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PublicNetworkAccess_Modify.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
