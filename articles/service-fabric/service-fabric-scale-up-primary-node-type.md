---
title: Azure Service Fabric のプライマリ ノード タイプをスケールアップする
description: 新しいノード タイプを追加し、前のものを削除して、Service Fabric クラスターを垂直方向にスケーリングします。
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251183"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric クラスターのプライマリ ノード タイプをスケールアップする

この記事では、最小限のダウンタイムで Service Fabric クラスターのプライマリ ノード タイプをスケールアップする方法について説明します。 Service Fabric クラスター ノードをアップグレードする一般的な方法は次のとおりです。

1. アップグレードされた (または変更された) 仮想マシン スケール セットの SKU および構成を使用する新しいノード タイプを Service Fabric クラスターに追加します。 この手順では、スケール セットの新しいロード バランサー、サブネット、およびパブリック IP の設定も必要になります。

1. 元のスケール セットとアップグレードしたスケール セットの両方が並行して実行したら、システム サービス (またはステートフル サービスのレプリカ) が新しいスケール セットに移行するように、一度に 1 つずつ元のノード インスタンスを無効にします。

1. クラスターと新しいノードが正常であることを確認してから、削除したノードの元のスケール セット (および関連リソース) とノードの状態を削除します。

以下で、、5 つのノードを含む 1 つのスケールセットを使用する、[シルバー持続性](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)が設定されたサンプル クラスターのプライマリ ノード タイプ VM の VM サイズとオペレーティング システムを更新するプロセスについて説明します。 プライマリ ノード タイプを次のようにアップグレードします。

- VM サイズ *Standard_D2_V2* から *Standard D4_V2* へ、および
- VM オペレーティング システム *Windows Server 2016 Datacenter with Containers* から *Windows Server 2019 Datacenter with Containers* へ。

> [!WARNING]
> 運用クラスターでこの手順を実行する前に、サンプル テンプレートを調べて、テスト クラスターに対してプロセスを検証することをお勧めします。 クラスターが短期間使用できなくなる場合もあります。
>
> クラスターの状態が異常な場合は、プライマリ ノード タイプのスケールアップ手順を試行しないでください。クラスターがさらに不安定になります。

このサンプル アップグレード シナリオを完了するために使用する、ステップバイステップの Azure デプロイ テンプレートは https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade にあります。

## <a name="set-up-the-test-cluster"></a>テスト クラスターをセットアップする

最初の Service Fabric テスト クラスターをセットアップしてみましょう。 まず、このシナリオを実行するために使用する Azure Resource Manager サンプル テンプレートを[ダウンロード](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)します。

次に、Azure アカウントにサインインします。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

次に、[*parameters.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) ファイルを開き、`clusterName` の値を (Azure 内で) 一意のものに更新します。

次のコマンドを実行すると、新しい自己署名証明書を生成し、テスト クラスターをデプロイできます。 使用する証明書が既にある場合は、「[既存の証明書を使用してクラスターをデプロイする](#use-an-existing-certificate-to-deploy-the-cluster)」に進んでください。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>自己署名証明書を生成してクラスターをデプロイする

まず、Service Fabric クラスターのデプロイに必要な変数を割り当てます。 `resourceGroupName`、`certSubjectName`、`parameterFilePath`、および `templateFilePath` の値を、特定のアカウントと環境に合わせて調整します。

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> 新しい Service Fabric クラスターをデプロイするコマンドを実行する前に、`certOutputFolder` の場所がローカル コンピューターに存在することを確認してください。

次に、Service Fabric テスト クラスターをデプロイします。

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

デプロイが完了したら、ローカル コンピューターで *.pfx* ファイル (`$certPfx`) を見つけて、証明書ストアにインポートします。

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

この操作によって、証明書の拇印が返されます。ここでこれを使用して、[新しいクラスターに接続し](#connect-to-the-new-cluster-and-check-health-status)、その正常性状態を確認できます。 (クラスターのデプロイの代替方法である次のセクションはスキップしてください)。

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>既存の証明書を使用してクラスターをデプロイする

既存の Azure Key Vault 証明書を代わりに使用して、テスト クラスターをデプロイすることもできます。 これを行うには、[Key Vault への参照](#obtain-your-key-vault-references)と証明書の拇印への参照を取得する必要があります。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

次に、クラスターのリソース グループ名を指定し、`templateFilePath` と `parameterFilePath` の場所を設定します。

> [!NOTE]
> 指定されたリソース グループは既に存在し、Key Vault と同じリージョンに存在している必要があります。

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

最後に、次のコマンドを実行して初期テスト クラスターをデプロイします。

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>新しいクラスターに接続して正常性状態を確認する

クラスターに接続し、そのノードの 5 つすべてが正常であることを確認します (`clusterName` および `thumb` 変数を実際の値に置き換えてください)。

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

これで、アップグレード手順を開始する準備ができました。

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>アップグレードしたスケール セットを使用して新しいプライマリ ノード タイプをデプロイする

ノード タイプをアップグレード (垂直方向にスケーリング) するには、まず、新しいスケール セットとサポート リソースを使用する新しいノード タイプをデプロイする必要があります。 新しいスケール セットは、元のスケール セットと同様に、プライマリ (`isPrimary: true`) としてマークされます (非プライマリ ノード タイプのアップグレードを実行している場合を除きます)。 次のセクションで作成されるリソースは、最終的にはクラスター内の新しいプライマリ ノード タイプになり、元のプライマリ ノード タイプのリソースは削除されます。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>アップグレードしたスケール セットでクラスター テンプレートを更新する

ここでは、新しいプライマリ ノード タイプとサポート リソースを追加するための、元のクラスター デプロイ テンプレートのセクションごとの変更点を示します。

この手順に必要な変更は、[*Step1-AddPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) テンプレート ファイルで既に行われています。これらの変更の詳細については、以下で説明します。 必要に応じて、説明をスキップし、[Key Vault 参照の取得](#obtain-your-key-vault-references)と、クラスターに新しいプライマリ ノード タイプを追加する[更新したテンプレートのデプロイ](#deploy-the-updated-template)を進めることができます。

> [!Note]
> 元のノード タイプ、スケール セット、ロード バランサー、パブリック IP、および元のプライマリ ノード タイプのサブネットと重複しない名前を使用するようにしてください。これらのリソースは、このプロセスの後の手順で削除するためです。

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>既存の仮想ネットワーク内に新しいサブネットを作成する

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>一意の domainNameLabel を持つ新しいパブリック IP を作成する

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>パブリック IP 用の新しいロード バランサーを作成する

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>(アップグレードされた VM および OS SKU を使用して) 新しい仮想マシン スケール セットを作成する

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

また、ワークロードに必要な追加の拡張機能を確実に含めるようにしてください。

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>クラスターに新しいプライマリ ノード タイプを追加する

新しいノード タイプ (vmNodeType1Name) に独自の名前、サブネット、IP、ロード バランサー、およびスケール セットを設定したので、元のノード タイプの他のすべての変数 (`nt0applicationEndPort`、 `nt0applicationStartPort`、 `nt0fabricTcpGatewayPort` など) を再利用できます。

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

テンプレートとパラメーター ファイルにすべての変更を実装したら、次のセクションに進み、Key Vault 参照を取得して、クラスターに更新をデプロイします。

### <a name="obtain-your-key-vault-references"></a>Key Vault 参照を取得する

更新された構成をデプロイするには、まず、Key Vault に格納されているクラスター証明書へのいくつかの参照が必要です。 これらの値を見つける最も簡単な方法は、Azure portal を使用することです。 必要なものは次のとおりです。

* **クラスター証明書の Key Vault URL。** Azure portal の Key Vault で、 **[証明書]**  > *目的の証明書* >  **[シークレット識別子]** を選択します。

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **クラスター証明書の拇印。** ([初期クラスターに接続](#connect-to-the-new-cluster-and-check-health-status)して、その正常性の状態を確認している場合は、既にこれを持っている可能性があります)。Azure portal で同じ証明書ブレード ( **[証明書]**  > *目的の証明書*) から、**X.509 SHA-1 の拇印 (16 進数)** をコピーします。

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault のリソース ID。** Azure portal の Key Vault から、 **[プロパティ]**  >  **[リソース ID]** を選択します。

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする

必要に応じて `templateFilePath` を調整し、次のコマンドを実行します。

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

デプロイが完了したら、クラスターの正常性を再度確認し、両方のノード タイプのすべてのノードが正常であることを確認します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>シード ノードを新しいノード タイプに移行する

現在、元のノード タイプを非プライマリとして更新し、そのノードの無効化を開始する準備ができています。 ノードが無効になると、クラスターのシステム サービスおよびシード ノードが新しいスケール セットに移行されます。

### <a name="unmark-the-original-node-type-as-primary"></a>元のノード タイプからプライマリのマークを解除する

最初に、元のノード タイプのテンプレート内の `isPrimary` の指定を解除します。

```json
{
    "isPrimary": false,
}
```

次に、その更新を含むテンプレートをデプロイします。 これにより、新しいスケール セットへのシード ノードの移行が開始されます。

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> 新しいスケール セットへのシード ノードの移行が完了するまでしばらく時間がかかります。 データの整合性を確保するため、一度に変更できるシード ノードは 1 つだけです。 各シード ノードの変更には、クラスターの更新が必要であるため、シード ノードの交換には、2 回のクラスターのアップグレード (ノードの追加と削除のために 1 回ずつ) が必要です。 このサンプル シナリオで 5 つのシード ノードをアップグレードすると、10 回のクラスターのアップグレードが発生します。

新しいスケール セットへのシード ノードの移行を監視するには、Service Fabric Explorer を使用します。 元のノード タイプ (nt0vm) のノードはすべて **[Is Seed Node]\(シード ノードです\)** 列が *false* になっているはずです。新しいノード タイプ (nt1vm) のものは *true* になります。

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>元のノー ド タイプのスケール セット内のノードを無効にする

すべてのシード ノードが新しいスケール セットに移行されたら、元のスケール セットのノードを無効にできます。

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
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

Service Fabric Explorer を使用して、元のスケール セットのノードの *[Disabling]\(無効化中\)* から *[Disabled]\(無効\)* 状態への進行状況を監視します。

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="無効なノードの状態を示す Service Fabric Explorer":::

シルバーおよびゴールド持続性の場合、一部のノードは [Disabled]\(無効\) 状態になりますが、その他は *[Disabling]\(無効化中\)* 状態のまま残ることがあります。 Service Fabric Explorer で、[Disabling]\(無効化中\) 状態のノードの **[詳細]** タブを確認してください。 種類が *EnsurePartitionQuorem* (インフラストラクチャ サービス パーティションのクォーラムを確保する) の "*安全性チェックの保留中*" が表示されている場合は、続行しても安全です。

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="'EnsurePartitionQuorum' という種類の安全性チェックの保留中が表示されている場合は、データの停止と、'無効化' 状態で停止しているノードの削除を進めることができます。":::

クラスターがブロンズ持続性の場合は、すべてのノードが *[Disabled]\(無効\)* 状態になるまで待ちます。

### <a name="stop-data-on-the-disabled-nodes"></a>無効化されたノードでデータを停止する

ここで、無効化されたノードでデータを停止できます。

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>元のノード タイプを削除し、そのリソースをクリーンアップする

元のノード タイプとそれに関連付けられているリソースを削除して、垂直スケーリングの手順を終了する準備ができています。

### <a name="remove-the-original-scale-set"></a>元のスケール セットを削除する

まず、ノード タイプのバッキング スケール セットを削除します。

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>元の IP およびロード バランサー リソースを削除する

ここで、元の IP およびロード バランサー リソースを削除できます。 この手順では、DNS 名も更新します。

> [!Note]
> *Standard* SKU のパブリック IP およびロード バランサーを既に使用している場合、この手順は省略可能です。 この場合は、同じロード バランサーで複数のスケール セットやノード タイプを使用できます。

次のコマンドを実行します。`$lbname` の値は、必要に応じて変更します。

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>元のノード タイプからノードの状態を削除する

元のノード タイプのノードでは、 **[正常性状態]** に *[エラー]* が表示されるようになります。 クラスターからそれらのノードの状態を削除します。

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

これで、Service Fabric Explorer には、新しいノード タイプ (nt1vm) である 5 つのノードのみがすべて *[OK]* の正常性状態の値で表示されるはずです。 クラスターの正常性状態には、引き続き *[エラー]* が表示されます。 次に、最新の変更内容と再デプロイを反映するようにテンプレートを更新することで、それを修復します。

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>新しくスケールアップされたプライマリ ノード タイプを反映するようにデプロイ テンプレートを更新する

この手順に必要な変更は、[*Step3-CleanupOriginalPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) テンプレート ファイルで既に行われています。以降のセクションで、これらのテンプレートの変更について詳しく説明します。 必要に応じて、説明をスキップし、[更新されたテンプレートのデプロイ](#deploy-the-finalized-template)を進めて、このチュートリアルを完了することができます。

#### <a name="update-the-cluster-management-endpoint"></a>クラスター管理エンドポイントを更新する

(*vmNodeType0Name* を *vmNodeType1Name* で更新することで) 新しい IP を参照するようにデプロイ テンプレートのクラスター `managementEndpoint` を更新します。

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>元のノード タイプの参照を削除する

デプロイ テンプレート内の Service Fabric リソースから元のノード タイプの参照を削除します。

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

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>既存のエラーを無視するように正常性ポリシーを構成する

シルバー以上の持続性クラスターの場合のみ、テンプレート内のクラスター リソースを更新し、以下に示すように、クラスター リソースのプロパティの下に *applicationDeltaHealthPolicies* を追加することで `fabric:/System` のアプリケーションの正常性を無視するように正常性ポリシーを構成します。 以下のポリシーでは既存のエラーが無視されますが、新しい正常性エラーは許容されません。

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

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>元のノード タイプのサポート リソースを削除する

ARM テンプレートとパラメーター ファイルから、元のノード タイプに関連するその他のすべてのリソースを削除します。 次の部分を削除します。

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>完成したテンプレートをデプロイする

最後に、変更した Azure Resource Manager テンプレートをデプロイします。

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> この手順にはしばらく時間がかかります (通常は最大 2 時間)。

このアップグレードでは *InfrastructureService* に対する設定が変更されるため、ノードの再起動が必要になります。 この場合、*forceRestart* は無視されます。 パラメーター `upgradeReplicaSetCheckTimeout` には、パーティションが (まだ安全な状態でない場合に) 安全な状態になるまで Service Fabric が待機する最大時間を指定します。 ノード上のすべてのパーティションが安全性チェックに合格すると、Service Fabric はそのノードでアップグレードを進めます。 パラメーター `upgradeTimeout` の値は 6 時間に短縮できますが、最大の安全確保のために 12 時間とする必要があります。

デプロイが完了したら、Azure portal で、Service Fabric リソースの状態が *[準備完了]* であることを確認します。 新しい Service Fabric Explorer エンドポイントに接続できること、**クラスターの正常性状態** が *[OK]* であること、デプロイされたすべてのアプリケーションが正常に機能することを確認します。

これで、クラスターのプライマリ ノード タイプが垂直方向にスケーリングされました。

## <a name="next-steps"></a>次のステップ

* [クラスターにノード タイプを追加する](virtual-machine-scale-set-scale-node-type-scale-out.md)方法について学習します。
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターのスケールインまたはスケールアウトします](service-fabric-cluster-windows-server-add-remove-nodes.md)。
