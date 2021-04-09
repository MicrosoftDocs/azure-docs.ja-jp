---
title: 'Virtual WAN: NVA への仮想ハブ ルート テーブルを作成する: Azure PowerShell'
description: トラフィックをネットワーク仮想アプライアンスに誘導するための Virtual WAN 仮想ハブ ルート テーブル。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267127"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>トラフィックをネットワーク仮想アプライアンスに誘導するための Virtual WAN ハブ ルート テーブルを作成する

この記事では、トラフィックを仮想ハブからネットワーク仮想アプライアンスに誘導するする方法を示します。 

![Virtual WAN のダイアグラム](./media/virtual-wan-route-table-nva/vwanroute.png)

この記事では、次の方法について説明します。

* WAN を作成する
* ハブを作成する
* ハブの仮想ネットワーク接続を作成する
* ハブ ルートを作成する
* ルート テーブルの作成
* ルート テーブルを適用する

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下の条件を満たしていることを確認します。

* ネットワーク仮想アプライアンス (NVA) があります。 これは、仮想ネットワークの Azure Marketplace から通常プロビジョニングする、ご自分で選択するサードパーティ製のソフトウェアです。
* NVA ネットワーク インターフェイスに割り当てられたプライベート IP があること。 
* NVA は仮想ハブにデプロイすることはできません。 独立した VNet 内にデプロイする必要があります。 この記事では、NVA の VNet を "DMZ VNet" と呼びます。
* "DMZ VNet" には、1 つまたは複数の仮想ネットワークを接続できます。 この記事では、それらの VNet を "間接スポーク VNet" と呼びます。 それらの VNet を、VNet ピアリングを使用して DMZ VNet に接続できます。
* 2 つの VNet が既に作成されていることを確認します。 それらは、スポーク VNet として使用されます。 この記事では、VNet スポークのアドレス空間は 10.0.2.0/24 と 10.0.3.0/24 です。 VNet の作成方法に関する情報が必要な場合は、「[PowerShell を使用して仮想ネットワークを作成する](../virtual-network/quick-create-powershell.md)」を参照してください。
* どの VNet 内にも仮想ネットワーク ゲートウェイがないことを確認します。

## <a name="1-sign-in"></a><a name="signin"></a>1.サインイン

Resource Manager PowerShell コマンドレットの最新版がインストールされていることを確認します。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページを参照してください。 この点は重要です。以前のバージョンのコマンドレットには、この演習に必要な最新の値が含まれていません。

1. 昇格された特権で PowerShell コンソールを開き、Azure アカウントにサインインします。 このコマンドレットでは、サインイン資格情報の入力を求められます。 サインイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

   ```powershell
   Connect-AzAccount
   ```
2. Azure サブスクリプションの一覧を取得します。

   ```powershell
   Get-AzSubscription
   ```
3. 使用するサブスクリプションを指定します。

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2.リソースを作成する

1. リソース グループを作成します。

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. 仮想 WAN を作成します。

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. 仮想ハブを作成します。

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3.接続を作成する

間接スポーク VNet と DMZ VNet から仮想ハブへのハブ仮想ネットワーク接続を作成します。

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4.仮想ハブ ルートを作成する

この記事では、間接スポーク VNet のアドレス空間は 10.0.2.0/24 と 10.0.3.0/24 であり、DMZ NVA ネットワーク インターフェイスのプライベート IP アドレスは 10.0.4.5 です。

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5.仮想ハブ ルート テーブルを作成する

仮想ハブ ルート テーブルを作成し、作成済みのルートに適用します。
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6.変更をコミットする

仮想ハブに変更をコミットします。

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
