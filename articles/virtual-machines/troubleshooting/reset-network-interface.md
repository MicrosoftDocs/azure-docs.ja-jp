---
title: Azure Windows VM のネットワーク インターフェイスをリセットする方法 | Microsoft Docs
description: Azure Windows VM のネットワーク インターフェイスをリセットする方法を示します
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: afb8335d3206a76b8f9bc47733e9816126e80af0
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058460"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Azure Windows VM のネットワーク インターフェイスをリセットする方法 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

この記事では、Microsoft Azure Windows Virtual Machine (VM) に接続できなくなったときに、次の操作を行ってから、Azure Windows VM のネットワーク インターフェイスをリセットして問題を解決する方法について説明します。

* 既定のネットワーク インターフェイス (NIC) を無効にする。 
* NIC の静的 IP を手動で設定する。 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>ネットワーク インターフェイスをリセットする

### <a name="for-vms-deployed-in-resource-group-model"></a>リソース グループ モデルでデプロイされた VM の場合

1.  [Azure ポータル](https://ms.portal.azure.com)にアクセスします。
2.  影響を受ける仮想マシンを選択します。
3.  **[ネットワーク]** を選択してから、VM のネットワーク インターフェイスを選択します。

    ![ネットワーク インターフェイスの場所](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  **[IP 構成]** を選択します。
5.  IP を選択します。 
6.  **[プライベート IP の割り当て]** が **[静的]** になっていない場合は、 **[静的]** に変更します。
7.  **[IP アドレス]** を、サブネットで使用できる別の IP アドレスに変更します。
8. 仮想マシンが再起動して、新しい NIC をシステムに初期化します。
9.  マシンへの RDP を試します。 成功したら、必要に応じて元のプライベート IP アドレスに戻すことができます。 あるいは、そのまま保持することもできます。 

#### <a name="use-azure-powershell"></a>Azure PowerShell の使用

1. [最新の Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) がインストールされていることを確認します。
2. 管理者特権の Azure PowerShell セッション (管理者として実行) を開きます。 次のコマンドを実行します。

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. マシンへの RDP を試します。  成功したら、必要に応じて元のプライベート IP アドレスに戻すことができます。 あるいは、そのまま保持することもできます。

### <a name="for-classic-vms"></a>クラシック VM の場合

ネットワーク インターフェイスをリセットするには、次の手順に従います。

#### <a name="use-azure-portal"></a>Azure Portal の使用

1.  [Azure ポータル]( https://ms.portal.azure.com)にアクセスします。
2.  **[仮想マシン (クラシック)]** を選択します。
3.  影響を受ける仮想マシンを選択します。
4.  **[IP アドレス]** を選択します。
5.  **[プライベート IP の割り当て]** が **[静的]** になっていない場合は、 **[静的]** に変更します。
6.  **[IP アドレス]** を、サブネットで使用できる別の IP アドレスに変更します。
7.  **[保存]** を選択します。
8.  仮想マシンが再起動して、新しい NIC をシステムに初期化します。
9.  マシンへの RDP を試します。 成功した場合は、元のプライベート IP アドレスに戻すことができます。  

#### <a name="use-azure-powershell"></a>Azure PowerShell の使用

1. [最新の Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) がインストールされていることを確認します。
2. 管理者特権の Azure PowerShell セッション (管理者として実行) を開きます。 次のコマンドを実行します。

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. マシンへの RDP を試します。 成功したら、必要に応じて元のプライベート IP アドレスに戻すことができます。 あるいは、そのまま保持することもできます。 

## <a name="delete-the-unavailable-nics"></a>使用できない NIC を削除する
コンピューターにリモート デスクトップ接続できたら、潜在的な問題を回避するために古い NIC を削除する必要があります。

1.  デバイス マネージャーを開きます。
2.  **[表示]**  >  **[非表示のデバイスの表示]** を選択します。
3.  **[ネットワーク アダプター]** を選択します。 
4.  "Microsoft Hyper-V Network Adapter" という名前のアダプターを確認します。
5.  使用できないアダプターは淡色表示されることがあります。アダプターを右クリックし、[アンインストール] を選択します。

    ![NIC の図](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 名前が "Microsoft Hyper-V Network Adapter" である使用できないアダプターのみをアンインストールしてください。 その他の非表示のアダプターをアンインストールすると、別の問題が発生する可能性があります。
    >
    >

6.  これで、使用できないすべてのアダプターがシステムから取り除かれます
