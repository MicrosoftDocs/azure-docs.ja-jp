---
title: "一般化された Azure VM からの VM イメージのキャプチャ | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで作成された一般化された Azure VM から VM イメージをキャプチャする方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7d3b927a1dc111c54efadadac469cf070596dd43
ms.openlocfilehash: 9e4f80be051b9de05e9bb890259116071dba59c0
ms.lasthandoff: 02/16/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>一般化された Azure VM から VM イメージをキャプチャする方法
この記事では、Azure PowerShell を使用して、一般化された Azure VM のイメージを作成する方法について説明します。 その後、そのイメージを使用して、別の VM を作成できます。 イメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。 イメージには仮想ネットワーク リソースは含まれないため、新しい VM を作成するときにこれらのリソースを設定する必要があります。 

## <a name="prerequisites"></a>前提条件
* 既に[一般化されている VM](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) が必要です。 VM の一般化とは、特に重要なこととして、すべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 `sudo waagent -deprovision+user` を使用して Linux VM を一般化してから、PowerShell を使用して VM をキャプチャすることもできます。 CLI を使用して VM をキャプチャする方法については、[Azure CLI で Linux 仮想マシンを一般化してキャプチャする方法](virtual-machines-linux-capture-image.md)に関する記事をご覧ください。
* Azure PowerShell Version 1.0.x 以降がインストールされている必要があります。 PowerShell がインストールされていない場合、インストール手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン
1. Azure PowerShell を開き、Azure アカウントにサインインします。
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。
2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM の割り当てを解除して、一般化状態に設定
1. VM リソースの割り当てを解除します。
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure Portal で VM の *[状態]* が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。
2. 仮想マシンの状態を **[一般化]**に設定します。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM の状態を確認します。 VM の **[OSState/generalized (OS の状態/一般化)]** セクションの **[DisplayStatus (ステータス表示)]** が **[VM が汎用化されました]** に設定されています。  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>イメージの作成
1. 次のコマンドを使用して、仮想マシンのイメージをコピー先ストレージ コンテナーにコピーします。 イメージは、元の仮想マシンと同じストレージ アカウントに作成されます。 `-Path` パラメーターにより、JSON テンプレートのコピーがローカルに保存されます。 `-DestinationContainerName` パラメーターは、イメージを格納するコンテナーの名前です。 コンテナーが存在しない場合、コンテナーは自動的に作成されます。
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    イメージの URL は、JSON ファイル テンプレートから取得できます。 イメージの完全なパスは、**resources** > **storageProfile** > **osDisk** > **image** > **uri** セクションにあります。 イメージの URL は `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`のようになります。
   
    ポータルで URI を確認することもできます。 イメージは、ストレージ アカウントの **system** という名前のコンテナーにコピーされます。 

## <a name="next-steps"></a>次のステップ
* [イメージから VM を作成](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。


