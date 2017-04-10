## <a name="overview"></a>概要
[Azure Marketplace](https://azure.microsoft.com/marketplace/)からイメージをデプロイすることによってリソース グループに新しい仮想マシン (VM) を作成するとき、既定の OS ドライブは 127 GB です。 VM にデータ ディスクを追加でき (数は選択されている SKU に依存)、アプリケーションおよび CPU 多用ワークロードはこれらの追加ディスクにインストールすることが推奨されますが、次のような特定のシナリオをサポートするために OS ドライブの拡張が必要になることがあります。

1. OS ドライブにコンポーネントをインストールするレガシ アプリケーションをサポートする場合。
2. オンプレミスから OS ドライブの大きい物理 PC または仮想マシンを移行する場合。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデルです。 この記事では、リソース マネージャー モデルの使用について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。
> 
> 

## <a name="resize-the-os-drive"></a>OS ドライブのサイズ変更
この記事では、 [Azure Powershell](/powershell/azureps-cmdlets-docs)のリソース マネージャー モジュールを使用して、OS ドライブのサイズを変更します。 管理者モードで Powershell ISE または Powershell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、次のようにサブスクリプションを選択します。
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. リソース グループ名と VM 名を次のように設定します。
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 次のように、VM への参照を取得します。
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 次のように、ディスクのサイズを変更する前に VM を停止します。
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 次に、 OS ディスクのサイズを目的の値に設定し、VM を更新します。
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 許可される最大値は、1,023 GB です。
   > 
   > 
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了した後、VM を再起動します。
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

これで終了です。 VM に RDP で接続し、[コンピューターの管理] \(または [ディスクの管理]) を開いて、新しく割り当てた領域を使用してドライブを拡張します。

## <a name="summary"></a>概要
この記事では、Powershell の Azure リソース マネージャー モジュールを使用して、IaaS 仮想マシンの OS ドライブを拡張しました。 参考のために完全なスクリプトを以下に示します。

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>次のステップ
この記事では VM の OS ディスクの拡張に主に重点を置きましたが、作成したスクリプトは、コードを 1 行変更するだけで、VM に接続されたデータ ディスクの拡張にも使用できます。 たとえば、VM に接続されている最初のデータ ディスクを拡張するには、```StorageProfile``` の ```OSDisk``` オブジェクトを ```DataDisks``` 配列に置き換え、数値インデックスを使用して接続されている最初のデータ ディスクへの参照を取得します。次に例を示します。

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
同様に、上のようにインデックスを使用して、または以下に示すようにディスクの ```Name``` プロパティを使用して、VM に接続されている他のデータ ディスクを参照できます。

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Azure Resource Manager VM にディスクを接続する方法については、こちらの[記事](../articles/virtual-machines/windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。

