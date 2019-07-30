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
ms.openlocfilehash: ae1cd0912733116dce1b550dd937cc9fc5f8737b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359773"
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
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure Virtual Machine オペレーティング システムの自動アップグレード構成 
仮想マシンのアップグレードは、ユーザーによって開始される操作です。Azure Service Fabric クラスター ホストの更新プログラム管理には、[仮想マシン スケール セットによる自動オペレーティング システム アップグレード](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)を使用することをお勧めします。パッチ オーケストレーション アプリケーションを Azure で使用することはできますが、Azure で POA をホストするオーバーヘッドのために、一般的には POA よりも仮想マシン オペレーティング システム自動アップグレードが選ばれるので、POA は Azure の外部でホストする場合の代替ソリューションです。 以下は、自動 OS アップグレードを有効にするための、コンピューティング仮想マシン スケール セット Resource Manager テンプレートのプロパティです。

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Service Fabric と OS 自動アップグレードを使用している場合、Service Fabric で実行されているサービスの高可用性を維持するために、新しい OS イメージは更新ドメインごとに 1 つずつロールアウトされます。 Service Fabric で OS 自動アップグレードを利用するには、クラスターが、Silver 以上の耐久性レベルを使用するように構成されている必要があります。

次のレジストリ キーが false に設定されていることを確認して、調整されていない更新を Windows ホスト マシンが開始しないようにします。HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU。

以下は、WindowsUpdate レジストリ キーを false に設定するための、コンピューティング仮想マシン スケール セット Resource Manager テンプレートのプロパティです。
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric クラスターのアップグレード構成
以下は、自動アップグレードを有効にするための、Service Fabric クラスター Resource Manager テンプレートのプロパティです。
```json
"upgradeMode": "Automatic",
```
クラスターを手動でアップグレードするには、cab/deb 配布をクラスター仮想マシンにダウンロードして、次の PowerShell を実行します。
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
