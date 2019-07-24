---
title: OpenShift Container Platform の Azure へのデプロイ | Microsoft Docs
description: OpenShift Container Platform を Azure にデプロイします。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/04/2018
ms.author: haroldw
ms.openlocfilehash: 1d869d822cdeb0051836a5fc5f01eb69c523f9e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995546"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>OpenShift Container Platform の Azure へのデプロイ

Azure で OpenShift Container Platform をデプロイするには、次のいずれかの方法を使用できます。

- [OpenShift Container Platform のドキュメント](https://docs.openshift.com/container-platform)の手順を実行するには、事前に必要な Azure インフラストラクチャ コンポーネントを手動でデプロイする必要があります。
- また、OpenShift Container Platform クラスターのデプロイを簡略化する既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-container-platform/)を使用することもできます。
- 別のオプションとして、[Azure Marketplace プラン](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)の使用があります。

どのオプションでも、Red Hat サブスクリプションが必要です。 デプロイ中に、Red Hat Enterprise Linux インスタンスは Red Hat サブスクリプションに登録され、OpenShift Container Platform の資格を含むプール ID に接続されます。
有効な Red Hat Subscription Manager (RHSM) のユーザー名、パスワード、およびプール ID があることを確認してください。 アクティブ化キー、組織 ID、およびプール ID を使用できます。 これらの情報は、 https://access.redhat.com にサインインして確認できます。

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>OpenShift Container Platform Resource Manager テンプレートを使用したデプロイ

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
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
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
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
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
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

パラメーターをユーザー固有の情報に置き換えます。

リリースによってパラメーターは異なっている可能性があるため、お使いの分岐に必要なパラメーターを確認してください。

### <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ

> [!NOTE] 
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)」を参照してください。

次の例では、OpenShift クラスターとすべての関連リソースを openshiftrg という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは GitHub リポジトリから直接参照され、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルが使用されます。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数と構成されたオプションに基づいて、このデプロイが完了するには少なくとも 30 分かかります。 デプロイが完了すると、Bastion DNS FQDN と OpenShift コンソールの URL がターミナルに出力されます。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

デプロイの完了を待ちながらコマンド ラインを関連付けたくない場合は、グループのデプロイのオプションの 1 つとして `--no-wait` を追加します。 デプロイからの出力は、リソース グループのデプロイ セクションで、Azure portal から取得できます。
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>OpenShift Container Platform Azure Marketplace オファーを使用したデプロイ

OpenShift Container Platform を Azure にデプロイするための最も簡単な方法は、[Azure Marketplace Offer](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview) の使用です。

これは最も簡単なだけでなく、制限されたカスタマイズ機能も備えている方法です。 Marketplace オファーには、次の構成オプションが含まれています。

- **マスター ノード**: 構成可能なインスタンスの種類がある 3 つのマスター ノード。
- **インフラストラクチャ ノード**: 構成可能なインスタンスの種類がある 3 つのインフラストラクチャ ノード。
- **ノード数**: ノードの数 (2 ～ 9) とインスタンスの種類は構成可能です。
- **ディスクの種類**: マネージド ディスクが使用されます。
- **ネットワーク**:新規または既存のネットワークと、カスタムの CIDR 範囲をサポートします。
- **CNS**: CNS を有効にできます。
- **[メトリック]**:メトリックを有効にできます。
- **ログ**: ログ記録を有効にできます。
- **Azure Cloud Provider**: 有効化できます。

## <a name="connect-to-the-openshift-cluster"></a>OpenShift クラスターへの接続

デプロイが完了したら、デプロイの出力セクションから接続を取得します。 ブラウザーで `OpenShift Console URL` を使用して、OpenShift コンソールに接続します。 または、要塞ホストに SSH 接続することもできます。 管理者のユーザー名は clusteradmin で、要塞のパブリック IP DNS FQDN は bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com である例を次に示します。

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group) コマンドを使用して削除できます。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>次の手順

- [デプロイ後タスク](./openshift-post-deployment.md)
- [Azure での OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
- [OpenShift Container Platform の概要](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>ドキュメントの共同作成者

このドキュメントを最新に保つための Vincent Power (vincepower) 氏と Alfred Sin (asinn826) 氏のご協力に感謝いたします。
