---
title: PowerShell の例 - Azure SQL Database にマネージド インスタンスを作成する | Microsoft Docs
description: Azure SQL Database にマネージド インスタンスを作成する Azure PowerShell スクリプトの例です
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 32b3c3b45c627d8dfdb42642228a7f9d9786111e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569967"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>PowerShell を使用して Azure SQL Database マネージド インスタンスを作成する

この PowerShell スクリプトの例では、新しい仮想ネットワーク内の専用サブネットに Azure SQL Database マネージド インスタンスを作成します。 また、仮想ネットワークのルート テーブルとネットワーク セキュリティ グループも構成します。 スクリプトが正常に実行されると、マネージド インスタンスに仮想ネットワーク内またはオンプレミス環境からアクセスできます。 「[Azure SQL Database Managed Instance に接続するように Azure VM を構成する](../sql-database-managed-instance-configure-vm.md)」と「[オンプレミスから Azure SQL Database Managed Instance へのポイント対サイト接続を構成する](../sql-database-managed-instance-configure-p2s.md)」を参照してください。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](../sql-database-managed-instance-resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AZ PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 仮想ネットワークにサブネット構成を追加します |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | リソース グループ内の仮想ネットワークを取得します |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 仮想ネットワークの目標状態を設定します |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 仮想ネットワーク内のサブネットを取得します |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 仮想ネットワークのサブネット構成の目標状態を構成します |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | ルート テーブルを作成します |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | ルート テーブルを取得します |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | ルート テーブルの目標状態を設定します |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Azure SQL Database マネージド インスタンスを作成します |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
