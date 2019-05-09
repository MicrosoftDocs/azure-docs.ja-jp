---
title: コンテナー インスタンスを Azure 仮想ネットワークにデプロイする
description: コンテナー グループを新規または既存の Azure 仮想ネットワークにデプロイする方法について説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a4da7a23d6dcb50164829507130fed145abeebbd
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517319"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>コンテナー インスタンスを Azure 仮想ネットワークにデプロイする

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) では、Azure リソースやオンプレミス リソースのセキュアなプライベート ネットワーキングが提供されます。 Azure 仮想ネットワークにコンテナー グループをデプロイすれば、それらのコンテナーで、仮想ネットワーク内の他のリソースと安全に通信することができます。

Azure 仮想ネットワークにデプロイされたコンテナー グループでは、次のようなシナリオに対応できます。

* 同じサブネット内のコンテナー グループ間での直接的な通信
* コンテナー インスタンスから、仮想ネットワーク内のデータベースに[タスクベース](container-instances-restart-policy.md)のワークロード出力を送信する
* 仮想ネットワーク内の[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)から、コンテナー インスタンス用のコンテンツを取得する
* 仮想ネットワーク内の仮想マシンとのコンテナー通信
* [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)または [ExpressRoute](../expressroute/expressroute-introduction.md) を使用した、オンプレミス リソースとのコンテナー通信

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="virtual-network-deployment-limitations"></a>仮想ネットワークのデプロイに関する制限事項

仮想ネットワークにコンテナー グループをデプロイする場合には、いくつかの制限が適用されます。

* コンテナー グループをサブネットにデプロイする場合、サブネットに他の種類のリソースを含めることはできません。 コンテナー グループをデプロイする前に、既存のサブネットから既存のリソースをすべて削除するか、新しいサブネットを作成してください。
* 仮想ネットワークにデプロイされたコンテナー グループ内で[マネージド ID](container-instances-managed-identity.md) を使用することはできません。
* 仮想ネットワークにコンテナー グループをデプロイすると、追加のネットワーキング リソースが必要になるため、通常は、標準のコンテナー インスタンスをデプロイする場合よりも若干低速になります。

## <a name="preview-limitations"></a>プレビューの制限事項

この機能はプレビュー段階であるため、仮想ネットワークにコンテナー グループをデプロイする場合には、次の制限が適用されます。 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

コンテナーのリソース制限は、これらのリージョンでのネットワークに接続されていないコンテナー インスタンスに対する制限とは異なる場合があります。 現在、この機能でサポートされているのは、Linux コンテナーのみです。 Windows でのサポートは計画されています。

### <a name="unsupported-networking-scenarios"></a>サポートされていないネットワーク シナリオ 

* **Azure Load Balancer** - ネットワーク接続されたコンテナー グループ内のコンテナー インスタンスの前に Azure Load Balancer を配置することはサポートされていません
* **仮想ネットワーク ピアリング** - Azure Container Instances に委任されたサブネットを含む仮想ネットワークを別の仮想ネットワークにピアリングすることはできません
* **ルート テーブル** - Azure Container Instances に委任されたサブネットにユーザー定義のルートを設定することはできません
* **ネットワーク セキュリティ グループ** - Azure Container Instances に委任されたサブネットに適用された NSG における送信セキュリティ規則は、現在適用されていません 
* **パブリック IP または DNS ラベル** - 仮想ネットワークにデプロイされたコンテナー グループでは現在、パブリック IP アドレスまたは完全修飾ドメイン名を使用してコンテナーをインターネットに直接公開することはサポートされていません
* **内部名前解決** - 内部 Azure DNS を介した仮想ネットワーク内の Azure リソースの名前解決はサポートされていません

**ネットワーク リソースを削除**するには、仮想ネットワークにコンテナー グループをデプロイした後で、[追加の手順](#delete-network-resources)を実行する必要があります。

## <a name="required-network-resources"></a>必要なネットワーク リソース

仮想ネットワークにコンテナー グループをデプロイするには、3 つの Azure Virtual Network リソースが必要になります。[仮想ネットワーク](#virtual-network)自体と、仮想ネットワーク内で[委任されたサブネット](#subnet-delegated)、そして[ネットワーク プロファイル](#network-profile)です。 

### <a name="virtual-network"></a>仮想ネットワーク

仮想ネットワークでは、1 つ以上のサブネットを作成するためのアドレス空間が定義されます。 これにより、仮想ネットワーク内のサブネットに Azure リソース (コンテナー グループなど) をデプロイできるようになります。

### <a name="subnet-delegated"></a>サブネット (委任して使用)

サブネットは、仮想ネットワークを個別のアドレス空間に分割し、サブネット内に配置された Azure リソースから使用できるようにするものです。 仮想ネットワーク内には、1 つ以上のサブネットを作成できます。

コンテナー グループ用に使用するサブネットには、コンテナー グループしか含めることができません。 サブネットに最初のコンテナー グループをデプロイすると、Azure によって、そのサブネットが Azure Container Instances に委任されます。 委任されると、サブネットはコンテナー グループ用にしか使用できなくなります。 委任されたサブネットにコンテナー グループ以外のリソースをデプロイしようとすると、操作が失敗します。

### <a name="network-profile"></a>ネットワーク プロファイル

ネットワーク プロファイルは、Azure リソース用のネットワーク構成テンプレートです。 このプロファイルでは、リソースのネットワーク プロパティが指定されます (たとえば、リソースのデプロイ先となるサブネットなど)。 最初に [az container create][az-container-create] コマンドを使用してコンテナー グループをサブネット (および仮想ネットワーク) にデプロイすると、Azure により、ネットワーク プロファイルが自動的に作成されます。 その後は、そのネットワーク プロファイルを使ってサブネットにリソースをデプロイしていくことができます。 

Resource Manager テンプレート、YAML ファイル、またはプログラムのメソッドを使用して、コンテナー グループをサブネットにデプロイするには、ネットワーク プロファイルの完全な Resource Manager リソース ID を指定する必要があります。 以前 [az container create][az-container-create] を使用して作成したプロファイルを使用することも、Resource Manager テンプレート ([テンプレートの例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet)と[リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)を参照) を使用してプロファイルを作成することもできます。 以前作成したプロファイルの ID を取得するには、[az network profile list][az-network-profile-list] コマンドを使用します。 

次の図では、Azure Container Instances へ委任されたサブネットに、複数のコンテナー グループがデプロイされています。 サブネットにコンテナー グループを 1 つデプロイしたら、同じネットワーク プロファイルを指定して追加のコンテナー グループをデプロイしていくことができます。

![仮想ネットワーク内のコンテナー グループ][aci-vnet-01]

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

[az container create][az-container-create] を使用して、コンテナー グループを新しい仮想ネットワークにデプロイし、必要なネットワーク リソースを Azure で自動的に作成したり、既存の仮想ネットワークにデプロイしたりできます。 

### <a name="new-virtual-network"></a>新しい仮想ネットワーク

新しい仮想ネットワークにデプロイして、ネットワーク リソースを自動的に作成するには、[az container create][az-container-create] を実行するときに次の項目を指定します。

* 仮想ネットワーク名
* 仮想ネットワーク アドレスのプレフィックス (CIDR 形式)
* サブネット名
* サブネット アドレスのプレフィックス (CIDR 形式)

仮想ネットワークとサブネットのアドレス プレフィックスでは、仮想ネットワークとサブネットそれぞれのアドレス空間を指定します。 これらの値は、CIDR (Classless Inter-domain Routing) 表記法で表されます (例: `10.0.0.0/16`)。 サブネットの操作について詳しくは、「[仮想ネットワーク サブネットの追加、変更、削除](../virtual-network/virtual-network-manage-subnet.md)」をご覧ください。

この方法を使用して最初のコンテナー グループをデプロイした後は、仮想ネットワーク名とサブネット名を指定するか、Azure によって自動作成されたネットワーク プロファイルを指定して、同じサブネットへのデプロイを実行できます。 サブネットは Azure によって Azure Container Instances に委任されるので、サブネットにデプロイできるのは、コンテナー グループ*のみ*となります。

### <a name="existing-virtual-network"></a>既存の仮想ネットワーク

コンテナー グループを既存の仮想ネットワークにデプロイするには:

1. 既存の仮想ネットワーク内にサブネットを作成するか、既存のサブネットから、他の*すべて*のリソースを削除します
1. [az container create][az-container-create] を使用してコンテナー グループをデプロイし、次のいずれかを指定します。
   * 仮想ネットワーク名とサブネット名
   * 仮想ネットワーク リソース ID、およびサブネット リソース ID (これにより、別のリソース グループから仮想ネットワークを使用できます)
   * ネットワーク プロファイルの名前または ID (これは、[az network profile list][az-network-profile-list] を使用して取得できます)

既存のサブネットに最初のコンテナー グループをデプロイすると、Azure によって、そのサブネットが Azure Container Instances に委任されます。 そのサブネットには、コンテナー グループ以外のリソースをデプロイできなくなります。

## <a name="deployment-examples"></a>デプロイの例

次のセクションでは、Azure CLI を使用して仮想ネットワークにコンテナー グループをデプロイする方法について説明します。 サンプル コマンドは **Bash** シェル形式で示してあります。 PowerShell やコマンド プロンプトなど、別のシェルを使用する場合は、行継続文字を適切に調整してください。

### <a name="deploy-to-a-new-virtual-network"></a>新しい仮想ネットワークへのデプロイ

最初に、コンテナー グループをデプロイし、新しい仮想ネットワークとサブネットのパラメーターを指定します。 これらのパラメーターを指定すると、Azure によって仮想ネットワークとサブネットが作成され、サブネットが Azure Container instances に委任されて、ネットワーク プロファイルも作成されます。 これらのリソースが作成されると、コンテナー グループがサブネットにデプロイされます。

新しい仮想ネットワークとサブネットの設定を指定して、次の [az container create][az-container-create] コマンドを実行します。 リソース グループが、仮想ネットワーク内のコンテナー グループを[サポート](#preview-limitations)するリージョンで作成されている場合は、そのリソース グループの名前を指定する必要があります。 このコマンドにより、パブリックの Microsoft [aci-helloworld][aci-helloworld] コンテナーがデプロイされます。これは、静的 Web ページを提供する小規模な Node.js Web サーバーを実行するコンテナーです。 次のセクションでは、同じサブネットに 2 つ目のコンテナー グループをデプロイし、2 つのコンテナー インスタンス間の通信をテストします。

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

この方法を使って新しい仮想ネットワークへのデプロイを実行する際には、ネットワーク リソースの作成に数分かかることがあります。 最初のデプロイが済んだら、追加のコンテナー グループはより短時間でデプロイできます。

### <a name="deploy-to-existing-virtual-network"></a>既存の仮想ネットワークへのデプロイ

新しい仮想ネットワークにコンテナー グループをデプロイしたら、次は同じサブネットに 2 つ目のコンテナー グループをデプロイし、2 つのコンテナー インスタンス間の通信を確認します。

まず、デプロイした 1 つ目のコンテナー グループ (*appcontainer*) の IP アドレスを取得します。

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

出力には、プライベート サブネット内にあるコンテナー グループの IP アドレスが表示されます。

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

次に、`CONTAINER_GROUP_IP` を `az container show` コマンドで取得した IP アドレスに設定し、次の `az container create` コマンドを実行します。 この 2 つ目のコンテナー (*commchecker*) は、Alpine Linux ベースのイメージを実行し、最初のコンテナー グループのプライベート サブネット IP アドレスに対して `wget` を実行します。

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

この 2 つ目のコンテナーのデプロイが完了したら、実行された `wget` コマンドの出力を確認するために、ログを取得します。

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

2 つ目のコンテナーが最初のコンテナーと正常に通信できた場合、出力は次のようになります。

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

ログ出力を見ると、`wget` が最初のコンテナーに接続し、ローカル サブネット上のプライベート IP アドレスを使用して、最初のコンテナーからインデックス ファイルをダウンロードできたことがわかります。 2 つのコンテナー グループ間のネットワーク トラフィックは、仮想ネットワーク内に維持されたことになります。

### <a name="deploy-to-existing-virtual-network---yaml"></a>既存の仮想ネットワークへのデプロイ - YAML

既存の仮想ネットワークにコンテナー グループをデプロイするのには、YAML ファイルを使用することもできます。 仮想ネットワーク内のサブネットにデプロイするには、YAML にいくつかの追加プロパティを指定します。

* `ipAddress`:コンテナー グループの IP アドレス設定。
  * `ports`:開くポート (もしあれば)。
  * `protocol`:開いたポートのプロトコル (TCP または UDP)。
* `networkProfile`:ネットワーク設定 (Azure リソース用の仮想ネットワークとサブネットなど)。
  * `id`:`networkProfile` の完全な Resource Manager リソース ID。

YAML ファイルを使用して仮想ネットワークにコンテナー グループをデプロイするには、まずネットワーク プロファイルの ID を取得する必要があります。 仮想ネットワークと委任されたサブネットを含んでいるリソース グループの名前を指定して、[az network profile list][az-network-profile-list] コマンドを実行します。

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

コマンドの出力には、ネットワーク プロファイルの完全なリソース ID が表示されます。

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

ネットワーク プロファイル ID が取得できたら、*vnet-deploy-aci.yaml* という名前の新しいファイルに、次の YAML をコピーします。 `networkProfile` で、`id` の値を先ほど取得した ID に置き換え、ファイルを保存します。 この YAML は、*appcontaineryaml* という名前のコンテナー グループを仮想ネットワーク内に作成します。

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

[az container create][az-container-create] コマンドを使ってコンテナー グループをデプロイします。`--file` パラメーターには、YAML ファイルを指定します。

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

デプロイが完了したら、[az container show][az-container-show] コマンドを実行して状態を表示します。

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

### <a name="delete-container-instances"></a>コンテナー インスタンスの削除

作成したコンテナー インスタンスの操作が完了したら、次のコマンドを使ってそれらを削除します。

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>ネットワーク リソースの削除

この機能の初期プレビューでは、先ほど作成したネットワーク リソースを削除するために、いくつかの追加コマンドが必要になります。 この記事の前のセクションに示したサンプル コマンドを使って仮想ネットワークとサブネットを作成した場合は、次のスクリプトを使ってそれらのネットワーク リソースを削除できます。

スクリプトを実行する前に、`RES_GROUP` 変数を、削除する仮想ネットワークとサブネットを含んだリソース グループの名前に設定してください。 スクリプトは Bash シェル用に書式設定されています。 別のシェル (PowerShell やコマンド プロンプトなど) を使用する場合は、変数の割り当てとアクセサーを適宜調整する必要があります。

> [!WARNING]
> このスクリプトを実行すると、リソースが削除されます。 仮想ネットワークと、それに含まれているすべてのサブネットが削除されます。 このスクリプトを実行する前に、仮想ネットワーク (およびそれに含まれるすべてのサブネット) 内の*いずれの*リソースも、今後必要でないことを確認してください。 一度削除すると、**それらのリソースを復旧することはできません**。

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>次の手順

新しい仮想ネットワーク、サブネット、ネットワーク プロファイル、およびコンテナー グループを Resource Manager テンプレートを使用してデプロイするには、「[Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)」(VNet 内に Azure コンテナー グループを作成する) を参照してください。

この記事では、仮想ネットワークのいくつかのリソースと機能について簡潔に説明しました。 これらのトピックについては、Azure Virtual Network のドキュメントで詳しく説明しています。

* [Virtual Network](../virtual-network/manage-virtual-network.md)
* [サブネット](../virtual-network/virtual-network-manage-subnet.md)
* [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
