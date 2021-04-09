---
title: 修復できるポリシーをデプロイする
description: Azure Lighthouse を通して修復タスクを使用するポリシーをデプロイするには、顧客テナント内にマネージド ID を作成する必要があります。
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233884"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>委任されたサブスクリプション内で修復が可能なポリシーをデプロイする

[Azure Lighthouse](../overview.md) では、サービス プロバイダーが委任されたサブスクリプション内にポリシー定義を作成したり、その編集をしたりすることができます。 ただし、[修復タスク](../../governance/policy/how-to/remediate-resources.md)を使用しているポリシー (つまり、効果が [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) または [modify](../../governance/policy/concepts/effects.md#modify) のポリシー) をデプロイする場合には、顧客のテナントに[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を作成する必要があります。 このマネージド ID は、ポリシー内にテンプレートをデプロイする際に Azure Policy が使用するものです。 このシナリオを実現するにあたっては、顧客を Azure の委任されたリソース管理にオンボードする場合とポリシーそのものをデプロイする場合のどちらにも必要な手順があります。

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](../concepts/enterprise.md)も同じプロセスを使用できます。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>顧客のテナント内でマネージド ID にロールを割り当てることができるユーザーを作成する

Azure Lighthouse に顧客をオンボードするときには、[Azure Resource Manager テンプレート](onboard-customer.md#create-an-azure-resource-manager-template)とパラメーター ファイルを使用し、顧客テナント内の委任されたリソースにアクセスを付与する承認ワークフローを定義します。 承認ごとに、管理テナントの Azure AD ユーザー、グループ、サービス プリンシパルに対応する **principalId** と、付与される [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)に対応する **roleDefinitionId** が指定されます。

ある **principalId** に顧客テナント内でのマネージド ID の作成を許可するには、**roleDefinitionId** を **ユーザー アクセス管理者** に設定する必要があります。 このロールは一般にサポートされているものではありませんが、今回のように、このアクセス許可が設定されているユーザー アカウントがマネージド ID に対していくつかの組み込みロールを割り当てられるようにするシナリオでは利用が可能です。 これらのロールは **delegatedRoleDefinitionIds** プロパティに定義されており、ユーザー アクセスの管理者と所有者を除き、あらゆる [サポートされている Azure 組み込みロール](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)を含めることができます。

顧客のオンボードが終わると、この承認で作成された **principalId** が、顧客テナント内のマネージド ID にこれらの組み込みロールを割り当てることができるようになります。 ただし、ユーザー アクセス管理者ロールに通常関連付けられている他のアクセス許可が付与されることはありません。

以下の例は、ある **principalId** にユーザー アクセス管理者ロールを設定するものです。 このユーザーは、顧客テナント内でマネージド ID に次の 2 種類の組み込みロールを割り当てることができます:共同作成者および Log Analytics 共同作成者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>修復可能なポリシーをデプロイする

上で説明したとおりに必要なアクセス許可を設定してユーザーを作成した後、そのユーザーが、修復タスクを使用するポリシーを委任された顧客サブスクリプション内にデプロイできます。

たとえば、こちらの[サンプル](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)にあるように、顧客テナント内にある Azure Key Vault リソースを対象とした診断を有効にしたい場合を考えてみましょう。 (上で説明したとおり) 管理主体となるテナント内で適切なアクセス許可の設定を済ませたユーザーが、このシナリオを実現するために [Azure Resource Manager テンプレート](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)をデプロイします。

現時点では、委任されたサブスクリプションと一緒に使用するポリシーの割り当てを作成する操作に Azure portal ではなく、API を使用する必要があります。 その際、**apiVersion** は新しい **delegatedManagedIdentityResourceId** プロパティが含まれる **2019-04-01-preview** に設定する必要があります。 このプロパティによって、(Azure Lighthouse にオンボードされているサブスクリプションまたはリソース グループに) 顧客テナントに存在するマネージド ID を含めることができます。

次の例は、**delegatedManagedIdentityResourceId** を使ったロール割り当てを示したものです。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 委任されたサブスクリプションに (modify の効果を使って) タグを追加または削除するポリシーのデプロイ方法は、[別のよく似たサンプル](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag)でも確認できます。

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/index.yml) の詳細を確認する。
- [Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認する。
