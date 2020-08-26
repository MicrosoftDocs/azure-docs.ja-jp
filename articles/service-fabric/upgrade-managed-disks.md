---
title: Azure Managed Disks を使用するためにクラスター ノードをアップグレードする
description: クラスターのダウンタイムをほとんどまたはまったく発生させずに、Azure Managed Disks を使用するように既存の Service Fabric クラスターをアップグレードする方法を次に示します。
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 1ca85af86df28691e2194c40e1cdde1abd7c8a4d
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192306"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Azure Managed Disks を使用するためにクラスター ノードをアップグレードする

[Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) は、Azure Virtual Machines でデータの永続的な記憶域として使用できる、お勧めのディスク ストレージ サービスです。 お使いのノードの種類の基になっている仮想マシン スケール セットを、マネージド ディスクを使用するようにアップグレードすることで、Service Fabric ワークロードの回復性を向上させることができます。 クラスターのダウンタイムをほとんどまたはまったく発生させずに、Azure Managed Disks を使用するように既存の Service Fabric クラスターをアップグレードする方法を次に示します。

マネージド ディスクを使用するために Service Fabric クラスター ノードをアップグレードする一般的な方法は次のとおりです。

1. そのノードの種類の、それ以外の点で重複した仮想マシン スケール セットをデプロイします。ただし、[managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) オブジェクトを、仮想マシン スケール セット デプロイ テンプレートの `osDisk` セクションに追加します。 新しいスケール セットは、元のものと同じロード バランサー/IP にバインドする必要があります。これにより、移行中にユーザーがサービスの停止を経験することがないようにします。

2. 元のスケール セットとアップグレードしたスケール セットの両方が並行して実行したら、システム サービス (またはステートフル サービスのレプリカ) が新しいスケール セットに移行するように、一度に 1 つずつ元のノード インスタンスを無効にします。

3. クラスターと新しいノードが正常であることを確認してから、削除したノードの元のスケール セットとノードの状態を削除します。

この記事では、クラスターのダウンタイムを回避しながら、マネージド ディスクを使用するように、サンプル クラスターのプライマリ ノードの種類をアップグレードする手順について説明します (以下の注意事項を参照してください)。 サンプル テスト クラスターの初期状態は、[Silver 持続性](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)の 1 つのノードの種類で構成され、5 つのノードを含む 1 つのスケール セットでサポートされています。

> [!NOTE]
> Basic SKU のロード バランサーの制限により、追加のスケール セットを追加することはできません。 代わりに、Standard SKU ロード バランサーを使用することをお勧めします。 詳細については、[2 つの SKU の比較](/azure/load-balancer/skus)に関する記事を参照してください。

> [!CAUTION]
> この手順では、クラスター DNS への依存関係がある場合 ([Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) にアクセスする場合など) にのみ、停止が発生します。 アーキテクチャ上の[フロントエンド サービスのベスト プラクティス](/azure/architecture/microservices/design/gateway)は、ノードの種類の前に何らかの種類の[ロード バランサー](/azure/architecture/guide/technology-choices/load-balancing-overview)を使用して、停止を発生させずにノード交換を可能にすることです。

アップグレード シナリオを完了するために使用する Azure Resource Manager の [テンプレートとコマンドレット](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)を次に示します。 テンプレートの変更については、次の「[プライマリ ノードの種類のアップグレードされたスケール セットをデプロイする](#deploy-an-upgraded-scale-set-for-the-primary-node-type)」を参照してください。

## <a name="set-up-the-test-cluster"></a>テスト クラスターをセットアップする

最初の Service Fabric テスト クラスターをセットアップしてみましょう。 まず、このシナリオを実行するために使用する Azure Resource Manager サンプル テンプレートを[ダウンロード](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)します。

次に、Azure アカウントにサインインします。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

次のコマンドを実行すると、新しい自己署名証明書を生成し、テスト クラスターをデプロイできます。 使用する証明書が既にある場合は、「[既存の証明書を使用してクラスターをデプロイする](#use-an-existing-certificate-to-deploy-the-cluster)」に進んでください。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>自己署名証明書を生成してクラスターをデプロイする

まず、Service Fabric クラスターのデプロイに必要な変数を割り当てます。 `resourceGroupName`、`certSubjectName`、`parameterFilePath`、および `templateFilePath` の値を、特定のアカウントと環境に合わせて調整します。

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> コマンドを実行して、新しい Service Fabric クラスターをデプロイする前に、`certOutputFolder` の場所がローカル コンピューターに存在することを確認してください。

次に、[*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) ファイルを開き、`clusterName` と `dnsName` の値を、PowerShell で設定した動的な値に対応するように調整して、変更を保存します。

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

この操作によって、証明書の拇印が返されます。これは、[新しいクラスターに接続し](#connect-to-the-new-cluster-and-check-health-status)、その正常性状態を確認するために使用します。 (クラスターのデプロイの代替方法である次のセクションはスキップしてください)。

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>既存の証明書を使用してクラスターをデプロイする

さらに、既存の Azure Key Vault 証明書を使用して、テスト クラスターをデプロイすることもできます。 これを行うには、[Key Vault への参照](#obtain-your-key-vault-references)と証明書の拇印への参照を取得する必要があります。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

[*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) ファイルを開いて、`clusterName` と `dnsName` の値を一意のものに変更します。

最後に、クラスターのリソース グループ名を指定し、*Initial-1NodeType-UnmanagedDisks* ファイルの `templateFilePath` と `parameterFilePath` の場所を設定します。

> [!NOTE]
> 指定されたリソース グループは既に存在し、Key Vault と同じリージョンに存在している必要があります。

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

最後に、次のコマンドを実行して初期テスト クラスターをデプロイします。

```powershell
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

クラスターに接続し、5 つのすべてのノードが正常であることを確認します (`clusterName` と `thumb` の変数をお使いのクラスターに置き換えます)。

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>プライマリ ノードの種類のアップグレードされたスケール セットをデプロイする

ノードの種類をアップグレードするか、*垂直方向にスケーリングする*には、そのノードの種類の仮想マシン スケール セットのコピーをデプロイする必要があります。これは、目的のアップグレード/変更とその独自の個別のサブネットと受信 NAT アドレス プールを含むことを除いて、元のスケール セット (同じ `nodeTypeRef`、`subnet`、`loadBalancerBackendAddressPools` への参照を含む) と同じです。 プライマリ ノードの種類をアップグレードするので、元のスケール セットと同様に、新しいスケール セットがプライマリ (`isPrimary: true`) としてマークされます。 (プライマリ以外のノードの種類のアップグレードの場合は、単にこれを省略してください)。

便宜上、*Upgrade-1NodeType-2ScaleSets-ManagedDisks* [ テンプレート](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json)および[パラメーター](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) ファイルで、必要な変更が既に行われています。

以下のセクションでは、テンプレートの変更点について詳しく説明します。 必要に応じて、説明をスキップし、[アップグレード手順の次のステップ](#obtain-your-key-vault-references)に進んでもかまいません。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>アップグレードしたスケール セットでクラスター テンプレートを更新する

ここでは、プライマリ ノード の種類のアップグレードされたスケール セットを追加するための、元のクラスター デプロイ テンプレートのセクションごとの変更点を示します。

#### <a name="parameters"></a>パラメーター

新しいスケール セットのインスタンス名、数、およびサイズのパラメーターを追加します。 `vmNodeType1Name` は新しいスケール セットに一意であり、数とサイズの値は元のスケール セットと同じであることに注意してください。

**テンプレート ファイル**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**パラメーター ファイル**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>変数

デプロイ テンプレートの `variables` セクションで、新しいスケール セットの受信 NAT アドレス プールのエントリを追加します。

**テンプレート ファイル**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>リソース

デプロイ テンプレートの *resources* セクションで、新しい仮想マシン スケール セットを追加します。次の点に注意してください。

* 新しいスケール セットでは、元のノードと同じノードの種類を参照します。

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 新しいスケール セットでは、同じロード バランサーのバックエンド アドレスとサブネットを参照します (ただし、別のロード バランサーの受信 NAT プールを使用します)。

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* 元のスケール セットと同様に、新しいスケール セットがプライマリ ノードの種類としてマークされます。 (プライマリ以外のノードの種類をアップグレードする場合は、この変更を省略します)。

    ```json
    "isPrimary": true,
    ```

* 元のスケール セットとは異なり、新しいスケール セットはマネージド ディスクを使用するようにアップグレードされます。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

テンプレートとパラメーター ファイルにすべての変更を実装したら、次のセクションに進み、Key Vault 参照を取得して、クラスターに更新をデプロイします。

### <a name="obtain-your-key-vault-references"></a>Key Vault 参照を取得する

更新された構成をデプロイするには、まず、Key Vault に格納されているクラスター証明書へのいくつかの参照を取得します。 これらの値を見つける最も簡単な方法は、Azure portal を使用することです。 必要なものは次のとおりです。

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

必要に応じて `parameterFilePath` と `templateFilePath` を調整し、次のコマンドを実行します。

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

デプロイが完了したら、再度クラスターの正常性を確認し、10 ノードすべて (元の 5 つと新しいスケール セットの 5 つ) が正常であることを確認します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>シード ノードを新しいスケール セットに移行する

これで、元のスケール セットのノードの無効化を開始する準備ができました。 これらのノードが無効になると、システム サービスとシード ノードが、プライマリ ノードの種類としてもマークされているため、新しいスケール セットの VM に移行されます。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Service Fabric Explorer を使用して、新しいスケール セットへのシード ノードの移行と、元のスケール セットのノードの*無効にしています*から*無効*状態への進行状況を監視します。

![無効なノードの状態を示す Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 元のスケール セットのすべてのノードで無効化操作が完了するまでにいくらか時間がかかる場合があります。 データの整合性を確保するため、一度に変更できるシード ノードは 1 つだけです。 各シード ノードの変更には、クラスターの更新が必要であるため、シード ノードの交換には、2 回のクラスターのアップグレード (ノードの追加と削除のために 1 回ずつ) が必要です。 このサンプル シナリオで 5 つのシード ノードをアップグレードすると、10 回のクラスターのアップグレードが発生します。

## <a name="remove-the-original-scale-set"></a>元のスケール セットを削除する

無効化操作が完了したら、スケール セットを削除します。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer では、削除されたノード (つまり、*クラスターの正常性状態*) が *エラー*状態で表示されるようになります。

![エラー状態の無効なノードを示す Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Service Fabric クラスターから古いノードを削除して、クラスターの正常性状態を *OK* に復元します。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![エラー状態の停止したノードが削除された Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プロセス中のサービスの停止を回避しながら、Service Fabric クラスターの仮想マシン スケール セットを、マネージド ディスクを使用するようにアップグレードする方法について説明しました。 関連トピックの詳細については、次のリソースを参照してください。

具体的には、次の方法を学習します。

* [Service Fabric クラスターのプライマリ ノード タイプをスケールアップする](service-fabric-scale-up-node-type.md)

* [マネージド ディスクを使用するためにスケール セット テンプレートを変換する](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Service Fabric ノード タイプを削除する](service-fabric-how-to-remove-node-type.md)

関連項目:

* [サンプル:Azure Managed Disks を使用するためにクラスター ノードをアップグレードする](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [垂直方向のスケーリングに関する考慮事項](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
