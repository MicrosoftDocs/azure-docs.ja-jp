---
title: OpenShift Container Platform 3.11 を Azure へデプロイする
description: OpenShift Container Platform 3.11 を Azure へデプロイする。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: ce3f56530a7bad6eecd4c2edd3b21debe8c75a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214152"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>OpenShift Container Platform 3.11 を Azure へデプロイする

Azure で OpenShift Container Platform 3.11 をデプロイするには、次のいずれかの方法を使用できます。

- [OpenShift Container Platform のドキュメント](https://docs.openshift.com/container-platform)の手順を実行するには、事前に必要な Azure インフラストラクチャ コンポーネントを手動でデプロイする必要があります。
- また、OpenShift Container Platform クラスターのデプロイを簡略化する既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-container-platform/)を使用することもできます。
- 別のオプションとして、[Azure Marketplace プラン](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)の使用があります。

どのオプションでも、Red Hat サブスクリプションが必要です。 デプロイ中に、Red Hat Enterprise Linux インスタンスは Red Hat サブスクリプションに登録され、OpenShift Container Platform の資格を含むプール ID に接続されます。
有効な Red Hat Subscription Manager (RHSM) のユーザー名、パスワード、およびプール ID があることを確認してください。 アクティブ化キー、組織 ID、およびプール ID を使用できます。 これらの情報は、 https://access.redhat.com にサインインして確認できます。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>OpenShift Container Platform Resource Manager 3.11 テンプレートを使用したデプロイ

### <a name="private-clusters"></a>プライベート クラスター

プライベートの OpenShift クラスターをデプロイする場合は、マスター ロード バランサー (Web コンソール) またはインフラ ロード バランサー (ルーター) に関連付けられているパブリック IP を持たないこと以外にも、要件があります。  一般に、プライベート クラスターでは、カスタム DNS サーバー (既定の Azure DNS ではない)、カスタム ドメイン名 (contoso.com など)、および定義済みの仮想ネットワークを使用します。  プライベート クラスターの場合、すべての適切なサブネットおよび DNS サーバー設定を使って、事前に仮想ネットワークを構成する必要があります。  その後、**existingMasterSubnetReference**、**existingInfraSubnetReference**、**existingCnsSubnetReference**、および **existingNodeSubnetReference** を使用して、クラスターによって使用される既存のサブネットを指定します。

プライベート マスターが選択されている場合 (**masterClusterType**=private)、**masterPrivateClusterIp** には静的プライベート IP が指定される必要があります。  この IP は、マスター ロード バランサーのフロント エンドに割り当てられます。  IP は、マスター サブネット用の CIDR 内にあり、使用中でないことが要件です。  **masterClusterDnsType** は "custom" に設定される必要があり、**masterClusterDns** にはマスター DNS 名が指定される必要があります。  DNS 名は、静的プライベート IP にマップする必要があり、マスター ノード上でコンソールにアクセスするために使用されます。

プライベート ルーターが選択されている場合 (**routerClusterType**=private)、**routerPrivateClusterIp** には静的プライベート IP が指定される必要があります。  この IP は、インフラ ロード バランサーのフロント エンドに割り当てられます。  IP は、インフラ サブネット用の CIDR 内にあり、使用中でないことが要件です。  **routingSubDomainType** は "custom" に設定される必要があり、**routingSubDomain** にはルーティング用のワイルドカード DNS 名が指定される必要があります。  

また、プライベート マスターとプライベート ルーターが選択された場合は、**domainName** にカスタム ドメイン名を入力することも必要になります。

デプロイが成功した後は、踏み台ノードは SSH 接続できるパブリック IP を備えたノードだけです。  マスター ノードがパブリック アクセス用に構成されている場合でも、SSH アクセスには公開されません。

Resource Manager テンプレートを使用してデプロイするには、パラメーター ファイルを使用して入力パラメーターを指定します。 デプロイをさらにカスタマイズするには、GitHub リポジトリをフォークし、適切な項目を変更します。

一般的なカスタマイズ オプションには以下のような項目がありますが、この限りではありません。

- Bastion VM サイズ (azuredeploy.json 内の変数)
- 名前付け規則 (azuredeploy.json 内の変数)
- OpenShift クラスターの詳細 - ホスト ファイル (deployOpenShift.sh) 経由で変更済み

### <a name="configure-the-parameters-file"></a>パラメーター ファイルの構成

[OpenShift Container Platform テンプレート](https://github.com/Microsoft/openshift-container-platform)には、異なるバージョンの OpenShift Container Platform で利用できる複数の分岐があります。  ニーズに基づいて、リポジトリから直接デプロイすることも、リポジトリをフォークし、デプロイする前にテンプレートまたはスクリプトに対してカスタムの変更を加えることもできます。

以前に作成したサービス プリンシパルの値 `appId` を `aadClientId` パラメーターに使用します。

次の例では、必要なすべての入力を含む azuredeploy.parameters.json という名前のパラメーター ファイルを示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

パラメーターをユーザー固有の情報に置き換えます。

リリースによってパラメーターは異なっている可能性があるため、お使いの分岐に必要なパラメーターを確認してください。

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.json ファイルの説明

| プロパティ | 説明 | 有効なオプション | Default value |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 成果物 (json、スクリプトなど) への URL |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | リソースのデプロイ先となる Azure リージョン |  |  |
| `masterVmSize` | マスター VM のサイズ。 azuredeploy.json ファイルに一覧表示された許可されている VM サイズのいずれかから選択します |  | Standard_E2s_v3 |
| `infraVmSize` | インフラ VM のサイズ。 azuredeploy.json ファイルに一覧表示された許可されている VM サイズのいずれかから選択します |  | Standard_D4s_v3 |
| `nodeVmSize` | アプリ ノード VM のサイズ。 azuredeploy.json ファイルに一覧表示された許可されている VM サイズのいずれかから選択します |  | Standard_D4s_v3 |
| `cnsVmSize` | コンテナー ネイティブ ストレージ (CNS) ノード VM のサイズ。 azuredeploy.json ファイルに一覧表示された許可されている VM サイズのいずれかから選択します |  | Standard_E4s_v3 |
| `osImageType` | 使用する RHEL イメージ。 defaultgallery:オンデマンド、marketplace: サード パーティのイメージ | defaultgallery <br> マーケットプレース | defaultgallery |
| `marketplaceOsImage` | `osImageType` が marketplace の場合、マーケットプレース オファーの 'publisher'、'offer'、'sku'、'version' に適切な値を入力します。 このパラメーターは、オブジェクトの種類です |  |  |
| `storageKind` | 使用されるストレージの種類  | キー管理<br> unmanaged | キー管理 |
| `openshiftClusterPrefix` | すべてのノードに対してホスト名を構成するために使用されるクラスター プレフィックス。  1 から 20 文字まで |  | mycluster |
| `minoVersion` | デプロイする OpenShift Container Platform 3.11 のマイナー バージョン |  | 69 |
| `masterInstanceCount` | デプロイするマスター ノードの数 | 1、3、5 | 3 |
| `infraInstanceCount` | デプロイするインフラ ノードの数 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | デプロイするノードの数 | 1、2、3、4、5、6、7、8、9、10、11、12、13、14、15、16、17、18、19、20、21、22、23、24、25、26、27、28、29、30 | 2 |
| `cnsInstanceCount` | デプロイする CNS ノードの数 | 3、4 | 3 |
| `osDiskSize` | VM 用の OS ディスクのサイズ (GB 単位) | 64、128、256、512、1024、2048 | 64 |
| `dataDiskSize` | Docker ボリューム用のノードにアタッチするデータ ディスクのサイズ (GB 単位) | 32、64、128、256、512、1024、2048 | 64 |
| `cnsGlusterDiskSize` | glusterfs によって使用する CNS ノードにアタッチするデータ ディスクのサイズ (GB 単位) | 32、64、128、256、512、1024、2048 | 128 |
| `adminUsername` | OS (VM) ログインと初期の OpenShift ユーザーの両方に対する管理者ユーザー名 |  | ocpadmin |
| `enableMetrics` | メトリックを有効にします。 メトリックではより多くのリソースが必要になるため、インフラ VM に適したサイズを選択します | true <br> false | false |
| `enableLogging` | ログ記録を有効にします。 elasticsearch ポッドでは 8 GB の RAM が必要になるため、インフラ VM 用に適切なサイズを選択します | true <br> false | false |
| `enableCNS` | コンテナー ネイティブ ストレージを有効にします | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat Subscription Manager のユーザー名または組織 ID |  |  |
| `rhsmPoolId` | コンピューティング ノードに対する OpenShift 権利が含まれている Red Hat Subscription Manager のプール ID |  |  |
| `rhsmBrokerPoolId` | マスター ノードおよびインフラ ノードの OpenShift 権利が含まれている Red Hat Subscription Manager のプール ID。 別のプール ID を持っていない場合は、'rhsmPoolId' と同じプール ID を入力します |  |
| `sshPublicKey` | SSH 公開キーをここにコピー |  |  |
| `keyVaultSubscriptionId` | キー コンテナーを含むサブスクリプションのサブスクリプション ID |  |  |
| `keyVaultResourceGroup` | キー コンテナーを含むリソース グループの名前 |  |  |
| `keyVaultName` | 作成したキー コンテナーの名前 |  |  |
| `enableAzure` | Azure Cloud Provider を有効にします | true <br> false | true |
| `aadClientId` | Azure Active Directory のクライアント ID。サービス プリンシパル用のアプリケーション ID とも呼ばれます |  |  |
| `domainName` | 使用するカスタム ドメインの名前 (該当する場合)。 完全なプライベート クラスターをデプロイしない場合は、"none" に設定します |  | なし |
| `masterClusterDnsType` | OpenShift Web コンソールのドメインの種類。 'default' では、マスター インフラ パブリック IP の DNS ラベルが使用されます。 'custom' を使用して独自の名前を定義できます | default <br> custom | default |
| `masterClusterDns` | `masterClusterDnsType` に 'custom' を選択した場合に、OpenShift Web コンソールへのアクセスに使用するカスタム DNS 名 |  | console.contoso.com |
| `routingSubDomainType` | 'nipio' に設定した場合、`routingSubDomain` では nip.io が使用されます。  ルーティングに使用する独自のドメインがある場合は、'custom' を使用します | nipio <br> custom | nipio |
| `routingSubDomain` | `routingSubDomainType` に 'custom' を選択した場合に、ルーティングに使用するワイルドカード DNS 名 |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 既存の Virtual Network を使用するか、新しい Virtual Network を作成するかを選択します | existing <br> new | new |
| `virtualNetworkResourceGroupName` | `virtualNetworkNewOrExisting` に 'new' を選択した場合、新しい Virtual Network 用のリソース グループの名前 |  | resourceGroup().name |
| `virtualNetworkName` | `virtualNetworkNewOrExisting` に 'new' を選択した場合、作成する新しい Virtual Network の名前 |  | openshiftvnet |
| `addressPrefixes` | 新しい仮想ネットワークのアドレス プレフィックス |  | 10.0.0.0/14 |
| `masterSubnetName` | マスター サブネットの名前 |  | mastersubnet |
| `masterSubnetPrefix` | マスター サブネットに使用される CIDR - addressPrefix のサブセットにする必要があります |  | 10.1.0.0/16 |
| `infraSubnetName` | インフラ サブネットの名前 |  | infrasubnet |
| `infraSubnetPrefix` | インフラ サブネットに使用される CIDR - addressPrefix のサブセットにする必要があります |  | 10.2.0.0/16 |
| `nodeSubnetName` | ノード サブネットの名前 |  | nodesubnet |
| `nodeSubnetPrefix` | ノード サブネットに使用される CIDR - addressPrefix のサブセットにする必要があります |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | マスター ノードに対する既存のサブネットの完全参照。 新しい vNet / サブネットを作成する場合には、必要なし |  |  |
| `existingInfraSubnetReference` | インフラ ノードに対する既存のサブネットの完全参照。 新しい vNet / サブネットを作成する場合には、必要なし |  |  |
| `existingCnsSubnetReference` | CNS ノードに対する既存のサブネットの完全参照。 新しい vNet / サブネットを作成する場合には、必要なし |  |  |
| `existingNodeSubnetReference` | コンピューティング ノードに対する既存のサブネットの完全参照。 新しい vNet / サブネットを作成する場合には、必要なし |  |  |
| `masterClusterType` | クラスターがプライベートまたはパブリックのどちらのマスター ノードを使用するかを指定します。 プライベートを選択した場合、マスター ノードがパブリック IP 経由でインターネットに公開されることはありません。 代わりに、`masterPrivateClusterIp` に指定されたプライベート IP を使用します | public <br> プライベート | public |
| `masterPrivateClusterIp` | プライベート マスター ノードが選択された場合、内部ロード バランサーによってマスター ノードに対して使用されるプライベート IP アドレスを指定する必要があります。 この静的 IP はマスター サブネット用の CIDR ブロック内にあり、既に使用されていないことが要件となります。 パブリック マスター ノードが選択された場合、この値は使用されないが、やはり指定しておく必要があります |  | 10.1.0.200 |
| `routerClusterType` | クラスターがプライベートまたはパブリックのどちらのインフラ ノードを使用するかを指定します。 プライベートが選択された場合、インフラ ノードがパブリック IP 経由でインターネットに公開されることはありません。 代わりに、`routerPrivateClusterIp` に指定されたプライベート IP を使用します | public <br> プライベート | public |
| `routerPrivateClusterIp` | プライベート インフラ ノードが選択された場合、内部ロード バランサーによってインフラ ノードに対して使用されるプライベート IP アドレスを指定する必要があります。 この静的 IP はインフラ サブネット用の CIDR ブロック内にあり、既に使用されていないことが要件となります。 パブリック インフラ ノードが選択された場合、この値は使用されないが、やはり指定しておく必要があります |  | 10.2.0.200 |
| `routingCertType` | ルーティング ドメイン用のカスタム証明書または既定の自己署名証明書を使用 - 「**カスタム証明書**」セクションの手順に従います | selfsigned <br> custom | selfsigned |
| `masterCertType` | マスター ドメイン用のカスタム証明書または既定の自己署名証明書を使用 - 「**カスタム証明書**」セクションの手順に従います | selfsigned <br> custom | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ

> [!NOTE] 
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次の例では、OpenShift クラスターとすべての関連リソースを openshiftrg という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは GitHub リポジトリから直接参照され、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルが使用されます。

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数と構成されたオプションに基づいて、デプロイが完了するには少なくとも 60 分かかります。 デプロイが完了すると、Bastion DNS FQDN と OpenShift コンソールの URL がターミナルに出力されます。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

デプロイの完了を待ちながらコマンド ラインを関連付けたくない場合は、グループのデプロイのオプションの 1 つとして `--no-wait` を追加します。 デプロイからの出力は、リソース グループのデプロイ セクションで、Azure portal から取得できます。

## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、デプロイの出力セクションから接続を取得します。 **OpenShift コンソール URL** を使用して、ブラウザーから OpenShift コンソールに接続します。 踏み台ホストに SSH 接続することもできます。 管理者のユーザー名は clusteradmin で、踏み台のパブリック IP DNS FQDN は bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com である例を次に示します。

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group) コマンドを使用して削除できます。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>次のステップ

- [デプロイ後タスク](./openshift-container-platform-3x-post-deployment.md)
- [Azure での OpenShift デプロイのトラブルシューティング](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform の概要](https://docs.openshift.com)
