---
title: Azure PowerShell を使用して Azure Monitor for SAP Solutions をデプロイする
description: Azure PowerShell を使用して Azure Monitor for SAP Solutions をデプロイする
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1cea4b72049386ad7a1cc3e67003861c694812c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671960"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>クイック スタート」を参照してください。Azure PowerShell を使用してAzure Monitor for SAP Solutions をデプロイする

この記事では、[Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell モジュールを使用して、Azure Monitor for SAP Solutions リソースを作成する方法について説明します。

> [!CAUTION]
> Azure Monitor for SAP Solutions は、現在パブリック プレビュー段階です。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 運用環境のワークロードにはお勧めしません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="requirements"></a>必要条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。 Cloud Shell の使用を選択した場合、詳細については「[Azure Cloud Shell の概要](../../../cloud-shell/overview.md)」を参照してください。

> [!IMPORTANT]
> **Az.HanaOnAzure** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。 この PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../../../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、指定された名前のリソース グループを、指定された場所に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP モニター

SAP モニターを作成するには、[New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、およびリソース名の SAP モニターを作成します。

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

SAP モニターのプロパティを取得するには、[Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、およびリソース名の SAP モニターのプロパティを取得します。

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>プロバイダー インスタンス

プロバイダー インスタンスを作成するには、[New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、およびリソース名のプロバイダー インスタンスを作成します。

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

プロバイダー インスタンスのプロパティを取得するには、[Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、SapMonitor 名、およびリソース名のプロバイダー インスタンスのプロパティを取得します。

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要であれば、次の例を実行して削除できます。

### <a name="delete-the-provider-instance"></a>プロバイダー インスタンスを削除する

プロバイダー インスタンスを削除するには、[Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、SapMonitor 名、およびリソース名のプロバイダー インスタンスを削除します。

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>SAP モニターを削除する

SAP モニターを削除するには、[Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) コマンドレットを使用します。 次の例では、指定されたサブスクリプション、リソース グループ、およびモニター名の SAP モニターを削除します。

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>リソース グループを削除します

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[Azure Monitor for SAP Solutions](azure-monitor-overview.md) の詳細について詳しく学習する。