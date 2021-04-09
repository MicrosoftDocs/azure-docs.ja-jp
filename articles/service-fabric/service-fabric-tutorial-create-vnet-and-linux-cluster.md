---
title: Azure に Linux Service Fabric クラスターを作成する
description: Azure CLI を使用して Linux Service Fabric クラスターを既存の Azure 仮想ネットワークにデプロイする方法を学習します。
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77cc49c1b79e5c24e78a67a69493aa0b0059d565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791073"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Azure 仮想ネットワークに Linux Service Fabric クラスターをデプロイする

この記事では、Azure CLI とテンプレートを使用して Linux Service Fabric クラスターを [Azure 仮想ネットワーク (VNET)](../virtual-network/virtual-networks-overview.md) 内にデプロイする方法を学習します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。 PowerShell を使用して Windows クラスターを作成する場合は、[Azure でのセキュリティで保護された Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Service Fabric CLI をインストール](service-fabric-cli.md)します。
* [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* クラスターの主要な概念については、[Azure クラスターの概要](service-fabric-azure-clusters-overview.md)に関するページを参照してください。
* 運用環境用のクラスターのデプロイを[計画して準備します](service-fabric-cluster-azure-deployment-preparation.md)。

次の手順で、7 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="download-and-explore-the-template"></a>テンプレートのダウンロードと詳細の確認

次の Resource Manager テンプレート ファイルをダウンロードします。

Ubuntu 16.04 LTS の場合:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ubuntu 18.04 LTS の場合:

* [AzureDeploy.json][template2]
* [AzureDeploy.Parameters.json][parameters2]

Ubuntu 18.04 LTS の 2 つのテンプレートの違い 
* **vmImageSku** 属性が "18.04-LTS" に設定されています
* 各ノードの **typeHandlerVersion** が 1.1 に設定されています
* 次の Microsoft.ServiceFabric/clusters リソース
   - **apiVersion** が "2019-03-01" 以上に設定されています
   - **vmImage** プロパティが "Ubuntu18_04" に設定されています

このテンプレートでは、7 つの仮想マシンと 3 つのノード タイプから成るセキュリティ保護されたクラスターが、仮想ネットワーク内にデプロイされます。  [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) には他のサンプル テンプレートがあります。 [AzureDeploy.json][template] では、次のものを含むいくつかのリソースがデプロイされます。

### <a name="service-fabric-cluster"></a>Service Fabric クラスター

**Microsoft.ServiceFabric/clusters** リソースでは、以下の特性によって Linux クラスターがデプロイされます。

* 3 つのノード タイプ
* プライマリ ノード タイプに 5 つのノード (テンプレート パラメーターで構成可能)、他のノード タイプにそれぞれ 1 つのノード
* OS: (Ubuntu 16.04 LTS/Ubuntu 18.04 LTS) (テンプレート パラメーターで構成可能)
* 証明書の保護 (テンプレート パラメーターで構成可能)
* [DNS サービス](service-fabric-dnsservice.md)が有効
* ブロンズ[持続性レベル](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
* シルバー[信頼性レベル](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)
* クライアント接続エンドポイント: 19000 (テンプレート パラメーターで構成可能)
* HTTP ゲートウェイ エンドポイント: 19080 (テンプレート パラメーターで構成可能)

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft.Network/loadBalancers** リソースでは、ロード バランサーが構成され、以下のポートに対してプローブとルールが設定されます。

* クライアント接続エンドポイント: 19000
* HTTP ゲートウェイ エンドポイント: 19080
* アプリケーション ポート: 80
* アプリケーション ポート: 443

### <a name="virtual-network-and-subnet"></a>仮想ネットワークとサブネット

仮想ネットワークとサブネットの名前は、テンプレート パラメーターで宣言されています。  仮想ネットワークとサブネットのアドレス空間も、テンプレート パラメーターで宣言され、**Microsoft.Network/virtualNetworks** リソース内に構成されます。

* 仮想ネットワークのアドレス空間: 10.0.0.0/16
* Service Fabric サブネットのアドレス空間: 10.0.2.0/24

他のアプリケーション ポートが必要な場合は、Microsoft.Network/loadBalancers リソースを調整してトラフィックを許可する必要があります。

## <a name="set-template-parameters"></a>テンプレート パラメーターの設定

**AzureDeploy.Parameters** ファイルでは、クラスターおよび関連リソースのデプロイに使用される多くの値が宣言されています。 実際のデプロイに合わせて変更する必要があるパラメーターの一部を次に示します。

|パラメーター|値の例|Notes|
|---|---||
|adminUserName|vmadmin| クラスター VM の管理者ユーザー名。 |
|adminPassword|Password#1234| クラスター VM の管理者パスワード。|
|clusterName|mysfcluster123| クラスターの名前。 |
|location|southcentralus| クラスターの場所。 |
|certificateThumbprint|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の SHA1 サムプリントの値を入力します。 例: "6190390162C988701DB5676EB81083EA608DCCF3"。 </p>|
|certificateUrlValue|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の URL を入力します。 例: "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346"。</p>|
|sourceVaultValue||<p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、ソース コンテナー値を入力します。 たとえば、"/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT" と入力します。</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>仮想ネットワークとクラスターのデプロイ

次に、ネットワーク トポロジを設定し、Service Fabric クラスターをデプロイします。 **AzureDeploy.json** Resource Manager テンプレートでは、Service Fabric 用の仮想ネットワーク (VNET) とサブネットが作成されます。 このテンプレートを使用すると、証明書セキュリティが有効なクラスターもデプロイできます。  運用環境クラスターの場合は、証明機関 (CA) から取得した証明書をクラスター証明書として使用します。 自己署名証明書を使用して、テスト クラスターを保護することができます。

この記事のテンプレートでは、クラスター証明書を識別するために証明書の拇印を使用するクラスターがデプロイされます。  2 つの証明書が同じ拇印を持つことはできず、そのことが証明書の管理をより困難にしています。 デプロイされたクラスターで使用するのを、証明書の拇印から証明書共通名に切り替えることで、証明書の管理が大幅に単純化します。  証明書の管理に証明書共通名を使用するようにクラスターを更新する方法については、[証明書共通名管理へのクラスターの変更](service-fabric-cluster-change-cert-thumbprint-to-cn.md)に関するページを参照してください。

### <a name="create-a-cluster-using-an-existing-certificate"></a>既存の証明書を使用したクラスターの作成

次のスクリプトでは、既存の証明書で保護された新しいクラスターをデプロイするために、[az sf cluster create](/cli/azure/sf/cluster) コマンドとテンプレートを使用しています。 また、このコマンドを使用して、Azure に新しいキー コンテナーを作成し、証明書をアップロードします。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>新しい自己署名証明書を使用したクラスターの作成

次のスクリプトでは、[az sf cluster create](/cli/azure/sf/cluster) コマンドとテンプレートを使用して、Azure に新しいクラスターがデプロイされます。 また、このコマンドでは、Azure に新しいキー コンテナーが作成され、新しい自己署名証明書がそのキー コンテナーに追加されて、証明書ファイルがローカルにダウンロードされます。

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する

Service Fabric CLI `sfctl cluster select` コマンドでキーを使用して、クラスターに接続します。  自己署名証明書には **--no-verify** オプションのみを使用します。

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

`sfctl cluster health` コマンドを使用して、接続できていることと、クラスターが正常であることを確認します。

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の記事にすぐに進まない場合は、料金の発生を避けるため、[クラスターを削除](./service-fabric-tutorial-delete-cluster.md)することができます。

## <a name="next-steps"></a>次のステップ

[クラスターのスケーリング](service-fabric-tutorial-scale-cluster.md)の方法について学びます。

この記事のテンプレートでは、クラスター証明書を識別するために証明書の拇印を使用するクラスターがデプロイされます。  2 つの証明書が同じ拇印を持つことはできず、そのことが証明書の管理をより困難にしています。 デプロイされたクラスターで使用するのを、証明書の拇印から証明書共通名に切り替えることで、証明書の管理が大幅に単純化します。  証明書の管理に証明書共通名を使用するようにクラスターを更新する方法については、[証明書共通名管理へのクラスターの変更](service-fabric-cluster-change-cert-thumbprint-to-cn.md)に関するページを参照してください。

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
