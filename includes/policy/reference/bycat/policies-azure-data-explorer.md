---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c305cc356351019bb3c94ca80a873ff94b53c6ed
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610957"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer における保存時の暗号化でカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure Data Explorer クラスターでカスタマー マネージド キーを使用した保存時の暗号化を有効にすると、保存時の暗号化で使用されるキーをさらに制御できるようになります。 この機能は、多くの場合、特別なコンプライアンス要件を持つ顧客に適用でき、キーの管理に Key Vault を必要とします。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |ディスク暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |二重暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 二重暗号化が有効になっている場合、ストレージ アカウント内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回、インフラストラクチャ レベルで 1 回) 暗号化されます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure Data Explorer に対して仮想ネットワークの挿入を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |ネットワーク セキュリティ グループ規則を適用し、オンプレミスで接続し、サービス エンドポイントを使用してデータ接続ソースのセキュリティで保護できるようにする、仮想ネットワークの挿入によってネットワーク境界をセキュリティで保護します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
