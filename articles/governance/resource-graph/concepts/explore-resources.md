---
title: Azure リソースの探索
description: Resource Graph のクエリ言語を使用して、リソースを探索し、どのように接続されているかを確認する方法について学習します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 3174e74dc3fb8c56279c0c9708a67d99ae19724a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795972"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Resource Graph を使用してご利用の Azure リソースを探索する

Azure Resource Graph では、し、Azure リソースを迅速かつ大規模に探索および確認する機能が提供されています。 迅速に応答するよう設計されており、ご利用の環境のほか、Azure リソースを構成するプロパティの詳細を確認する優れた手段となっています。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>仮想マシンの詳細を確認する

Azure の一般的なリソースは仮想マシンです。 リソースの一種である仮想マシンは、クエリの対象になる多数のプロパティを備えています。 各プロパティには、探しているリソースだけをフィルター処理して見つけるためのオプションがあります。

### <a name="virtual-machine-discovery"></a>仮想マシンの確認

まずは、環境から 1 つの VM を取得し、返されるプロパティを確認する簡単なクエリを実行してみましょう。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` コマンドレットは、既定で **PSCustomObject** を返します。 Azure CLI から返される内容と同様の出力にするには、`ConvertTo-Json` コマンドレットを使用します。 **Depth** の既定値は _2_ です。 _100_ に設定すると、返されたすべてのレベルが変換されます。

JSON 結果は次の例のように構成されます。

```json
[
  {
    "aliases": {
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": "WindowsServer",
      "Microsoft.Compute/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/imageSku": "2016-Datacenter",
      "Microsoft.Compute/imageVersion": "latest",
      "Microsoft.Compute/licenseType": null,
      "Microsoft.Compute/virtualMachines/availabilitySet.id": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.enabled": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.storageUri": null,
      "Microsoft.Compute/virtualMachines/imageOffer": "WindowsServer",
      "Microsoft.Compute/virtualMachines/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/virtualMachines/imageSku": "2016-Datacenter",
      "Microsoft.Compute/virtualMachines/imageVersion": "latest",
      "Microsoft.Compute/virtualMachines/networkInterfaces[*].id": [
        "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535"
      ],
      "Microsoft.Compute/virtualMachines/osDisk.Uri": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminPassword": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminUsername": "localAdmin",
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration": null,
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration.disablePasswordAuthentication": null,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration": {
        "enableAutomaticUpdates": true,
        "provisionVMAgent": true
      },
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.enableAutomaticUpdates": true,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.provisionVMAgent": true,
      "Microsoft.Compute/virtualMachines/sku.name": "Standard_B2s",
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].caching": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].createOption": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].diskSizeGB": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].image.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].lun": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.id": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.storageAccountType": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].name": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].vhd.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.caching": "ReadWrite",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.createOption": "FromImage",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings.enabled": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType": "Premium_LRS",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.osType": "Windows",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd.uri": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

**aliases** の下にある最初のプロパティのセットは、関連するさまざまなプロパティ値を示しています。 エイリアスに関する詳細情報を得て、使用できるエイリアスを確認するには、「[Azure Policy definition structure - Aliases](../../policy/concepts/definition-structure.md#aliases)」(Azure Policy の定義の構造 - エイリアス) を参照してください。 エイリアスは、組織の規則とガバナンスへのコンプライアンスを管理するために、主に Azure Policy で使用されます。

その他のプロパティは、仮想マシン リソース自体に関する追加情報 (SKU、OS、ディスク、タグなどのすべての情報)、およびリソースが属しているリソース グループとサブスクリプションに関する追加情報を提供します。

### <a name="virtual-machines-by-location"></a>場所別の仮想マシン

仮想マシンのリソースについて学習した内容を念頭に置き、**location** プロパティ使用して、すべての仮想マシンを場所別にカウントしてみましょう。 クエリを更新するために、制限を削除し、場所の値の数を集計します。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON 結果は次の例のように構成されます。

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

これで、各 Azure リージョンにある仮想マシンの数を確認できました。

### <a name="virtual-machines-by-sku"></a>SKU 別の仮想マシン

仮想マシンの元のプロパティに戻って、SKU サイズが **Standard_B2s** であるすべての仮想マシンを見つけましょう。 返される JSON を確認すると、**properties.hardwareprofile.vmsize** に格納されることがわかります。 このサイズと一致するすべての VM を見つけて、VM およびリージョンの名前だけを返すように、クエリを更新します。

```Query
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>プレミアム マネージド ディスクに接続された仮想マシン

これらの **Standard_B2S** 仮想マシンに接続されているプレミアム マネージド ディスクの詳細情報を得るには、これらのマネージド ディスクのリソース ID を提供するようにクエリを拡張します。

```Query
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> SKU を取得するもう 1 つの方法としては、**aliases** プロパティ **Microsoft.Compute/virtualMachines/sku.name** を使用します。

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

結果は、ディスク ID の一覧です。

### <a name="managed-disk-discovery"></a>マネージド ディスクの確認

前のクエリから最初のレコードを取得して、最初の仮想マシンに接続されていたマネージド ディスクに存在するプロパティを確認します。 更新されたクエリでは、ディスク ID を使用し、種類を変更します。

前のクエリからの出力例を次に示します。

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```Query
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

クエリを実行する前に、**type** が **Microsoft.Compute/disks** であることがなぜわかるかを見てみましょう。
完全な ID を確認すると、文字列の一部が **/providers/Microsoft.Compute/disks/** となっています。 この文字列の部分から、検索対象となる種類がわかります。 別の方法としては、種類による制限を削除し、代わりに ID フィールドだけを使用して検索を行います。 ID は一意なので、1 つのレコードだけが返され、含まれる **type** プロパティによってその詳細情報が提供されます。

> [!NOTE]
> この例を動作させるには、ID フィールドを、独自の環境の結果に置き換える必要があります。

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON 結果は次の例のように構成されます。

```json
[
  {
    "aliases": {
      "Microsoft.Compute/disks/sku.name": "Premium_LRS",
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": null,
      "Microsoft.Compute/imagePublisher": null,
      "Microsoft.Compute/imageSku": null,
      "Microsoft.Compute/imageVersion": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>仮想マシンを探索してパブリック IP アドレスを確認する

この Azure CLI の一連のクエリは、最初に仮想マシンに接続されているすべてのネットワーク インターフェイス (NIC) リソースを検索し、それらを格納します。 次に、NIC の一覧を使用して、パブリック IP アドレスである各 IP アドレスのリソースを検索し、それらの値を格納します。 最後に、パブリック IP アドレスの一覧を提供します。

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

次のクエリの中で `nics.txt` ファイルを使用して、関連するネットワーク インターフェイス リソースの詳細情報を取得します。これには、NIC にアタッチされているパブリック IP アドレスが含まれます。

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

最後に、`ips.txt` に格納されているパブリック IP アドレス リソースの一覧を使用して、そこから実際のパブリック IP アドレスを取得して表示します。

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>次の手順

- [クエリ言語](query-language.md)の詳細について学習します
- [初歩的なクエリ](../samples/starter.md)で使用されている言語を参照してください
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を参照してください