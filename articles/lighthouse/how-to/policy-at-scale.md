---
title: 委任されたサブスクリプションに Azure Policy を大規模にデプロイする
description: Azure Lighthouse によって、ポリシー定義とポリシー割り当てを複数のテナントにわたってデプロイする方法について説明します。
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 48354c3cca7574b1d5acf71865218564591bc23e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049782"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>委任されたサブスクリプションに Azure Policy を大規模にデプロイする

サービス プロバイダーは、[Azure Lighthouse](../overview.md) に複数の顧客テナントをオンボードしている場合があります。 Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数のテナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

このトピックでは、PowerShell コマンドを介して [Azure Policy](../../governance/policy/index.yml) を使用し、複数のテナントにわたってポリシー定義とポリシー割り当てをデプロイする方法を示します。 この例では、ポリシー定義によって HTTPS トラフィックのみを許可することで、ストレージ アカウントが確実に保護されるようにしています。

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](../concepts/enterprise.md)も同じプロセスを使用できます。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Azure Resource Graph を使用して複数の顧客テナントにわたるクエリを実行する

[Azure Resource Graph](../../governance/resource-graph/index.yml) を使用して、管理対象の顧客テナントに含まれるすべてのサブスクリプションにわたってクエリを実行できます。 この例では、現在 HTTPS トラフィックを必要としていないサブスクリプションに含まれるすべてのストレージ アカウントを特定します。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>複数の顧客テナントにわたってポリシーをデプロイする

下の例は、[Azure Resource Manager テンプレート](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)を使用して、複数の顧客テナントの委任されたサブスクリプションにわたってポリシー定義とポリシー割り当てをデプロイする方法を示しています。 このポリシー定義では、すべてのストレージ アカウントが HTTPS トラフィックを使用するよう要求し、準拠していない新しいストレージ アカウントの作成を防止して、その設定が適用されていない既存のストレージ アカウントを非準拠としてマークします。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

> [!NOTE]
> 複数のテナントにポリシーをデプロイすることはできますが、現時点では、これらのテナント内の非準拠リソースに関する[コンプライアンスの詳細を表示](../../governance/policy/how-to/determine-non-compliance.md#compliance-details)することはできません。

## <a name="validate-the-policy-deployment"></a>ポリシーのデプロイを検証する

Azure Resource Manager テンプレートをデプロイした後は、委任されたサブスクリプションのいずれかで **EnableHttpsTrafficOnly** を **false** に設定してストレージ アカウントの作成を試みることで、ポリシー定義が正しく適用されたことを確認できます。 ポリシーの割り当てにより、このストレージ アカウントの作成はできないはずです。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

終了したら、このデプロイによって作成されたポリシー定義と割り当てを削除します。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/index.yml) の詳細を確認する。
- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- 委任されたサブスクリプション内で[修復が可能なポリシーをデプロイする](deploy-policy-remediation.md)方法について学習します。
