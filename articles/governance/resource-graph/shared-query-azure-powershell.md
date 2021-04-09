---
title: 'クイックスタート: Azure PowerShell を使用して共有クエリを作成する'
description: このクイックスタートでは、手順に従い、Azure PowerShell を使用して Resource Graph 共有クエリを作成します。
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981546"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Resource Graph 共有クエリを作成する

この記事では、[Az.ResourceGraph](/powershell/module/az.resourcegraph) PowerShell モジュールを使用して、Azure Resource Graph 共有クエリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.ResourceGraph** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、別途インストールする必要があります。

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- 複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Resource Graph 共有クエリを作成する

選択した環境に **Az.ResourceGraph** PowerShell モジュールが追加されたら、Resource Graph 共有クエリを作成しましょう。 共有クエリは、Azure Resource Graph エクスプローラーで権限を与えたり、実行したりできる Azure Resource Manager オブジェクトです。 このクエリでは、すべてのリソースの総数が "_場所_" 別にグループ化され、まとめられます。

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、Azure Resource Graph 共有クエリを保存するリソース グループを作成します。 このリソース グループの名前は `resource-graph-queries` であり、場所は `westus2` です。

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. **Az.ResourceGraph** PowerShell モジュールと [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) コマンドレットを使用して、Azure Resource Graph 共有クエリを作成します。

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. 新しいリソース グループの共有クエリを一覧表示します。 [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) コマンドレットは値の配列を返します。

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. 共有クエリの結果を 1 つだけ取得するには、`Name` パラメーターを指定して `Get-AzResourceGraphQuery` を使用します。

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure 環境から Resource Graph 共有クエリとリソース グループを削除する場合は、次のコマンドを使用します。

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure PowerShell を使用して Resource Graph 共有クエリを作成しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)