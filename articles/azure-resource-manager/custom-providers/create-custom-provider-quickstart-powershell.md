---
title: Azure PowerShell を使用して Azure カスタム リソース プロバイダーを作成する
description: Azure PowerShell を使用して Azure カスタム リソース プロバイダーを作成する方法について説明します
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951844"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>クイックスタート:Azure PowerShell を使用して Azure カスタム リソース プロバイダーを作成する

このクイックスタートでは、[Az.CustomProviders](/powershell/module/az.customproviders) PowerShell モジュールを使用して独自の Azure カスタム リソース プロバイダーを作成する方法について説明します。

> [!CAUTION]
> 現在、Azure カスタム プロバイダーはパブリック プレビュー段階にあります。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 運用環境のワークロードにはお勧めしません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="requirements"></a>必要条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。 Cloud Shell の使用を選択した場合、詳細については「[Azure Cloud Shell の概要](../../cloud-shell/overview.md)」を参照してください。

> [!IMPORTANT]
> **Az.CustomProviders** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。 この PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、指定された名前のリソース グループを、指定された場所に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>カスタム リソース プロバイダーの作成

カスタム リソース プロバイダーを作成または更新するには、次の例に示すように、[New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) コマンドレットを使用します。

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>カスタム リソース プロバイダー マニフェストを取得する

カスタム リソース プロバイダー マニフェストに関する情報を取得するには、次の例に示すように、[Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) コマンドレットを使用します。

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>関連付けを作成する

関連付けを作成または更新するには、次の例に示すように、[New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) コマンドレットを使用します。

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>関連付けを取得する

関連付けに関する情報を取得するには、次の例に示すように、[Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) コマンドレットを使用します。

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要であれば、次の例を実行して削除できます。

### <a name="delete-an-association"></a>関連付けを削除する

関連付けを削除するには、[Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) コマンドレットを使用します。 次の例では、関連付けを削除します。

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>カスタム リソース プロバイダーの削除

カスタム リソース プロバイダーを削除するには、[Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) コマンドレットを使用します。 次の例では、カスタム リソース プロバイダーを削除します。

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>リソース グループを削除します

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

詳細については、[Azure カスタム リソース プロバイダー](overview.md)に関するページを参照してください。