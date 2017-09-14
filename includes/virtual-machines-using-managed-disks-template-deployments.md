# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの管理ディスクの使用

このドキュメントでは、Azure Resource Manager テンプレートを使って仮想マシンをプロビジョニングする際の管理ディスクと非管理対象ディスクの違いについて説明します。 非管理対象ディスクが使用されている既存のテンプレートを管理ディスクに更新する際にお役立てください。 ここでは参考として [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) テンプレートを使用しています。 [管理ディスク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json)を使用したテンプレートと[非管理対象ディスク](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json)を使用した以前のバージョンを見て直接比較することもできます。

## <a name="unmanaged-disks-template-formatting"></a>非管理対象ディスクのテンプレートの書式

最初に、非管理対象ディスクのデプロイ方法について見てみましょう。 非管理対象ディスクを作成するときは、VHD ファイルを保持するストレージ アカウントが必要です。 ストレージ アカウントは新たに作成しても、既にあるものを使用してもかまいません。 この記事では、新しいストレージ アカウントの作成方法を紹介します。 そのためには、次に示すように、resources ブロックにストレージ アカウント リソースが必要です。

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

仮想マシン オブジェクトには、ストレージ アカウントへの依存関係が必要になります。ストレージ アカウントが仮想マシンより前に作成されるようにするためです。 そこで `storageProfile` セクション内に、VHD がある場所の完全 URI を指定します。この URI はストレージ アカウントを参照するもので、OS ディスク用とデータ ディスク用に必要となります。 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>管理ディスクのテンプレートの書式

Azure Managed Disks を使用した場合、そのディスクが最上位のリソースとなり、ユーザーによって作成されるストレージ アカウントは不要となります。 管理ディスクは、最初 `2016-04-30-preview` の API バージョンで公開され、その後のすべての API バージョンで使用でき、既定のディスク タイプになりました。 以降のセクションでは、既定の設定とディスクのカスタマイズ方法について詳しく見ていきます。

> [!NOTE]
> `2016-04-30-preview` と `2017-03-30` の間で大きな変更があったため、`2016-04-30-preview` 以降の API バージョンを使うことをお勧めします。
>
>

### <a name="default-managed-disk-settings"></a>管理ディスクの既定の設定

今後、管理ディスクを使用して VM を作成する場合、ストレージ アカウント リソースを作成する必要はありません。既存の仮想マシン リソースは次のように更新することができます。 具体的には、`apiVersion` に `2017-03-30` が反映され、`osDisk` と `dataDisks` には、VHD の具体的な URI が指定されていません。 デプロイ時に別途プロパティを指定しない限り、ディスクには [Standard LRS ストレージ](../articles/storage/common/storage-redundancy.md)が使用されます。 名前を指定しなかった場合、OS ディスクには `<VMName>_OsDisk_1_<randomstring>` 形式の名前が、各データ ディスクには `<VMName>_disk<#>_<randomstring>` 形式の名前が付きます。 既定では、Azure Disk Encryption が無効になり、キャッシュは、OS ディスクの場合は "読み取り/書き込み" に、データ ディスクの場合は "なし" になります。 下の例を見るとわかるように、ストレージ アカウントの依存関係は依然として存在します。ただし、これはあくまで診断のストレージ用であって、ディスク ストレージに必要なものではありません。

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>最上位の管理ディスク リソースの使用

仮想マシン オブジェクトにディスク構成を指定する代わりに、最上位のディスク リソースを作成して、それを仮想マシン作成の過程でアタッチすることもできます。 たとえばデータ ディスクとして使用するディスク リソースは、次のように作成できます。

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

このディスク オブジェクトは、その後 VM オブジェクト内で参照することでアタッチすることができます。 作成した管理ディスクのリソース ID を `managedDisk` プロパティに指定しておけば、VM が作成されるときにディスクがアタッチされます。 VM リソースの `apiVersion` は `2017-03-30` に設定されています。 また、VM の作成前にディスク リソースが作成されるように、ディスク リソースへの依存関係を作成していることに注意してください。 

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>管理ディスクを使った VM で構成される管理された可用性セットを作成する

管理ディスクを使った VM で構成される管理された可用性セットを作成するには、可用性セット リソースに `sku` オブジェクトを追加し、その `name` プロパティを `Aligned` に設定します。 これにより、各 VM のディスクが単一障害点とならないよう互いに分離されます。 また、可用性セット リソース の `apiVersion` は `2017-03-30` に設定されています。

```
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="additional-scenarios-and-customizations"></a>その他のシナリオとカスタマイズ

REST API の仕様の詳細については、[管理ディスク作成の REST API に関するドキュメント](/rest/api/manageddisks/disks/disks-create-or-update)を参照してください。 その他のシナリオや、テンプレートのデプロイ時に API に送信できる既定値や許容値についても説明されています。 

## <a name="next-steps"></a>次のステップ

* 管理ディスクを使用した完全なテンプレートについては、次の Azure クイック スタート リポジトリのリンクを参照してください。
    * [管理ディスクを使用した Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [管理ディスクを使用した Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [管理ディスク テンプレートの全一覧](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* 管理ディスクの詳細については、「[Azure Managed Disks の概要](../articles/virtual-machines/windows/managed-disks-overview.md)」のドキュメントを参照してください。
* 仮想マシン リソースのテンプレートについては、[Microsoft.Compute/virtualMachines テンプレート リファレンス](/templates/microsoft.compute/virtualmachines) ドキュメントを参照してください。
* ディスク リソースのテンプレートについては、[Microsoft.Compute/disks テンプレート リファレンス](/templates/microsoft.compute/disks) ドキュメントを参照してください。
 
