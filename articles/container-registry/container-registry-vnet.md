---
title: サービス エンドポイントを使用してアクセスを制限する
description: Azure 仮想ネットワークのサービス エンドポイントを使用して Azure コンテナー レジストリへのアクセスを制限します。 サービス エンドポイント アクセスは、Premium サービス レベルの機能です。
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: c49595ee4ee79aef264a87dd48bccd03f3d4f5a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773897"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Azure 仮想ネットワークのサービス エンドポイントを使用してコンテナー レジストリへのアクセスを制限する

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) では、Azure リソースやオンプレミス リソースのセキュアなプライベート ネットワーキングが提供されます。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、コンテナー レジストリのパブリック IP アドレスへのアクセスを仮想ネットワークのみに制限することで保護できます。 このエンドポイントでは、Azure バックボーン ネットワーク上のリソースへの最適なルートがトラフィックに提供されます。 仮想ネットワークとサブネットの ID も、各要求と一緒に転送されます。

この記事では、仮想ネットワークでコンテナー レジストリのサービス エンドポイント (プレビュー) を構成する方法について説明します。 

> [!IMPORTANT]
> Azure Container Registry で [Azure Private Link](container-registry-private-link.md) がサポートされ、仮想ネットワークからプライベート エンドポイントをレジストリにデプロイできるようになりました。 プライベート エンドポイントには、プライベート IP アドレスを使用して仮想ネットワーク内からアクセスできます。 ほとんどのネットワーク シナリオでは、サービス エンドポイントの代わりにプライベート エンドポイントを使用することをお勧めします。

レジストリ サービス エンドポイントの構成は、**Premium** コンテナー レジストリ サービス レベルで利用できます。 レジストリ サービスのレベルと制限については、「[Azure Container Registry のサービス レベル](container-registry-skus.md)」を参照してください。

## <a name="preview-limitations"></a>プレビューの制限事項

* 現在、Azure Container Registry のサービス エンドポイントの今後の開発は計画されていません。 代わりに、[プライベート エンドポイント](container-registry-private-link.md)を使用することをお勧めします。
* Azure portal を使用してレジストリにサービス エンドポイントを構成することはできません。
* サービス エンドポイントを使用してコンテナー レジストリにアクセスするためのホストとして使用できるのは、[Azure Kubernetes Service](../aks/intro-kubernetes.md) クラスターまたは Azure [仮想マシン](../virtual-machines/linux/overview.md)だけです。 "*Azure Container Instances を含むその他の Azure サービスはサポートされていません。* "
* Azure Container Registry のサービス エンドポイントは、Azure US Government Cloud または Azure China Cloud ではサポートされていません。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>前提条件

* この記事の Azure CLI 手順を使用するには、Azure CLI バージョン 2.0.58 以降が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

* コンテナー レジストリがまだない場合は、1 つ作成し (Premium レベルが必要)、Docker Hub から `hello-world` などのサンプル イメージをプッシュします。 たとえば、[Azure portal][quickstart-portal] または [Azure CLI][quickstart-cli] を使用してレジストリを作成します。 

* 別の Azure サブスクリプションのサービス エンドポイントを使用してレジストリ アクセスを制限する場合、そのサブスクリプションで Azure Container Registry のリソースプロバイダーを登録する必要があります。 次に例を示します。

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>レジストリへのネットワーク アクセスを構成する

このセクションでは、Azure 仮想ネットワーク内のサブネットからのアクセスを許可するようにコンテナー レジストリを構成します。 Azure CLI を使用した手順が用意されています。

### <a name="add-a-service-endpoint-to-a-subnet"></a>サブネットにサービス エンドポイントを追加する

VM を作成するときに、既定では Azure によって仮想ネットワークが同じリソース グループに作成されます。 仮想ネットワークの名前は仮想マシンの名前に基づいています。 たとえば、仮想マシンに *myDockerVM* という名前を付ける場合、既定の仮想ネットワーク名は *myDockerVMVNET* で、サブネット名は *myDockerVMSubnet* です。 これを確認するには、[az network vnet list][az-network-vnet-list] コマンドを使用します。

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

出力:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

[az network vnet subnet update][az-network-vnet-subnet-update] コマンドを使用して、**Microsoft.ContainerRegistry** サービス エンドポイントをサブネットに追加します。 次のコマンドでは、自分の仮想ネットワークとサブネットの名前に置き換えます。

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

[az network vnet subnet show][az-network-vnet-subnet-show] コマンドを使用して、サブネットのリソース ID を取得します。 これは、後でネットワーク アクセス規則を構成する手順で必要になります。

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

出力:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>レジストリへの既定のネットワーク アクセスを変更する

既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからの接続を許可します。 選択したネットワークへのアクセスを制限するには、既定のアクションを変更してアクセスを拒否します。 次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>レジストリにネットワーク規則を追加する

[az acr network-rule add][az-acr-network-rule-add] コマンドを使用して、VM のサブネットからのアクセスを許可するネットワーク規則をレジストリに追加します。 次のコマンドでは、コンテナー レジストリの名前とサブネットのリソース ID に置き換えます。 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>レジストリへのアクセスの検証

構成が更新されるまで数分間待機した後、VM がコンテナー レジストリにアクセスできることを確認します。 VM への SSH 接続を行い、[az acr login][az-acr-login] コマンドを実行してレジストリにログインします。 

```bash
az acr login --name mycontainerregistry
```

`docker pull` を実行してサンプル イメージをレジストリからプルするなどのレジストリ操作を実行できます。 先頭にレジストリ ログイン サーバー名 (すべて小文字) を付けて、レジストリに適したイメージとタグに置き換えます。

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker によってイメージが VM に正常にプルされます。

この例では、ネットワーク アクセス規則を通じてプライベート コンテナー レジストリにアクセスできることを示します。 ただし、ネットワーク アクセス規則が構成されていないログイン ホストからレジストリにアクセスすることはできません。 `az acr login` コマンドまたは `docker login` コマンドを使用して別のホストからログインしようとした場合、出力は次のようになります。

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>既定のレジストリ アクセスの復元

既定でアクセスを許可するようにレジストリを復元するには、構成されているネットワーク規則をすべて削除します。 次に、アクセスを許可する既定のアクションを設定します。 

### <a name="remove-network-rules"></a>ネットワーク規則を削除する

レジストリに対して構成されているネットワーク規則の一覧を表示するには、次の [az acr network-rule list][az-acr-network-rule-list] コマンドを実行します。

```azurecli
az acr network-rule list --name mycontainerregistry 
```

構成されている規則ごとに [az acr network-rule remove][az-acr-network-rule-remove] コマンドを実行して、各規則を削除します。 次に例を示します。

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>アクセスを許可

次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

すべての Azure リソースを同じリソース グループ内に作成し、それらが不要になった場合は、1 つの [az group delete](/cli/azure/group) コマンドを使用してリソースを削除することもできます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* クライアント ファイアウォールの内側からレジストリ アクセス規則を設定する必要がある場合は、「[ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](container-registry-firewall-access-rules.md)」を参照してください。


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
