---
title: Azure 仮想ネットワークへのコンテナー グループのデプロイ
description: Azure コマンド ライン インターフェイスを使用して、新規または既存の Azure 仮想ネットワークにコンテナー グループをデプロイする方法について説明します。
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028881"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>コンテナー インスタンスを Azure 仮想ネットワークにデプロイする

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) では、Azure リソースやオンプレミス リソースのセキュアなプライベート ネットワーキングが提供されます。 Azure 仮想ネットワークにコンテナー グループをデプロイすれば、それらのコンテナーで、仮想ネットワーク内の他のリソースと安全に通信することができます。

この記事では、Azure CLI で [az container create][az-container-create] コマンドを使用して、新しい仮想ネットワークまたは既存の仮想ネットワークにコンテナー グループを展開する方法について説明します。 

ネットワークのシナリオと制限事項については、[Azure Container Instances の仮想ネットワークのシナリオとリソース](container-instances-virtual-network-concepts.md)に関するページを参照してください。

> [!IMPORTANT]
> 仮想ネットワークへのコンテナー グループのデプロイは、一般に Azure Container Instances が利用可能なほとんどのリージョンでは、Linux コンテナーで使用できます。 詳細については、[リージョンとリソースの可用性][container-regions]に関するページをご覧ください。 

この記事の例は、Bash シェル形式で示してあります。 PowerShell やコマンド プロンプトなど、別のシェルを使用する場合は、行継続文字を適切に調整してください。


## <a name="deploy-to-new-virtual-network"></a>新しい仮想ネットワークへのデプロイ

新しい仮想ネットワークにデプロイして、ネットワーク リソースを自動的に作成するには、[az container create][az-container-create] を実行するときに次の項目を指定します。

* 仮想ネットワーク名
* 仮想ネットワーク アドレスのプレフィックス (CIDR 形式)
* サブネット名
* サブネット アドレスのプレフィックス (CIDR 形式)

仮想ネットワークとサブネットのアドレス プレフィックスでは、仮想ネットワークとサブネットそれぞれのアドレス空間を指定します。 これらの値は、CIDR (Classless Inter-domain Routing) 表記法で表されます (例: `10.0.0.0/16`)。 サブネットの操作について詳しくは、「[仮想ネットワーク サブネットの追加、変更、削除](../virtual-network/virtual-network-manage-subnet.md)」をご覧ください。

この方法を使用して最初のコンテナー グループをデプロイした後は、仮想ネットワーク名とサブネット名を指定するか、Azure によって自動作成されたネットワーク プロファイルを指定して、同じサブネットへのデプロイを実行できます。 サブネットは Azure によって Azure Container Instances に委任されるので、サブネットにデプロイできるのは、コンテナー グループ *のみ* となります。

### <a name="example"></a>例

次の [az container create][az-container-create] コマンドは、新しい仮想ネットワークとサブネットの設定を指定します。 仮想ネットワーク内のコンテナー グループのデプロイが[利用できる](container-instances-region-availability.md)リージョンで作成されたリソース グループの名前を指定します。 このコマンドにより、パブリックの Microsoft [aci-helloworld][aci-helloworld] コンテナーがデプロイされます。これは、静的 Web ページを提供する小規模な Node.js Web サーバーを実行するコンテナーです。 次のセクションでは、同じサブネットに 2 つ目のコンテナー グループをデプロイし、2 つのコンテナー インスタンス間の通信をテストします。

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

この方法を使って新しい仮想ネットワークへのデプロイを実行する際には、ネットワーク リソースの作成に数分かかることがあります。 最初のデプロイが済んだら、同じサブネットへの追加のコンテナー グループはより短時間でデプロイできます。

## <a name="deploy-to-existing-virtual-network"></a>既存の仮想ネットワークへのデプロイ

コンテナー グループを既存の仮想ネットワークにデプロイするには:

1. 既存の仮想ネットワーク内にサブネットを作成し、コンテナー グループが既にデプロイされている既存のサブネットを使用するか、または、他のリソースが *いっさい入っていない* 空の既存のサブネットを使用します。
1. [az container create][az-container-create] を使用してコンテナー グループをデプロイし、次のいずれかを指定します。
   * 仮想ネットワーク名とサブネット名
   * 仮想ネットワーク リソース ID、およびサブネット リソース ID (これにより、別のリソース グループから仮想ネットワークを使用できます)
   * ネットワーク プロファイルの名前または ID (これは、[az network profile list][az-network-profile-list] を使用して取得できます)

### <a name="example"></a>例

次の例では、前に作成した同じサブネットに 2 つ目のコンテナー グループをデプロイし、2 つのコンテナー インスタンス間の通信を検証します。

まず、デプロイした 1 つ目のコンテナー グループ (*appcontainer*) の IP アドレスを取得します。

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

出力には、プライベート サブネット内にあるコンテナー グループの IP アドレスが表示されます。 次に例を示します。

```console
10.0.0.4
```

次に、`CONTAINER_GROUP_IP` を `az container show` コマンドで取得した IP アドレスに設定し、次の `az container create` コマンドを実行します。 この 2 つ目のコンテナー (*commchecker*) は、Alpine Linux ベースのイメージを実行し、最初のコンテナー グループのプライベート サブネット IP アドレスに対して `wget` を実行します。

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

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
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

ログ出力を見ると、`wget` が最初のコンテナーに接続し、ローカル サブネット上のプライベート IP アドレスを使用して、最初のコンテナーからインデックス ファイルをダウンロードできたことがわかります。 2 つのコンテナー グループ間のネットワーク トラフィックは、仮想ネットワーク内に維持されたことになります。

### <a name="example---yaml"></a>例 - YAML

YAML ファイル、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)、またはその他のプログラムによる方法 (Python SDK など) を使用して、既存の仮想ネットワークにコンテナーグループをデプロイすることもできます。 

たとえば、YAML ファイルを使用する場合、Azure Container Instances に委任されたサブネットを持つ仮想ネットワークにデプロイできます。 次のプロパティを指定します。

* `ipAddress`: コンテナー グループのプライベート IP アドレス設定。
  * `ports`:開くポート (もしあれば)。
  * `protocol`:開いたポートのプロトコル (TCP または UDP)。
* `networkProfile`: VM の仮想ネットワークとサブネットのネットワーク設定。
  * `id`:`networkProfile` の完全な Resource Manager リソース ID。

ネットワーク プロファイルの ID を取得するには、仮想ネットワークと委任されたサブネットを含んでいるリソース グループの名前を指定して、[az network profile list][az-network-profile-list] コマンドを実行します。

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

サンプル出力:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

ネットワーク プロファイル ID が取得できたら、*vnet-deploy-aci.yaml* という名前の新しいファイルに、次の YAML をコピーします。 `networkProfile` で、`id` の値を先ほど取得した ID に置き換え、ファイルを保存します。 この YAML は、*appcontaineryaml* という名前のコンテナー グループを仮想ネットワーク内に作成します。

```YAML
apiVersion: '2019-12-01'
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

[az container create][az-container-create] コマンドを使って、`--file` パラメーターに YAML ファイル名を指定して、コンテナー グループをデプロイします。

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

デプロイが完了したら、[az container show][az-container-show] コマンドを実行して状態を表示します。 サンプル出力:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="delete-container-instances"></a>コンテナー インスタンスの削除

作成したコンテナー インスタンスの操作が完了したら、次のコマンドを使ってそれらを削除します。

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>ネットワーク リソースの削除

現在このフィーチャーでは、先ほど作成したネットワーク リソースを削除するために、いくつかの追加コマンドが必要です。 この記事の前のセクションに示したサンプル コマンドを使って仮想ネットワークとサブネットを作成した場合は、次のスクリプトを使ってそれらのネットワーク リソースを削除できます。 このスクリプトは、単一のネットワーク プロファイルを持つ単一の仮想ネットワークがリソース グループに含まれていることを前提としています。

スクリプトを実行する前に、`RES_GROUP` 変数を、削除する仮想ネットワークとサブネットを含んだリソース グループの名前に設定してください。 以前に提案された `aci-vnet` の名前を使用しなかった場合は、仮想ネットワークの名前を更新します。 スクリプトは Bash シェル用に書式設定されています。 別のシェル (PowerShell やコマンド プロンプトなど) を使用する場合は、変数の割り当てとアクセサーを適宜調整する必要があります。

> [!WARNING]
> このスクリプトを実行すると、リソースが削除されます。 仮想ネットワークと、それに含まれているすべてのサブネットが削除されます。 このスクリプトを実行する前に、仮想ネットワーク (およびそれに含まれるすべてのサブネット) 内の *いずれの* リソースも、今後必要でないことを確認してください。 一度削除すると、**それらのリソースを復旧することはできません**。

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>次のステップ

新しい仮想ネットワーク、サブネット、ネットワーク プロファイル、およびコンテナー グループを Resource Manager テンプレートを使用してデプロイするには、「[Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)」(VNet 内に Azure コンテナー グループを作成する) を参照してください。

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
