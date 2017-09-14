---
title: "Azure 仮想ネットワーク ゲートウェイと接続のトラブルシューティング - PowerShell | Microsoft Docs"
description: "このページでは、Azure Network Watcher を使用して PowerShell コマンドレットのトラブルシューティングを行う方法を説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0bdffbac18d1d236b7674feed4dbc784e50e0ea8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Azure Network Watcher PowerShell を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング

> [!div class="op_single_selector"]
> - [ポータル](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher は、Azure 内のネットワーク リソースの把握に関する多くの機能を提供します。 これらの機能の 1 つが、リソースのトラブルシューティングです。 リソースのトラブルシューティングは、ポータル、PowerShell、CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher は仮想ネットワーク ゲートウェイまたは接続の正常性を検査し、その結果を返します。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

サポートされるゲートウェイの種類の一覧については、「[Supported Gateway types (サポートされるゲートウェイの種類)](network-watcher-troubleshoot-overview.md#supported-gateway-types)」を参照してください。

## <a name="overview"></a>概要

リソースのトラブルシューティングを使用すると、仮想ネットワーク ゲートウェイと接続によって生じる問題をトラブルシューティングできます。 リソースのトラブルシューティングに対する要求が行われると、ログのクエリが実行され、検査されます。 検査が完了すると、結果が返されます。 リソースのトラブルシューティングの要求は長時間実行される要求であり、結果が返るまで数分かかることがあります。 トラブルシューティングのログは、指定したストレージ アカウントのコンテナーに格納されます。

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

まず、Network Watcher インスタンスを取得します。 `$networkWatcher` 変数は、手順 4 で `Start-AzureRmNetworkWatcherResourceTroubleshooting` コマンドレットに渡されます。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>仮想ネットワーク ゲートウェイ接続の取得

この例では、リソースのトラブルシューティングは接続上で実行されています。 仮想ネットワーク ゲートウェイを渡すこともできます。

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

リソースのトラブルシューティングではリソースの正常性に関するデータを返すとともに、レビュー用にストレージ アカウントにログを保存します。 この手順では、ストレージ アカウントを作成します。既存のストレージ アカウントが存在する場合は、それを使用できます。

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher のリソースのトラブルシューティングの実行

リソースのトラブルシューティングは、`Start-AzureRmNetworkWatcherResourceTroubleshooting` コマンドレットを使用して行います。 Network Watcher オブジェクト、接続または仮想ネットワーク ゲートウェイの ID、ストレージ アカウント ID、結果の保存先のパスをこのコマンドレットに渡します。

> [!NOTE]
> `Start-AzureRmNetworkWatcherResourceTroubleshooting` コマンドレットは長時間実行され、完了するまでに数分かかることがあります。

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

コマンドレットを実行すると、Network Watcher によってリソースがレビューされて正常性が検証されます。 結果はシェルに戻され、指定したストレージ アカウントに結果のログが保存されます。

## <a name="understanding-the-results"></a>結果について

アクション テキストは、問題を解決する方法の一般的なガイダンスを提供します。 問題に対してアクションを実施できる場合は、追加のガイダンスのリンクが提供されます。 追加のガイダンスがない場合は、サポート ケースを開くための URL が応答で提供されます。  応答のプロパティと内容の詳細については、[Network Watcher のトラブルシューティングの概要](network-watcher-troubleshoot-overview.md)に関するページを参照してください。

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure BLOB ストレージを使用する](../storage/blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他に Storage Explorer があります。 ストレージ エクスプローラーの詳細については、[ストレージ エクスプローラー](http://storageexplorer.com/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

設定が変更されて VPN 接続が停止される場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、問題がある可能性のあるネットワーク セキュリティ グループとセキュリティ規則を詳しく調べます。

