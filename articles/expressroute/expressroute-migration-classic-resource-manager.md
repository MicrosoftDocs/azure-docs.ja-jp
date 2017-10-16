---
title: "クラシックから Resource Manager への ExpressRoute 回線および関連する仮想ネットワークの移行: PowerShell | Microsoft Docs"
description: "このページでは、回線の移行後に、関連する仮想ネットワークを Resource Manager に移行する方法について説明します。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>クラシックから Resource Manager への ExpressRoute 回線および関連する仮想ネットワークの移行

この記事では、Azure ExpressRoute 回線の移行後に、ExpressRoute 回線に関連する仮想ネットワークをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法について説明します。 


## <a name="before-you-begin"></a>開始する前に
* Azure PowerShell モジュールの最新バージョンがあることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
* [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。 制限および制約事項について完全に理解していることを確認します。
* 回路がクラシック デプロイメント モデルで完全に動作できることを確認します。
* Resource Manager デプロイ モデルで作成したリソース グループがあることを確認します。
* 次のリソースの移行に関するドキュメントを確認します。

    * [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ: プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Review most common migration errors and mitigations](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーと緩和策を確認する)

## <a name="supported-and-unsupported-scenarios"></a>サポートされているシナリオとサポートされていないシナリオ

* ExpressRoute 回線は、ダウンタイムなしでクラシック環境から Resource Manager 環境に移行できます。 ExpressRoute 回線は、ダウンタイムなしでクラシック環境から Resource Manager 環境にすべて移動できます。 [PowerShell を使用してクラシック デプロイメント モデルから Resource Manager デプロイメント モデルに ExpressRoute 回線を移行する方法](expressroute-howto-move-arm.md)に関するページの手順に従ってください。 これは、仮想ネットワークに接続されているリソースを移動するための前提条件です。
* 同じサブスクリプションの ExpressRoute 回線に接続されている仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内の関連するデプロイは、ダウンタイムなしに Resource Manager 環境に移行できます。 後で説明する手順に従って、仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内にデプロイされた仮想マシンなどのリソースを移行できます。 これらのリソースを移行する前に、仮想ネットワークが正しく構成されていることを確認する必要があります。 
* ExpressRoute 回線と異なるサブスクリプションの仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内の関連するデプロイを移行する場合は、ダウンタイムが伴います。 このドキュメントの最後のセクションに、これらのリソースを移行するための手順を説明します。
* ExpressRoute ゲートウェイと VPN Gateway の両方を含んだ仮想ネットワークは移行できません。

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>クラシックから Resource Manager への ExpressRoute 回線の移動
ExpressRoute 回線に接続されているリソースを移行する前に、ExpressRoute 回線をクラシックから Resource Manager 環境に移動する必要があります。 このタスクを行うには、次の記事を参照してください。

* [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。
* [Azure PowerShell を使用してクラシックから Resource Manager に回線を移動する](expressroute-howto-move-arm.md)。
* Azure Service Management ポータルを使用します。 ワークフローに従って[新しい ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、インポート オプションを選択します。 

この操作では、ダウンタイムは発生しません。 移行操作中でも、オンプレミスと Microsoft の間で引き続きデータを転送できます。

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>仮想ネットワーク、ゲートウェイ、および関連するデプロイの移行

移行する手順は、リソースが同じサブスクリプションにあるか、別のサブスクリプションにあるか、またはその両方にあるかによって異なります。

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>ExpressRoute 回線と同じサブスクリプションの仮想ネットワーク、ゲートウェイ、および関連するデプロイの移行
このセクションでは、ExpressRoute 回線と同じサブスクリプションの仮想ネットワーク、ゲートウェイ、および関連するデプロイを移行するための手順について説明します。 この移行に関連してダウンタイムは発生しません。 移行プロセス中にすべてのリソースを引き続き使用できます。 移行操作中は、管理プレーンがロックされます。 

1. ExpressRoute 回線がクラシックから Resource Manager 環境に移動されていることを確認します。
2. 仮想ネットワークが移行用に適切に準備されていることを確認します。
3. リソースの移行用にサブスクリプションを登録します。 リソースの移行用にサブスクリプションを登録するには、次の PowerShell スニペットを使用します。

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. 検証、準備、および移行を行います。 仮想ネットワークを移動するには、次の PowerShell スニペットを使用します。

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  次の PowerShell コマンドレットを実行すると、移行を中止できます。

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>次のステップ
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ: プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Review most common migration errors and mitigations](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーと緩和策を確認する)
