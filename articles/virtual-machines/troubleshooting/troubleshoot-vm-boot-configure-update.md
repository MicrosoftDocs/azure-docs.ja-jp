---
title: VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内) | Microsoft Docs
description: 次のメッセージで停止する問題をトラブルシューティングするためのステップを紹介します。VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される
services: virtual-machines-windows
documentationcenter: ''
author: deland-han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c3592529d20680c6920e569887effee4ffe38344
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684002"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内)

この記事は、仮想マシン (VM) が起動中に次のメッセージで停止している問題の解決に役立ちます。"Windows の準備をしています。 コンピューターの電源を切らないでください。"

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>現象

**ブート診断**を使用して VM のスクリーンショットを取得するときに、オペレーティング システムが完全に起動されません。 VM は、次のメッセージを表示します。"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される

![Windows Server 2012 R2 でのメッセージの例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![メッセージの例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

この問題は通常、構成の変更後にサーバーが最終リブートを行っている場合に発生します。 構成の変更は、Windows Update またはサーバーのロール/機能に関する変更によって初期化される可能性があります。 Windows Update では、更新のサイズが大きかった場合、オペレーティング システムが変更を再構成するまでに必要な時間が長くなります。

## <a name="back-up-the-os-disk"></a>OS ディスクのバックアップ

この問題を修正しようとする前に、OS ディスクをバックアップしてください。

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>暗号化されたディスクがある VM の場合は、最初にディスクのロックを解除する必要があります

次の手順に従って、その VM が暗号化された VM であるかどうかを判定します。

1. Azure portal で VM を開き、ディスクを参照します。

2. **[暗号化]** 列を見て、暗号化が有効になっているかどうかを確認します。

OS ディスクが暗号化されている場合は、暗号化されたディスクのロックを解除します。 ディスクのロックを解除するには、次の手順に従います。

1. 影響を受ける VM と同じリソース グループ、ストレージ アカウント、場所に復旧 VM を作成します。

2. Azure Portal で、影響を受ける VM を削除し、そのディスクを保持します。

3. PowerShell を管理者として実行します。

4. 次のコマンドレットを実行し、シークレット名を取得します。

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. シークレット名を取得したら、PowerShell で次のコマンドを実行します。

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Base64 でエンコードされた値をバイトに変換し、その出力をファイルに書き込みます。 

    > [!Note]
    > USB のロック解除オプションを使用する場合は、BEK ファイル名が元の BEK GUID に一致している必要があります。 これらの手順に従う前に、C ドライブ上に "BEK" という名前のフォルダーを作成してください。
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. PC に BEK ファイルが作成された後、ロックされた OS ディスクが接続されている復旧 VM にファイルをコピーします。 BEK ファイルの場所を使用して、次のコマンドを実行します。

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **省略可能**: シナリオによっては、次のコマンドを使用してディスクを復号化することが必要になる場合があります。
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 前のコマンドでは、暗号化するディスクが文字 F 上にあることを前提にしています。

8. ログを収集する必要がある場合は、**ドライブ文字:\Windows\System32\winevt\Logs** のパスに移動します。

9. 復旧マシンからドライブをデタッチします。

### <a name="create-a-snapshot"></a>スナップショットの作成

スナップショットを作成するには、「[ディスクのスナップショットの作成](../windows/snapshot-copy-managed-disk.md)」の手順に従います。

## <a name="collect-an-os-memory-dump"></a>OS のメモリ ダンプの収集

構成時に VM が停止したときに OS ダンプを収集するには、[OS ダンプの収集](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)に関するセクションにある手順を使用します。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

ダンプ ファイルを収集した後、根本原因を分析するために [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に問い合わせてください。


## <a name="rebuild-the-vm-by-using-powershell"></a>PowerShell を使用して VM を再構築する

メモリ ダンプ ファイルを収集した後、次の手順に従って VM を再構築します。

**非マネージド ディスクの場合**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**マネージド ディスクの場合**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
