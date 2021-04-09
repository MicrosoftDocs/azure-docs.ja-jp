---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 39414510bcb7a0197347ddc3c4d197bc57cb85fd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611162"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[許可される場所](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |このポリシーでは、リソースをデプロイするときに組織が指定できる場所を制限できます。 geo コンプライアンス要件を強制するために使用されます。 リソース グループ Microsoft.AzureActiveDirectory/b2cDirectories や、「グローバル」リージョンを使用するリソースを除外します。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |
|[リソース グループが許可される場所](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |このポリシーでは、組織がリソース グループを作成できる場所を制限できます。 geo コンプライアンス要件を強制するために使用されます。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |
|[許可されるリソースの種類](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |このポリシーでは、組織でデプロイできるリソースの種類を指定できるようになります。 このポリシーの影響を受けるのは、'tags' と 'location' をサポートするリソースの種類のみです。 すべてのリソースを制限するには、このポリシーを複製し、'mode' を 'All' に変更してください。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |
|[リソースの場所がリソース グループの場所と一致することの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |リソースの場所がそのリソース グループの場所と一致することを監査します |監査 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[カスタム サブスクリプションの所有者ロールが作成されていないこと](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |このポリシーでは、カスタム サブスクリプションの所有者ロールが存在しないことを確認します。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|[許可されないリソースの種類](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |環境にデプロイできるリソースの種類を制限します。 リソースの種類を制限することで、環境の複雑さと攻撃対象領域を削減しつつ、コストの管理にも役立てるとができます。 コンプライアンス対応の結果は、準拠していないリソースについてのみ表示されます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |
