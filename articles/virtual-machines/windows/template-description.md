---
title: Azure Resource Manager テンプレートの仮想マシン | Microsoft Azure
description: Azure Resource Manager テンプレートで仮想マシン リソースがどのように定義されるかについて説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 32b73be3faf6eedb92220725b292a3e69cf7f965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555994"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの仮想マシン

この記事では、仮想マシンに適用される、Azure Resource Manager テンプレートの側面について説明します。 仮想マシンを作成するための完全なテンプレートについては、この記事では説明しません。完全なテンプレートには、ストレージ アカウント、ネットワーク インターフェイス、パブリック IP アドレス、および仮想ネットワークのリソース定義が必要です。 これらのリソースをまとめて定義できる方法の詳細については、「[Resource Manager テンプレートのチュートリアル](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。

VM リソースを含め、[ギャラリーにはテンプレート](https://azure.microsoft.com/documentation/templates/?term=VM)が多数あります。 テンプレートに含めることができるすべての要素をここで説明するわけではありません。

 

次の例では、指定された数の VM を作成するためのテンプレートの典型的なリソース セクションを示しています。

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>この例では、以前に作成したストレージ アカウントが使用されています。 このストレージ アカウントは、テンプレートからデプロイして作成することができました。 この例では、テンプレートで定義されるネットワーク インターフェイスとその依存リソースも使用されています。 これらのリソースは、この例では示されていません。
>
>

## <a name="api-version"></a>API バージョン

テンプレートを使用してリソースをデプロイする際に、使用する API のバージョンを指定する必要があります。 この例では、次の apiVersion 要素を使用して仮想マシン リソースを示しています。

```json
"apiVersion": "2016-04-30-preview",
```

テンプレートで指定した API のバージョンに応じて、テンプレートで定義できるプロパティが変わります。 通常は、テンプレートを作成するときに、最新の API バージョンを選択する必要があります。 既存のテンプレートの場合、以前の API バージョンの使用を続けるか、テンプレートを最新版に更新して新しい機能を最大限に活用するか決定できます。

最新の API バージョンを取得するには、次の方法を使用してください。

- REST API - [すべてのリソース プロバイダーの一覧を表示する](/rest/api/resources/providers)
- PowerShell - [Get AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>パラメーターと変数

[パラメーター](../../azure-resource-manager/templates/template-syntax.md)を使用すると、テンプレートの実行時にテンプレートに値を指定することが簡単になります。 この例では、次の parameters セクションが使用されています。

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

サンプル テンプレートをデプロイするときに、各 VM の管理者アカウントの名前とパスワード、作成する VM の数の値を入力します。 テンプレートと共に管理される別のファイルでパラメーター値を指定したり、要求時に値を指定したりすることもできます。

[変数](../../azure-resource-manager/templates/template-syntax.md)を使用すると、テンプレート全体で繰り返し使用される値や時間の経過と共に変化する可能性がある値を、テンプレートで簡単に設定できます。 この例では、次の variables セクションが使用されています。

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

サンプル テンプレートをデプロイすると、以前に作成したストレージ アカウントの名前と識別子に変数の値が使用されています。 変数は、診断拡張機能の設定の指定にも使用されています。 [Azure Resource Manager テンプレートを作成するためのベスト プラクティス](../../azure-resource-manager/templates/template-best-practices.md)に関する記事を参照すると、テンプレートでパラメーターと変数をどのように構成するかを決めるのに役立ちます。

## <a name="resource-loops"></a>リソース ループ

アプリケーションに複数の仮想マシンが必要な場合は、テンプレートの copy 要素を使用できます。 この省略可能な要素は、パラメーターとして指定した数の VM が作成されるまでループされます。

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

また、この例では、リソースのいくつかの値を指定する際にループ インデックスが使用されていることにも注意してください。 たとえば、インスタンス数として 3 を入力した場合、オペレーティング システム ディスクの名前は、myOSDisk1、myOSDisk2、myOSDisk3 となります。

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>この例では、仮想マシンのマネージド ディスクを使用します。
>
>

テンプレート内の 1 つのリソースに対してループを作成すると、他のリソースの作成時や他のリソースへのアクセス時にそのループを使用する必要が生じる可能性があることに注意してください。 たとえば、複数の VM では同じネットワーク インターフェイスを使用できないため、テンプレートでループして 3 つの VM を作成する場合は、3 つのネットワーク インターフェイスもループによって作成しなければなりません。 VM にネットワーク インターフェイスを割り当てる際は、それらを識別するためにループ インデックスが使用されます。

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>依存関係

ほとんどのリソースは、正常に動作するために他のリソースに依存しています。 仮想マシンは仮想ネットワークに関連付ける必要があり、そのためにはネットワーク インターフェイスが必要です。 [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) 要素は、VM が作成される前にネットワーク インターフェイスを使用できるようするために使用されます。

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager は、デプロイ中の他のリソースに依存していないすべてのリソースを並行してデプロイします。 不要な依存関係を指定すると、意図せずにデプロイの速度を低下させる可能性があるため、依存関係を設定する際は注意してください。 依存関係により、複数のリソースを連結できます。 たとえば、ネットワーク インターフェイスは、パブリック IP アドレスと仮想ネットワーク リソースに依存します。

依存関係が必要かどうかは、どのように判断するのでしょうか。 テンプレートで設定した値を確認してください。 仮想マシン リソース定義内の要素が、同じテンプレートでデプロイされる他のリソースを指している場合は、依存関係が必要です。 たとえば、このサンプルの仮想マシンは、次のようにネットワーク プロファイルを定義しています。

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

このプロパティを設定するには、ネットワーク インターフェイスが存在する必要があります。 したがって、依存関係が必要です。 1 つのリソース (子) が他のリソース (親) の中で定義されている場合も、依存関係を設定する必要があります。 たとえば、診断設定とカスタム スクリプト拡張機能は両方とも、仮想マシンの子リソースとして定義されています。 これらは、仮想マシンが存在しないうちは、作成することができません。 そのため、両方のリソースは、仮想マシンに依存しているとマークされます。

## <a name="profiles"></a>Profiles

いくつかのプロファイル要素は、仮想マシン リソースを定義する際に使用されます。 必須のものもあれば、省略可能なものもあります。 たとえば、hardwareProfile、osProfile、storageProfile、networkProfile 要素は必須で、diagnosticsProfile 要素は省略可能です。 これらのプロファイルは、次のような設定を定義します。
   
- [size](../sizes.md)
- [名前](/azure/architecture/best-practices/resource-naming)と資格情報
- ディスクと[オペレーティング システムの設定](cli-ps-findimage.md)
- [ネットワーク インターフェイス](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- ブート診断

## <a name="disks-and-images"></a>ディスクとイメージ
   
Azure では、vhd ファイルは[ディスクまたはイメージ](../managed-disks-overview.md)を表すことができます。 vhd ファイルのオペレーティング システムは、特定の VM に特化している場合はディスクと呼ばれます。 vhd ファイルのオペレーティング システムが、多くの VM の作成に使用されるように一般化されている場合は、イメージと呼ばれます。   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>プラットフォーム イメージから新しい仮想マシンと新しいディスクを作成する

VM を作成する場合は、どのオペレーティング システムを使用するかを決める必要があります。 新しい VM のオペレーティング システムを定義するには、imageReference 要素を使用します。 次の例では、Windows Server オペレーティング システムの定義を示しています。

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Linux オペレーティング システムを作成する場合は、次の定義を使用できます。

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

オペレーティング システム ディスクの構成設定は、osDisk 要素で割り当てられます。 この例では、キャッシュ モードが **ReadWrite** に設定された新しいマネージド ディスクを定義し、ディスクが [プラットフォーム イメージ](cli-ps-findimage.md)から作成されるようにします。

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>既存のマネージド ディスクから新しい仮想マシンを作成する

既存のディスクから仮想マシンを作成する場合は、imageReference 要素と osProfile 要素を削除し、次のディスク設定を定義します。

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>管理イメージから新しい仮想マシンを作成する

管理イメージから仮想マシンを作成する場合は、imageReference 要素を変更し、次のディスク設定を定義します。

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>データ ディスクを接続する

必要に応じて、VM にデータ ディスクを追加することができます。 [ディスク数](../sizes.md)は、使用するオペレーティング システム ディスクのサイズによって異なります。 VM のサイズが Standard_DS1_v2 に設定されている場合、VM に追加できるデータ ディスクの最大数は 2 です。 次の例では、各 VM に 1 つのマネージド データ ディスクが追加されます。

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>拡張機能

[拡張機能](../extensions/features-windows.md)は個別のリソースですが、VM に密接に関係しています。 拡張機能は、VM の子リソース、または個別のリソースとして追加できます。 次の例は、VM に追加される[診断の拡張機能](../extensions/diagnostics-template.md)を示しています。

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

この拡張機能リソースは、storageName 変数と診断変数を使用して値を指定します。 この拡張機能で収集するデータを変更する場合は、wadperfcounters 変数にさらに多くのパフォーマンス カウンターを追加できます。 また、VM ディスクの格納場所とは異なるストレージ アカウントに診断データを格納することもできます。

VM にインストールできる拡張機能は多数ありますが、最も便利なのは、おそらく[カスタム スクリプト拡張機能](../extensions/custom-script-windows.md)でしょう。 次の例では、start.ps1 という名前の PowerShell スクリプトが、各 VM で起動時に実行されます。

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

start.ps1 スクリプトは、多くの構成タスクを実行できます。 たとえば、この例で VM に追加されるデータ ディスクは、初期化されていないため、カスタム スクリプトを使用して初期化することができます。 実行するスタートアップ タスクが複数ある場合は、start.ps1 ファイルを使用して、Azure Storage 内の他の PowerShell スクリプトを呼び出すことができます。 例では PowerShell を使用していますが、お使いのオペレーティング システム上で利用可能な任意のスクリプト手法を使用してかまいません。

インストールした拡張機能の状態は、ポータルの [拡張機能] の設定で確認できます。

![拡張機能の状態の確認](./media/template-description/virtual-machines-show-extensions.png)

また、**Get-AzVMExtension** PowerShell コマンド、**vm extension get** Azure CLI コマンド、または **Get extension information** REST API を使用して、拡張機能の情報を取得することもできます。

## <a name="deployments"></a>デプロイメント

テンプレートをデプロイすると、Azure では、グループとしてデプロイしたリソースが追跡され、このデプロイされたグループに自動的に名前が割り当てられます。 デプロイの名前は、テンプレートの名前と同じです。

デプロイ内のリソースの状態が気になる場合は、Azure portal でリソース グループを表示します。

![デプロイ情報の取得](./media/template-description/virtual-machines-deployment-info.png)
    
リソースの作成や既存のリソースの更新に同じテンプレートを使用しても問題はありません。 コマンドを使用してテンプレートをデプロイする際は、使用する[モード](../../azure-resource-manager/templates/deploy-powershell.md)を指定することができます。 モードは、**Complete** または **Incremental** に設定することができます。 既定では、増分更新が実行されます。 **Complete** モードを使用する場合は、誤ってリソースを削除することがあるため、注意してください。 モードを **Complete** に設定すると、Resource Manager は、テンプレートに含まれていない、リソース グループ内のリソースすべてを削除します。

## <a name="next-steps"></a>次の手順

- 「[Azure Resource Manager のテンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)」を使用して、独自のテンプレートを作成します。
- 「[Resource Manager テンプレートで Windows 仮想マシンを作成する](ps-template.md)」を使用して作成したテンプレートをデプロイします。
- 作成した VM を管理する方法については、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md)」をご覧ください。
- テンプレート内のリソースの種類の JSON 構文とプロパティについては、[Azure Resource Manager テンプレート リファレンス](/azure/templates/)を参照してください。