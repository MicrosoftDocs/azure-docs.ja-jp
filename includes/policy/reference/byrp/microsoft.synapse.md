---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c6e767a1c6be08060865c43614c90aa7469d1b2
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092428"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Synapse ワークスペースでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |カスタマー マネージド キーは、Azure Synapse ワークスペースに格納されているデータの保存時の暗号化を制御するために使用されます。 カスタマー マネージド キーを使用すると、サービス マネージド キーによる既定の暗号化の上に 2 番目の暗号化レイヤーが追加されて、二重の暗号化が提供されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Azure Synapse ワークスペースの IP ファイアウォール規則を削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |IP ファイアウォール規則をすべて削除すると、Azure Synapse ワークスペースへのアクセス手段がプライベート エンドポイントに限定され、セキュリティが向上します。 ワークスペースのパブリック ネットワーク アクセスを許可するファイアウォール規則の作成が、この構成によって監査されます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Azure Synapse ワークスペースのマネージド ワークスペース仮想ネットワークを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |マネージド ワークスペース仮想ネットワークを有効にすると、そのワークスペースが他のワークスペースから分離されたネットワークであることが保証されます。 また、この仮想ネットワークにデプロイされるデータ統合と Spark リソースによって、Spark のアクティビティに関してユーザー レベルの分離性が確保されます。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Azure Synapse ワークスペースのプライベート エンドポイント接続を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |プライベート エンドポイントを構成すると、Azure Synapse ワークスペースに非公開で接続することができます。 これは、Azure Synapse ワークスペースにセキュリティで保護された通信チャネルを適用するために使用されます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Synapse マネージド プライベート エンドポイントは、承認された Azure Active Directory テナント内のリソースにのみ接続する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |承認された Azure Active Directory (Azure AD) テナント内のリソースへの接続のみを許可することによって、Synapse ワークスペースを保護します。 承認された Azure AD テナントは、ポリシーの割り当て中に定義できます。 |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
|[Synapse ワークスペースで脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0049a6b3-a662-4f3e-8635-39cf44ace45a) |Synapse ワークスペースで定期的な SQL 脆弱性評価スキャンを構成することで、潜在的な脆弱性を検出、追跡、修復します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/ASC_SQLVulnerabilityAssessmentOnSynapse_Audit.json) |
