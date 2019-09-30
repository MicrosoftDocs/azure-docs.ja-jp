---
title: VM (クラシック) または Cloud Services のロール インスタンスを別のサブネットに移動する - Azure PowerShell | Microsoft Docs
description: PowerShell を使用して VM (クラシック) と Cloud Services のロール インスタンスを別のサブネットに移動する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058373"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>PowerShell を使用して VM (クラシック) と Cloud Services のロール インスタンスを別のサブネットに移動する
PowerShell を使用すると、同じ仮想ネットワーク (VNet) 内のサブネット間で VM (クラシック) を移動することができます。 ロール インスタンスを移動する場合は、PowerShell を使用せずに、CSCFG ファイルを編集します。

> [!NOTE]
> この記事では、クラシック デプロイ モデルのみを使用してデプロイされた VM を移動する方法について説明します。
> 
> 

サブネット間で VM を移動する理由は何でしょうか。 たとえば、古いサブネットが小さいにもかかわらず、その中で既存の VM が動作しているために拡張できないときは、サブネットを移行することで問題を解決できます。 サブネットを移行する場合は、まず新しい、より規模の大きいサブネットを作成し、そこに VM を移行します。そして移行が完了したら、空になった古いサブネットを削除します。

## <a name="how-to-move-a-vm-to-another-subnet"></a>別のサブネットに VM を移動する方法
VM を移動するには、次の例をテンプレートとして使用して、Set-AzureSubnet という PowerShell コマンドレットを実行します。 以下の例では、TestVM を現在のサブネットから Subnet-2 に移動しています。 必ず、環境に合わせて例を編集してください。 移動の手順として Update-AzureVM コマンドレットを実行すると、更新プロセスの一環として VM が常に再起動されます。

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

VM に対して静的内部プライベート IP が指定されている場合は、VM を新しいサブネットに移動する前に、その設定を解除する必要があります。 この場合、以下のように指定します。

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>別のサブネットにロール インスタンスを移動する方法
ロール インスタンスを移動するには、CSCFG ファイルを編集します。 以下の例では、Virtual Network *VNETName* 内の「Role0」 を、現在のサブネットから *Subnet-2* に移動します。 ロール インスタンスは既にデプロイされているので、単に Subnet name = Subnet-2 の部分を変更します。 必ず、環境に合わせて例を編集してください。

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
