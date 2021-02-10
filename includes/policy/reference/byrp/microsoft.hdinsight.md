---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 88945738b33cb6d1d7760eec1015a006132d3c3f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99179855"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight クラスターでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |カスタマー マネージド キー (CMK) を使用して、Azure HDInsight クラスターの保存時の暗号化を管理します。 既定では、顧客データはサービス マネージド キーを使用して暗号化されますが、規制コンプライアンス標準を満たすためには一般に、CMK が必要です。 CMK を使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 CMK 暗号化の詳細については、[https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) を参照してください。  |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight クラスターでは、ホストでの暗号化を使用して保存データを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |ホストでの暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight クラスターでは、転送中の暗号化を使用して、Azure HDInsight クラスター ノード間の通信を暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Azure HDInsight クラスター ノード間での転送中に、データが改ざんされる可能性があります。 転送中の暗号化を有効にすると、この転送中の悪用や改ざんの問題に対処できます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
