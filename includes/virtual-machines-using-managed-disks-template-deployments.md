---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261348"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの管理ディスクの使用

このドキュメントでは、Azure Resource Manager テンプレートを使って仮想マシンをプロビジョニングする際のマネージド ディスクと非管理対象ディスクの違いについて説明します。 以下の例は、非管理対象ディスクが使用されている既存のテンプレートを管理ディスクに更新する際にお役立てください。 ここでは参考として [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) テンプレートを使用しています。 [マネージド ディスク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json)を使用したテンプレートと[非管理対象ディスク](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json)を使用した以前のバージョンを見て直接比較することもできます。

## <a name="unmanaged-disks-template-formatting"></a>非管理対象ディスクのテンプレートの書式

最初に、非管理対象ディスクのデプロイ方法について見てみましょう。 非管理対象ディスクを作成するときは、VHD ファイルを保持するストレージ アカウントが必要です。 ストレージ アカウントは新たに作成しても、既にあるものを使用してもかまいません。 この記事では、新しいストレージ アカウントの作成方法を紹介します。 次に示すように、resources ブロックでストレージ アカウント リソースを作成します。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

仮想マシン オブジェクト内で、ストレージ アカウントへの依存関係を追加します。ストレージ アカウントが仮想マシンより前に作成されるようにするためです。 `storageProfile` セクション内に、VHD がある場所の完全 URI を指定します。この URI はストレージ アカウントを参照するもので、OS ディスク用とデータ ディスク用に必要となります。

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

## <a name="managed-disks-template-formatting"></a>マネージド ディスクのテンプレートの書式

Azure Managed Disks を使用した場合、そのディスクが最上位のリソースとなり、ユーザーによって作成されるストレージ アカウントは不要となります。 マネージド ディスクは、最初 `2016-04-30-preview` の API バージョンで公開され、その後のすべての API バージョンで使用でき、既定のディスク タイプになりました。 以降のセクションでは、既定の設定とディスクのカスタマイズ方法について詳しく見ていきます。

> [!NOTE]
> `2016-04-30-preview` と `2017-03-30` の間で大きな変更があったため、`2016-04-30-preview` 以降の API バージョンを使うことをお勧めします。
>
>

### <a name="default-managed-disk-settings"></a>マネージド ディスクの既定の設定

今後、マネージド ディスクを使用して VM を作成する場合、ストレージ アカウント リソースを作成する必要はありません。既存の仮想マシン リソースは次のように更新することができます。 具体的には、`apiVersion` に `2017-03-30` が反映され、`osDisk` と `dataDisks` には、VHD の具体的な URI が指定されていません。 追加のプロパティを指定せずにデプロイすると、ディスクでは VM のサイズに基づいてストレージの種類が使用されます。 たとえば、Premium 対応の VM サイズ (Standard_D2s_v3 など、名前に "s" が付くサイズ) が使用されている場合、システムに Premium_LRS ストレージが使用されます。 ディスクの sku 設定を使用してストレージの種類を指定します。 名前を指定しなかった場合、OS ディスクには `<VMName>_OsDisk_1_<randomstring>` 形式の名前が、各データ ディスクには `<VMName>_disk<#>_<randomstring>` 形式の名前が付きます。 既定では、Azure Disk Encryption が無効になり、キャッシュは、OS ディスクの場合は "読み取り/書き込み" に、データ ディスクの場合は "なし" になります。 下の例を見るとわかるように、ストレージ アカウントの依存関係は依然として存在します。ただし、これはあくまで診断のストレージ用であって、ディスク ストレージに必要なものではありません。

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

### <a name="using-a-top-level-managed-disk-resource"></a>最上位のマネージド ディスク リソースの使用

仮想マシン オブジェクトにディスク構成を指定する代わりに、最上位のディスク リソースを作成して、それを仮想マシン作成の過程でアタッチすることもできます。 たとえば、データ ディスクとして使用するディスク リソースは次のように作成できます。

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
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

VM オブジェクト内で、アタッチするディスク オブジェクトを参照します。 作成した管理ディスクのリソース ID を `managedDisk` プロパティに指定しておけば、VM が作成されるときにディスクがアタッチされます。 VM リソースの `apiVersion` は `2017-03-30` に設定されています。 VM の作成前にディスク リソースが作成されるように、ディスク リソースへの依存関係が追加されます。 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>マネージド ディスクを使った VM で構成される管理された可用性セットを作成する

マネージド ディスクを使った VM で構成される管理された可用性セットを作成するには、可用性セット リソースに `sku` オブジェクトを追加し、その `name` プロパティを `Aligned` に設定します。 このプロパティにより、各 VM のディスクが単一障害点とならないよう互いに分離されます。 また、可用性セット リソース の `apiVersion` は `2017-03-30` に設定されています。

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
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

### <a name="standard-ssd-disks"></a>Standard SSD ディスク

Resource Manager テンプレートで Standard SSD ディスクを作成するには、次のパラメーターが必要です。

* Microsoft.Compute の *apiVersion* を `2018-04-01` (またはそれ以降) として設定する必要があります。
* `StandardSSD_LRS` として *managedDisk.storageAccountType* を指定します。

次の例は、Standard SSD ディスクを使用する VM の *properties.storageProfile.osDisk* セクションを示したものです。

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

テンプレートを使用して Standard SSD ディスクを作成する方法における完全なテンプレート例については、[Standard SSD データ ディスクを含む Windows イメージから VM を作成](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)に関するページを参照してください。

### <a name="additional-scenarios-and-customizations"></a>その他のシナリオとカスタマイズ

REST API の仕様の詳細については、[マネージド ディスク作成の REST API に関するドキュメント](/rest/api/manageddisks/disks/disks-create-or-update)を参照してください。 その他のシナリオや、テンプレートのデプロイ時に API に送信できる既定値や許容値についても説明されています。 

## <a name="next-steps"></a>次の手順

* マネージド ディスクを使用した完全なテンプレートについては、次の Azure クイック スタート リポジトリのリンクを参照してください。
    * [マネージド ディスクを使用した Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [マネージド ディスクを使用した Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [マネージド ディスク テンプレートの全一覧](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* マネージド ディスクの詳細については、「[Azure Managed Disks の概要](../articles/virtual-machines/windows/managed-disks-overview.md)」のドキュメントを参照してください。
* 仮想マシン リソースのテンプレートについては、[Microsoft.Compute/virtualMachines テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachines) ドキュメントを参照してください。
* ディスク リソースのテンプレートについては、[Microsoft.Compute/disks テンプレート リファレンス](/azure/templates/microsoft.compute/disks) ドキュメントを参照してください。
* Azure VM Scale Sets で管理ディスクを使う方法については、[スケール セットでデータ ディスクを使用する](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks)方法に関する文書を参照してください。
