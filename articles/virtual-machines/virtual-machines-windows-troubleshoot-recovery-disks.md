---

title: "Azure PowerShell での Windows トラブルシューティング VM の使用 | Microsoft Docs"
description: "Azure PowerShell で OS ディスクを復旧 VM に接続して、Azure で Windows VM の問題をトラブルシュートする方法を説明します。"
services: virtual-machines-windows
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/13/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: ad862fa466dcd985da07b2b89d8a995b77e7a868
ms.openlocfilehash: 3cd435e57e599649591e8754889ec6a0ae4b77d9


---

# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Azure PowerShell で OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う
Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でのトラブルシューティング手順の実行が必要な場合があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure PowerShell を使用して仮想ハード ディスクを別の Windows VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。


## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 仮想ハード ディスクを保持して、問題が発生している VM を削除します。
2. トラブルシューティングのために、仮想ハード ディスクを別の Windows VM に接続してマウントします。
3. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
4. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
5. 元の仮想ハード ディスクを使用して VM を作成します。

[最新の Azure PowerShell](/powershell/azureps-cmdlets-docs) がインストールされ、サブスクリプションにログインしていることを確認します。

```powershell
Login-AzureRMAccount
```

以下の例では、パラメーター名を独自の値に置き換えてください。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。


## <a name="determine-boot-issues"></a>起動の問題を特定する
起動の問題のトラブルシューティングに役立つ VM のスクリーンショットを Azure で表示できます。 このスクリーンショットは、VM が起動に失敗した理由の特定に役立ちます。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の Windows VM からスクリーンショットを取得します。

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

スクリーンショットを調べて、VM が起動できない理由を特定します。 特定のエラー メッセージまたはエラー コードが表示されていないかどうか確認してください。


## <a name="view-existing-virtual-hard-disk-details"></a>既存の仮想ハード ディスクの詳細を表示する
仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスク (VHD) の名前を確認しておく必要があります。

次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM の情報を取得します。

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

前のコマンドの出力から、`StorageProfile` セクション内の `Vhd URI` を探します。 次の出力例 (一部省略) では、コード ブロックの末尾付近に `Vhd URI` が示されています。

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>既存の VM を削除する
Azure では、仮想ハード ディスクと VM は 2 つの異なるリソースです。 仮想ハード ディスクには、オペレーティング システム自体、アプリケーション、構成が格納されています。 VM 自体は、サイズや場所を定義し、仮想ハード ディスクや仮想ネットワーク インターフェイス カード (NIC) などのリソースを参照するメタデータにすぎません。 各仮想ハード ディスクには、VM に接続されたときにリースが割り当てられています。 データ ディスクは、VM の実行中でも接続および切断できますが、OS ディスクは、VM リソースを削除しない限り、切断することはできません。 VM が停止され、割り当てが解除された状態であっても、リースによって OS ディスクは引き続きその VM に関連付けられています。

VM を回復するには、まず VM リソース自体を削除します。 VM を削除しても、仮想ハード ディスクはストレージ アカウントに残されます。 VM を削除したら、仮想ハード ディスクを別の VM に接続してトラブルシューティングを行い、エラーを解決します。

次の例では、`myResourceGroup` という名前のリソース グループから `myVM` という名前の VM を削除します。

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

VM の削除が完了するまで待ってから、仮想ハード ディスクを別の VM に接続します。 仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスクを VM に関連付けているリースを解放しておく必要があります。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>既存の仮想ハード ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクの内容を参照して編集するために、既存の仮想ハード ディスクをこのトラブルシューティング用 VM に接続します。 このプロセスにより、構成エラーの修正や、その他のアプリケーション ログ ファイルまたはシステム ログ ファイルの確認などが可能になります。 トラブルシューティングに使用する別の VM を選択または作成します。

既存の仮想ハード ディスクを接続するときは、前の `Get-AzureRmVM` コマンドで取得したディスクの URL を指定します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVMRecovery` という名前のトラブルシューティング用 VM に既存の仮想ハード ディスクを接続します。

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> ディスクの追加では、ディスクのサイズを指定する必要があります。 既存のディスクを接続した場合、`-DiskSizeInGB` は `$null` として指定されます。 この値により、データ ディスクが正しく接続され、データ ディスクの実際のサイズを決定する必要がなくなります。


## <a name="mount-the-attached-data-disk"></a>接続されたデータ ディスクをマウントする

1. 適切な資格情報を使用して、トラブルシューティング用 VM に RDP 接続します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVMRecovery` という名前の VM の RDP 接続ファイルを `C:\Users\ops\Documents` にダウンロードします。

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. データ ディスクが自動的に検出され、接続されます。 接続されたボリュームの一覧を表示して、次のようなドライブ文字を特定します。

    ```powershell
    Get-Disk
    ```

    次の出力例は、ディスク **2** に接続された仮想ハード ディスクを示しています。 (`Get-Volume` を使用して、ドライブ文字を表示することもできます):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>元の仮想ハード ディスクで問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 問題に対処したら、次の手順に進みます。


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>元の仮想ハード ディスクのマウントを解除して切断する
エラーが解決したら、既存の仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. RDP セッション内から、復旧 VM でデータ ディスクのマウントを解除します。 前の `Get-Disk` コマンドレットで取得したディスク番号が必要です。 次に、`Set-Disk` を使用して、ディスクをオフラインとして設定します。

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    `Get-Disk` をもう一度使用して、ディスクがオフラインとして設定されていることを確認します。 次の出力例は、ディスクがオフラインとして設定されていることを示しています。

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. RDP セッションを終了します。 Azure PowerShell セッションで、トラブルシューティング用 VM から仮想ハード ディスクを削除します。

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>元のハード ディスクから VM を作成する
元の仮想ハード ディスクから VM を作成するには、[この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet)を使用します。 実際の JSON テンプレートは次のリンクにあります。

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

このテンプレートでは、以前のコマンドで取得した VHD の URL を使用して、VM を既存の仮想ネットワークにデプロイします。 次の例では、`myResourceGroup` という名前のリソース グループにテンプレートをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

テンプレートのプロンプトに従って、VM 名、OS の種類、VM サイズを入力します。 `osDiskVhdUri` は、既存の仮想ハード ディスクをトラブルシューティング用 VM に接続したときに使用したものと同じです。


## <a name="re-enable-boot-diagnostics"></a>ブート診断を再度有効にする

既存の仮想ハード ディスクから VM を作成したときに、ブート診断が自動的に有効にならない場合があります。 次の例では、`myResourceGroup` という名前のリソース グループの `myVMDeployed` という名前の VM で診断拡張機能を有効にします。

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>次のステップ
VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。


<!--HONumber=Dec16_HO3-->


