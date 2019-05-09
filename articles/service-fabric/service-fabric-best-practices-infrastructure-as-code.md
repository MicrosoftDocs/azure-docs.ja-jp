---
title: コードとしての Azure Service Fabric インフラストラクチャに関するベスト プラクティス | Microsoft Docs
description: インフラストラクチャとしての Service Fabric をコードとして管理するためのベスト プラクティスです。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 9224ecebed35a631514c5254703ad2694675d40e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793816"
---
# <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

運用環境のシナリオでは、Resource Manager テンプレートを使用して Azure Service Fabric クラスターを作成します。 Resource Manager テンプレートでは、制御できるリソースのプロパティが多く、一貫性のあるリソース モデルが保証されます。

Windows および Linux 用のサンプルの Resource Manager テンプレートは、[GitHub の Azure サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates)で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。 `azuredeploy.json` と `azuredeploy.parameters.json` をダウンロードし、独自の要件に合わせて編集します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

上でダウンロードした `azuredeploy.json` テンプレートと `azuredeploy.parameters.json` テンプレートをデプロイするには、次の Azure CLI コマンドを使用します。

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

PowerShell を使用してリソースを作成する

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric のリソース

Azure Resource Manager を使用して、Service Fabric クラスターにアプリケーションとサービスを デプロイできます。 詳しくは、「[アプリケーションとサービスを Azure Resource Manager のリソースとして管理する](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource)」をご覧ください。 以下に示すのは、Resource Manager テンプレートのリソースに含める Service Fabric アプリケーション固有のリソースのベスト プラクティスです。

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Resource Manager を使用してアプリケーションをデプロイするには、最初に Service Fabric アプリケーション パッケージ [sfpkg を作成する](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)必要があります。 次の Python スクリプトは、sfpkg を作成する方法の例です。

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
