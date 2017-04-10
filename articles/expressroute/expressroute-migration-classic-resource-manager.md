---
title: "クラシックから Resource Manager への ExpressRoute 回線および関連する仮想ネットワークの移行: Azure | Microsoft Docs"
description: "このページでは、クラシック回線および関連する仮想ネットワークを Resource Manager に移行する方法について説明します。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>クラシックから Resource Manager デプロイメント モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行

この記事では、Azure ExpressRoute 回線および関連する仮想ネットワークをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法について説明します。 


## <a name="before-you-begin"></a>開始する前に
* Azure PowerShell モジュールの最新バージョンがあることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
* [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。 制限および制約事項について完全に理解していることを確認します。
* 回路がクラシック デプロイメント モデルで完全に動作できることを確認します。
* Resource Manager デプロイ モデルで作成したリソース グループがあることを確認します。
* 次のリソースの移行に関するドキュメントを確認します。

    * [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ: プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [クラシックから Azure Resource Manager への移行中に発生する一般的なエラー](../virtual-machines/virtual-machines-migration-errors.md)

## <a name="supported-and-unsupported-scenarios"></a>サポートされているシナリオとサポートされていないシナリオ

* ExpressRoute 回線は、ダウンタイムなしでクラシック環境から Resource Manager 環境に移行できます。 ExpressRoute 回線は、ダウンタイムなしでクラシック環境から Resource Manager 環境にすべて移動できます。 [PowerShell を使用してクラシック デプロイメント モデルから Resource Manager デプロイメント モデルに ExpressRoute 回線を移行する方法](expressroute-howto-move-arm.md)に関するページの手順に従ってください。 これは、仮想ネットワークに接続されているリソースを移動するための前提条件です。
* 同じサブスクリプションの ExpressRoute 回線に接続されている仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内の関連するデプロイは、ダウンタイムなしに Resource Manager 環境に移行できます。 後で説明する手順に従って、仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内にデプロイされた仮想マシンなどのリソースを移行できます。 これらのリソースを移行する前に、仮想ネットワークが正しく構成されていることを確認する必要があります。 
* ExpressRoute 回線と異なるサブスクリプションの仮想ネットワーク、ゲートウェイ、および仮想ネットワーク内の関連するデプロイを移行する場合は、ダウンタイムが伴います。 このドキュメントの最後のセクションに、これらのリソースを移行するための手順を説明します。

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>クラシックから Resource Manager への ExpressRoute 回線の移動
ExpressRoute 回線に接続されているリソースを移行する前に、ExpressRoute 回線をクラシックから Resource Manager 環境に移動する必要があります。 このタスクを行うには、次の記事を参照してください。

* [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。
* [Azure PowerShell を使用してクラシックから Resource Manager に回線を移動する](expressroute-howto-move-arm.md)。
* Azure Service Management ポータルを使用します。 ワークフローに従って[新しい ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、インポート オプションを選択します。 

この操作では、ダウンタイムは発生しません。 移行操作中でも、オンプレミスと Microsoft の間で引き続きデータを転送できます。

## <a name="prepare-your-virtual-network-for-migration"></a>移行のための仮想ネットワークの準備
移行する仮想ネットワークのネットワークに不必要なアーティファクトがないことを確認する必要があります。 仮想ネットワークの構成をダウンロードし、必要に応じて更新するには、次の PowerShell コマンドレットを実行します。

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
移行する仮想ネットワークから <ConnectionsToLocalNetwork> に対するすべての参照が削除されていることを確認する必要があります。 サンプルのネットワーク構成を次のスニペットに示します。

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
<ConnectionsToLocalNetwork> が空でない場合は、その下の参照を削除し、ネットワーク構成を再送信してください。 そのためには、次の PowerShell コマンドレットを実行します。

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>ExpressRoute 回線と同じサブスクリプションの仮想ネットワーク、ゲートウェイ、および関連するデプロイの移行
このセクションでは、ExpressRoute 回線と同じサブスクリプションの仮想ネットワーク、ゲートウェイ、および関連するデプロイを移行するための手順について説明します。 この移行に関連してダウンタイムは発生しません。 移行プロセス中にすべてのリソースを引き続き使用できます。 移行操作中は、管理プレーンがロックされます。 

1. ExpressRoute 回線がクラシックから Resource Manager 環境に移動されていることを確認します。
2. 仮想ネットワークが移行用に適切に準備されていることを確認します。
3. リソースの移行用にサブスクリプションを登録します。 リソースの移行用にサブスクリプションを登録するには、次の PowerShell スニペットを使用します。 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. 検証、準備、および移行を行います。 仮想ネットワークを移動するには、次の PowerShell スニペットを使用します。
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    次の PowerShell コマンドレットを実行すると、移行を中止できます。
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>ExpressRoute 回線と異なるサブスクリプションの仮想ネットワーク、ゲートウェイ、および関連するデプロイの移行

1. ExpressRoute 回線がクラシックから Resource Manager 環境に移動されていることを確認します。
2. 仮想ネットワークが移行用に適切に準備されていることを確認します。
3. ExpressRoute 回線がクラシック環境と Resource Manager 環境の両方で動作することを確認します。 クラシック環境と Resource Manager 環境の両方で回線を使用できるようにするには、次の PowerShell スクリプトを使用します。 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Resource Manager 環境で承認を作成します。 承認の作成方法については、[仮想ネットワークを ExpressRoute 回線にリンクする方法](expressroute-howto-linkvnet-arm.md)に関するページを参照してください。 承認を作成するには、次の PowerShell スニペットを使用します。
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    回線 ID と承認キーを記録します。 これらの要素は、移行が完了した後で仮想ネットワークに回線を接続するために使用されます。
  
5. 仮想ネットワークに関連付けられている専用回線リンクを削除します。 クラシック環境で回線リンクを削除するには、次のコマンドレットを使用します。 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. リソースの移行用にサブスクリプションを登録します。 リソースの移行用にサブスクリプションを登録するには、次の PowerShell スニペットを使用します。 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. 検証、準備、および移行を行います。 仮想ネットワークを移動するには、次の PowerShell スニペットを使用します。
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    次の PowerShell コマンドレットを実行すると、移行を中止できます。
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. 仮想ネットワークを再び ExpressRoute 回線に接続します。 次の PowerShell スニペットは、仮想ネットワークが作成されているサブスクリプションのコンテキストで実行されます。 このスニペットを回線が作成されているサブスクリプションで実行しないでください。 手順 4 で記録した、PeerID としての回線 ID と承認キーを使用します。
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>次のステップ
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ: プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [クラシックから Azure Resource Manager への移行中に発生する一般的なエラー](../virtual-machines/virtual-machines-migration-errors.md)

