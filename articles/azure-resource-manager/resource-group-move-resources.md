---
title: Azure リソースを新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs
description: Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721388"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>リソースを新しいリソース グループまたはサブスクリプションに移動する

この記事では、Azure リソースを別の Azure サブスクリプションまたは同じサブスクリプションの別のリソース グループに移動する方法について説明します。 リソースの移動には、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用できます。

移動操作の間は、ソース グループとターゲット グループの両方がロックされます。 これらのリソース グループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。 このロックはリソース グループでリソースを追加、更新、削除できなくなることを意味しますが、リソースが停止されるわけではありません。 たとえば、SQL Server とそのデータベースを新しいリソース グループに移動する場合、そのデータベースを使用するアプリケーションにダウンタイムは発生しません。 これまでどおり、データベースの読み取りと書き込みを行うことができます。

リソースを移動しても、新しいリソース グループまたはサブスクリプションに移動されるだけです。 リソースの場所は変わりません。

## <a name="checklist-before-moving-resources"></a>リソースの移動前のチェック リスト

リソースを移動する前に行うべき重要な手順がいくつかあります。 これらの条件を確認することにより、エラーの発生を回避できます。

1. 移動するリソースは、移動操作をサポートしているリソースである必要があります。 移動をサポートしているリソースのリストは、「[Move operation support for resources](move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。

1. 一部のサービスには、リソースを移動するときに特定の制限または要件があります。 次のサービスのいずれかを移動する場合は、移動の前にそのガイダンスを確認してください。

   * [App Services の移動ガイダンス](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services の移動ガイダンス](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [クラシック デプロイ モデルの移動ガイダンス](./move-limitations/classic-model-move-limitations.md) - Classic Compute、Classic Storage、Classic Virtual Networks、Cloud Services
   * [Recovery Services の移動ガイダンス](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [仮想マシンの移動ガイダンス](./move-limitations/virtual-machines-move-limitations.md)
   * [仮想ネットワークの移動ガイダンス](./move-limitations/virtual-network-move-limitations.md)

1. 移動元と移動先のサブスクリプションがアクティブである必要があります。 無効になっているアカウントを有効にするときに問題がある場合は、[Azure サポート リクエストを作成します](../azure-supportability/how-to-create-azure-support-request.md)。 問題の種類として **[サブスクリプション管理]** を選択します。

1. 移動元と移動先のサブスクリプションが同じ [Azure Active Directory テナント](../active-directory/develop/quickstart-create-new-tenant.md)内に存在している必要があります。 両方のサブスクリプションに同じテナント ID があることを確認するには、Azure PowerShell または Azure CLI を使用します。

   Azure PowerShell では、次を使用します。

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI では、次を使用します。

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   移動元と移動先のサブスクリプションのテナント ID が同じでない場合、次の方法でテナント ID を調整する必要があります。

   * [Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)
   * [Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 移動するリソースのリソース プロバイダーについて、移動先のサブスクリプションに登録する必要があります。 登録しないと、 **リソースの種類についてサブスクリプションへの登録が行われていない**ことを示すエラーが発生します。 このエラーは、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。

   PowerShell で登録状態を取得するには、次のコマンドを使用します。

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   リソース プロバイダーを登録するには、次のコマンドを使用します。

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI で登録状態を取得するには、次のコマンドを使用します。

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   リソース プロバイダーを登録するには、次のコマンドを使用します。

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. リソースを動かすアカウントには少なくとも次のアクセス許可を与える必要があります。

   * ソース リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**。
   * ターゲット リソース グループの **Microsoft.Resources/subscriptions/resourceGroups/write**。

1. リソースを移動する前に、リソースの移動先となるサブスクリプションのサブスクリプション クォータをチェックします。 リソースを移動することでサブスクリプションが制限を上回る場合、クォータの引き上げを要求できるかどうかを確認する必要があります。 制限の一覧と引き上げを要求する方法については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

## <a name="validate-move"></a>移動の検証

[移動の検証操作](/rest/api/resources/resources/validatemoveresources)を使用すると、実際にリソースを移動することなく、必要な移動のシナリオをテストすることができます。 この操作は、正常に移動されるかどうかを確認する目的で使用します。 移動要求を送信すると、検証が自動的に呼び出されます。 この操作は、結果を事前に確認する必要がある場合にのみ使用してください。 この操作を実行するには、次の要件を満たす必要があります。

* 移動元のリソース グループの名前
* 移動先のリソース グループのリソース ID
* 移動する各リソースのリソース ID
* アカウントの[アクセス トークン](/rest/api/azure/#acquire-an-access-token)

次の要求を送信します。

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

要求本文:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

要求が正しい形式になっていれば、次の応答が操作から返されます。

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

状態コード 202 は、検証要求が受け付けられたものの、移動操作に成功するかどうかの判断はまだ出ていないことを示します。 `location` 値には、長時間実行される操作の状態をチェックするための URL が格納されます。  

状態をチェックするには、次の要求を送信します。

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

操作が実行されている間は、継続的に状態コード 202 が返されます。 `retry-after` 値に示されている時間 (秒) が経過するのを待って再試行してください。 移動操作の検証が正常に完了すると、状態コード 204 が返されます。 移動の検証に失敗した場合は、次のようなエラー メッセージが返されます。

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>ポータルの使用

リソースを移動するには、それらのリソースがあるリソース グループを選択し、 **[移動]** ボタンを選択します。

![リソースの移動](./media/resource-group-move-resources/select-move.png)

リソースを新しいリソース グループに移動するか新しいサブスクリプションに移動するかを選択します。

移動するリソースを、移動先のリソース グループを選択します。 そのリソースのスクリプトを更新する必要があること確認し、 **[OK]** を選択します。 前の手順でサブスクリプションの編集アイコンを選択した場合は、移動先のサブスクリプションも選択する必要があります。

![移動先の選択](./media/resource-group-move-resources/select-destination.png)

**[通知]** に、移動操作が実行されていることが表示されます。

![移動の状態の表示](./media/resource-group-move-resources/show-status.png)

完了すると、結果が表示されます。

![移動の結果の表示](./media/resource-group-move-resources/show-result.png)

エラーが発生した場合は、「[新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング](troubleshoot-move.md)」を参照してください。

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[Move-AzResource](/powershell/module/az.resources/move-azresource) コマンドを使用します。 次の例では、いくつかのリソースを新しいリソース グループに移動する方法を示します。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

新しいサブスクリプションに移動する場合は、`DestinationSubscriptionId` パラメーターの値を含めます。

エラーが発生した場合は、「[新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング](troubleshoot-move.md)」を参照してください。

## <a name="use-azure-cli"></a>Azure CLI の使用

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) コマンドを使用します。 移動するリソースのリソース ID を指定します。 次の例では、いくつかのリソースを新しいリソース グループに移動する方法を示します。 `--ids` パラメーターには、移動するリソース ID のスペース区切りリストを指定します。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

エラーが発生した場合は、「[新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング](troubleshoot-move.md)」を参照してください。

## <a name="use-rest-api"></a>REST API を使用する

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、[リソースの移動](/rest/api/resources/Resources/MoveResources)操作を使用します。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

エラーが発生した場合は、「[新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング](troubleshoot-move.md)」を参照してください。

## <a name="next-steps"></a>次の手順

移動をサポートしているリソースのリストは、「[Move operation support for resources](move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。
