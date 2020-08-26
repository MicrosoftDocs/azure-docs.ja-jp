---
title: Azure Service Fabric のプライマリ ノード タイプをスケールアップする
description: ノードの種類を追加して Service Fabric クラスターをスケーリングする方法について説明します。
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: eecf398359470f6e5e151c53eb63b3cb56efbe39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056756"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>ノードの種類を追加して Service Fabric クラスターのプライマリ ノード タイプをスケールアップする
この記事では、クラスターにノードの種類を追加して Service Fabric クラスターのプライマリ ノード タイプをスケールアップする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。

次のチュートリアル内のサンプル テンプレートは、[Service Fabric のプライマリ ノード タイプのスケーリング サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)にあります。

> [!WARNING]
> クラスターの状態が異常な場合は、プライマリ ノード タイプのスケールアップ手順を試行しないでください。クラスターがさらに不安定になります。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>プライマリ ノード タイプのサイズとオペレーティング システムをアップグレードするためのプロセス
次に示すのは、プライマリ ノード タイプの VM の VM サイズとオペレーティング システムを更新するためのプロセスです。  アップグレードの後、プライマリ ノード タイプの VM は Standard D4_V2 のサイズになり、コンテナー搭載 Windows Server 2019 Datacenter を実行します。

> [!WARNING]
> 運用クラスターでこの手順を実行する前に、サンプル テンプレートを調べて、テスト クラスターに対してプロセスを検証することをお勧めします。 クラスターが短期間使用できなくなる場合もあります。 

### <a name="deploy-the-initial-service-fabric-cluster"></a>初期の Service Fabric クラスターをデプロイする 
サンプルに沿って実行する場合は、1 つのプライマリ ノード タイプを持つ初期のクラスターと、1 つのスケール セット [Service Fabric - 初期のクラスター](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json)をデプロイします。 既存の Service Fabric クラスターが既にデプロイされている場合は、この手順をスキップできます。 

1. Azure アカウントにログインします。 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. 新しいリソース グループを作成します。 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. テンプレート ファイル内のパラメーター値を入力します。 
4. 手順 2. で作成されたリソース グループにクラスターをデプロイします。 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>クラスターに新しいプライマリ ノード タイプを追加する
> [!Note]
> 次の手順で作成されたリソースは、スケーリング操作が完了すると、クラスター内の新しいプライマリ ノード タイプになります。 初期のサブネット、パブリック IP、ロード バランサー、仮想マシン スケール セット、ノードの種類から、必ず一意の名前を使用するようにしてください。 

次のすべての手順が完了したテンプレートは、[Service Fabric - 新しいノードの種類のクラスター](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json)で見つけることができます。 次の手順には、新しいリソースの変更が強調表示された部分的なリソース スニペットが含まれています。  

1. 既存の仮想ネットワーク内に新しいサブネットを作成します。
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. 一意の domainNameLabel で新しいパブリック IP リソースを作成します。 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. 上で作成されたパブリック IP に依存する新しいロード バランサー リソースを作成します。 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. 新しい VM SKU とスケールアップ先の OS SKU を使用する新しい仮想マシン スケール セットを作成します。 

ノードの種類の参照 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM の SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. 上で作成された仮想マシン スケール セットを参照する新しいノードの種類をクラスターに追加します。 このノードの種類の **isPrimary** プロパティは true に設定する必要があります。 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. 更新された ARM テンプレートをデプロイします。 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

デプロイが完了すると、Service Fabric クラスターにはノードの種類が 2 つ存在するようになります。 

### <a name="remove-the-existing-node-type"></a>既存のノードの種類を削除する 
リソースのデプロイが完了したら、元のプライマリ ノード タイプのノードを無効にしていくことができます。 ノードを無効にすると、システム サービスが、上記の手順でデプロイされた新しいプライマリ ノード タイプに移行されます。

1. Service Fabric クラスター リソースのプライマリ ノード タイプ プロパティを false に設定します。 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. 元のノードの種類の更新された isPrimary プロパティを使用してテンプレートをデプロイします。 元のノードの種類のプライマリ フラグが false に設定されたテンプレートは、[Service Fabric - プライマリ ノード タイプ false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json) で見つけることができます。

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. ノードの種類 0 のノードを無効にします。 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* ブロンズの持続性の場合は、すべてのノードが無効な状態になるまで待ちます。
* シルバーおよびゴールドの持続性の場合は、一部のノードが無効になり、残りは無効化中の状態になります。 無効化中の状態のノードの詳細タブを確認し、インフラストラクチャ サービス パーティションのクォーラムを確保しているときにそれらがすべて停止している場合は、続行しても安全です。

> [!Note]
> この手順は完了に時間がかかる場合があります。 

4. ノードの種類 0 のデータを停止します。 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. 元の仮想マシン スケール セット内のノードの割り当てを解除します。 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Standard SKU パブリック IP と Standard SKU ロード バランサーを既に使用している場合、手順 6. と 7. は省略可能です。 この場合は、同じロード バランサーのもとで仮想マシン スケール セット/ノードの種類を複数使用できます。 

6. これで、元の IP およびロード バランサー リソースを削除できるようになりました。 この手順では、DNS 名も更新します。 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. 新しい IP を参照するようにクラスター上の管理エンドポイントを更新します。 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. ノードの種類 0 からノードの状態を削除します。
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. ARM テンプレート内の Service Fabric リソースから元のノードの種類の参照を削除します。 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
シルバー以上の持続性クラスターの場合のみ、テンプレート内のクラスター リソースを更新し、次に示すように、クラスター リソースのプロパティの下に applicationDeltaHealthPolicies を追加することによって fabric:/System のアプリケーションの正常性を無視するように正常性ポリシーを構成します。 以下のポリシーでは既存のエラーが無視されますが、新しい正常性エラーは許可されません。
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. ARM テンプレートから、元のノードの種類に関連するその他のすべてのリソースを削除します。 これらの元のリソースがすべて削除されたテンプレートについては、[Service Fabric - 新しいノードの種類のクラスター](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json)に関するページを参照してください。

11. 変更した Azure Resource Manager テンプレートをデプロイします。 ** この手順にはしばらく時間がかかります (通常は最大 2 時間)。 このアップグレードでは、InfrastructureService に対する設定が変更されるため、ノードの再起動が必要になります。 この場合、forceRestart は無視されます。 パラメーター upgradeReplicaSetCheckTimeout は、パーティションが安全な状態になるまで Service Fabric が待機する最大時間を指定します (まだ安全な状態でない場合)。 ノード上のすべてのパーティションが安全性チェックに合格すると、Service Fabric はそのノードでアップグレードを進めます。 パラメーター upgradeTimeout の値は 6 時間まで短縮できますが、最大の安全性を確保するには 12 時間を使用する必要があります。
その後、ポータルの Service Fabric リソースが準備完了と表示されていることを確認します。 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

これで、クラスターのプライマリ ノード タイプがアップグレードされました。 デプロイされたアプリケーションが正常に機能すること、クラスターの正常性に問題がないことを確認します。

## <a name="next-steps"></a>次のステップ
* [クラスターにノード タイプを追加する](virtual-machine-scale-set-scale-node-type-scale-out.md)方法について学習します。
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターのスケールインまたはスケールアウトします](service-fabric-cluster-windows-server-add-remove-nodes.md)。
