---
title: 'Azure ExpressRoute: クラシックの回線を Resource Manager に移行する'
description: このページでは、PowerShell を使用して、クラシックの回線を Resource Manager デプロイ モデルに移行する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532547"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>PowerShell を使用してクラシック デプロイ モデルから Resource Manager デプロイ モデルに ExpressRoute 回線を移行する

ExpressRoute 回線をクラシック デプロイ モデルと Resource Manager デプロイ モデルの両方で使用するには、その回線を Resource Manager デプロイ モデルに移行する必要があります。 次のセクションでは、PowerShell を使用して回線を移動する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* お使いのコンピューターにクラシックおよび Az Azure PowerShell モジュールの両方がローカルにインストールされていることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
* [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。 制限および制約事項について完全に理解していることを確認します。
* 回路がクラシック デプロイ モデルで完全に動作できることを確認します。
* Resource Manager デプロイ モデルで作成したリソース グループがあることを確認します。

## <a name="move-an-expressroute-circuit"></a>ExpressRoute 回線の移行

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>手順 1:クラシック デプロイ モデルからの回線の詳細を収集する

Azure クラシック環境にサインインし、サービス キーを収集します。

1. Azure アカウントにサインインします。

   ```powershell
   Add-AzureAccount
   ```

2. 適切な Azure サブスクリプションを選択します。

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Azure および ExpressRoute 用の PowerShell モジュールをインポートします。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. 次のコマンドレットを使用して、すべての ExpressRoute 回線のサービス キーを取得します。 キーの取得後、Resource Manager デプロイ モデルに移行する回線の **サービス キー** をコピーします。

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>手順 2:サインインし、リソース グループを作成する

Resource Manager 環境にサインインし、新しいリソース グループを作成します。

1. Azure Resource Manager 環境にサインインします。

   ```powershell
   Connect-AzAccount
   ```

2. 適切な Azure サブスクリプションを選択します。

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. 次のスニペットを変更して、新しいリソース グループを作成します (まだリソース グループがない場合)。

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>手順 3:Resource Manager デプロイ モデルに ExpressRoute 回線を移行する

クラシック デプロイ モデルから Resource Manager デプロイ モデルに ExpressRoute 回線を移行する準備はできています。 先に進む前に、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](expressroute-move.md)」に記載されている情報を確認してください。

回線を移行するには、次のスニペットを変更して実行します。

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

クラシック モードの場合、ExpressRoute 回線には、リージョンに関連付けられるという概念はありません。 しかし、Resource Manager の場合、すべてのリソースを Azure リージョンにマップする必要があります。 Move-AzExpressRouteCircuit コマンドレットに指定するリージョンは、技術的には任意のリージョンにすることが可能です。 組織化を目的とする場合は、ピアリングの場所を厳密に表すリージョンを選択してください。

> [!NOTE]
> * クラシック ExpressRoute 回線を Resource Manager デプロイ モデルに移行した後、既定でクラシック デプロイ モデルと Resource Manager デプロイ モデルの両方にアクセスできます。
> * 前のコマンドレットで表示される新しい名前を使用してリソースに対応します。 回線の名前は実質的に変更されます。

## <a name="modify-circuit-access"></a>回線アクセスの変更

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>両方のデプロイメント モデルに対して ExpressRoute 回線アクセスを有効にするには

Resource Manager デプロイ モデルで作成された ExpressRoute 回線に対し、クラシック デプロイ モデルへのアクセスを有効にできます。 以下のコマンドレットを実行して、両方のデプロイメント モデルへのアクセスを有効にします。

1. 回線の詳細を取得します。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. [従来の操作の許可] を TRUE に設定します。

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. 回線を更新します。 この操作が正常に完了すると、クラシック デプロイ モデルで回線を表示できるようになります。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. 次のコマンドレットを実行して、ExpressRoute 回線の詳細を取得します。 表示されたサービス キーを確認できる必要があります。

   ```powershell
   get-azurededicatedcircuit
   ```

5. これで、クラシック VNet についてはクラシック デプロイ モデル コマンドを、Resource Manager VNet については Resource Manager コマンドを使用して、ExpressRoute 回線へのリンクを管理できます。 次の記事では、ExpressRoute 回線へのリンクを管理する方法について説明します。

    * [Resource Manager デプロイ モデルの ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)
    * [クラシック デプロイ モデルの ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>クラシック デプロイ モデルへの ExpressRoute 回線アクセスを無効にするには

以下のコマンドレットを実行して、クラシック デプロイ モデルへのアクセスを無効にします。

1. ExpressRoute 回線の詳細を取得します。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. [従来の操作の許可] を FALSE に設定します。

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. 回線を更新します。 この操作が正常に完了すると、クラシック デプロイ モデルで回線を表示できなくなります。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>次のステップ

* [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-arm.md)
* [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)
