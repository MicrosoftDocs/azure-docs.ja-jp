---
title: 仮想ネットワークからの Azure コンテナー レジストリへのアクセスを制限する
description: Azure コンテナー レジストリへは、Azure 仮想ネットワーク内のリソースから、またはパブリック IP アドレス範囲からのみアクセスできます。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 06e45127f940e01de5f3ceeefc354014a88014db
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514402"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure 仮想ネットワークまたはファイアウォール規則を使用して Azure コンテナー レジストリへのアクセスを制限する

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) では、Azure リソースやオンプレミス リソースのセキュアなプライベート ネットワーキングが提供されます。 Azure 仮想ネットワークからプライベート Azure コンテナー レジストリへのアクセスを制限することで、仮想ネットワーク内のリソースのみがレジストリにアクセスするようにすることができます。 クロスプレミスのシナリオでは、特定の IP アドレスからのレジストリ アクセスだけを許可するファイアウォール規則を構成することもできます。

この記事では、Azure コンテナー レジストリへのアクセスを制限するネットワーク アクセス規則を作成する 2 つのシナリオ (仮想ネットワークにデプロイされている仮想マシンから、または VM のパブリック IP アドレスから) を示します。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="preview-limitations"></a>プレビューの制限事項

* ネットワーク アクセス規則で構成できるのは **Premium** コンテナー レジストリだけです。 レジストリ サービス レベルについては、[「Azure Container Registry SKU」](container-registry-skus.md)をご覧ください。 

* 仮想ネットワーク内のコンテナー レジストリにアクセスするためのホストとして使用できるのは、[Azure Kubernetes Service](../aks/intro-kubernetes.md) クラスターまたは Azure [仮想マシン](../virtual-machines/linux/overview.md)だけです。 *Azure Container Instances を含むその他の Azure サービスは現在サポートされていません。*

* [ACR タスク](container-registry-tasks-overview.md)操作は、仮想ネットワークでアクセスされるコンテナー レジストリでは現在サポートされていません。

* 各レジストリでは、最大 100 個の仮想ネットワーク規則がサポートされます。

## <a name="prerequisites"></a>前提条件

* この記事の Azure CLI 手順を使用するには、Azure CLI バージョン 2.0.58 以降が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

* コンテナー レジストリがまだない場合は、1 つ作成し (Premium SKU が必要)、Docker Hub から `hello-world` などのサンプル イメージをプッシュします。 たとえば、[Azure portal][quickstart-portal] or the [Azure CLI][quickstart-cli] を使用してレジストリを作成します。 

## <a name="about-network-rules-for-a-container-registry"></a>コンテナー レジストリのネットワーク規則について

既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 仮想ネットワークを使用すると、AKS クラスターや Azure VM などの Azure リソースのみが、ネットワーク境界を越えずにレジストリに安全にアクセスできます。 特定のパブリック インターネット IP アドレスの範囲をホワイトリストに登録するネットワーク ファイアウォール規則を構成することもできます。 

レジストリへのアクセスを制限するには、最初にレジストリの既定のアクションを変更して、すべてのネットワーク接続が拒否されるようにします。 次に、ネットワーク アクセス規則を追加します。 ネットワーク規則を介してアクセス権を付与されているクライアントは、引き続き[コンテナー レジストリに対して認証](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)され、データへのアクセスを承認される必要があります。

### <a name="service-endpoint-for-subnets"></a>サブネットのサービス エンドポイント

仮想ネットワーク内のサブネットからのアクセスを許可するには、Azure Container Registry サービスの[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加する必要があります。 

Azure Container Registry のようなマルチ テナント サービスでは、すべての顧客に対して 1 セットの IP アドレスを使用します。 サービス エンドポイントでは、レジストリにアクセスするためのエンドポイントが割り当てられます。 このエンドポイントでは、Azure バックボーン ネットワーク上のリソースへの最適なルートがトラフィックに提供されます。 仮想ネットワークとサブネットの ID も、各要求と一緒に転送されます。

### <a name="firewall-rules"></a>ファイアウォール規則

IP ネットワーク規則では、許可するインターネット アドレスの範囲は、*16.17.18.0/24* などの CIDR 表記法または *16.17.18.19* のような個々の IP アドレスを使用して指定します。 IP ネットワーク規則は、*パブリック* インターネット IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP 規則では許可されません。

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 対応仮想マシンの作成

この記事では、Docker 対応の Ubuntu VM を使用して Azure コンテナー レジストリにアクセスします。 レジストリに Azure Active Directory 認証を使用するには、VM に [Azure CLI][azure-cli] もインストールします。 既に Azure 仮想マシンがある場合は、この作成手順を省略します。

仮想マシンとコンテナー レジストリに同じリソース グループを使用できます。 このセットアップでは最終的にクリーンアップが簡素化されますが、必須ではありません。 仮想マシンと仮想ネットワークに別々のリソース グループを作成することを選択する場合は、[az group create][az-group-create] を実行します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *westcentralus* に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

ここで、[az vm create][az-vm-create] を使用して、既定の Ubuntu Azure 仮想マシンをデプロイします。 次の例では、*myDockerVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM が作成されるまで､数分間かかります｡ コマンドが完了したら、Azure CLI によって表示された `publicIpAddress` をメモします。 このアドレスは、VM への SSH 接続の確立と、オプションで後のファイアウォール規則のセットアップに使用します。

### <a name="install-docker-on-the-vm"></a>VM に Docker をインストールする

VM が実行されたら、VM への SSH 接続を作成します。 *publicIpAddress* を VM のパブリック IP アドレスに置き換えます。

```bash
ssh azureuser@publicIpAddress
```

次のコマンドを実行して、Ubuntu VM に Docker をインストールします。

```bash
sudo apt install docker.io -y
```

インストールの後、次のコマンドを実行して、VM 上で Docker が正しく実行されていることを確認します。

```bash
sudo docker run -it hello-world
```

出力:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI のインストール

Ubuntu 仮想マシンに Azure CLI をインストールするには、「[apt での Azure CLI のインストール](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)」の手順に従います。 この記事の場合は、バージョン 2.0.58 以降をインストールするようにしてください。

SSH 接続を終了します。

## <a name="allow-access-from-a-virtual-network"></a>仮想ネットワークからのアクセスの許可

このセクションでは、Azure 仮想ネットワーク内のサブネットからのアクセスを許可するようにコンテナー レジストリを構成します。 Azure CLI と Azure portal を使用した同等の手順が提供されます。

### <a name="allow-access-from-a-virtual-network---cli"></a>仮想ネットワークからのアクセスの許可 - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>サブネットにサービス エンドポイントを追加する

VM を作成するときに、既定では Azure によって仮想ネットワークが同じリソース グループに作成されます。 仮想ネットワークの名前は仮想マシンの名前に基づいています。 たとえば、仮想マシンに *myDockerVM* という名前を付ける場合、既定の仮想ネットワーク名は *myDockerVMVNET* で、サブネット名は *myDockerVMSubnet* です。 これは、Azure portal で、または [az network vnet list][az-network-vnet-list] コマンドを使用して確認します。

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
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

#### <a name="change-default-network-access-to-registry"></a>レジストリへの既定のネットワーク アクセスを変更する

既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからの接続を許可します。 選択したネットワークへのアクセスを制限するには、既定のアクションを変更してアクセスを拒否します。 次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>レジストリにネットワーク規則を追加する

[az acr network-rule add][az-acr-network-rule-add] コマンドを使用して、VM のサブネットからのアクセスを許可するネットワーク規則をレジストリに追加します。 次のコマンドでは、コンテナー レジストリの名前とサブネットのリソース ID に置き換えます。 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

「[レジストリへのアクセスの検証](#verify-access-to-the-registry)」に進みます。

### <a name="allow-access-from-a-virtual-network---portal"></a>仮想ネットワークからのアクセスの許可 - ポータル

#### <a name="add-service-endpoint-to-subnet"></a>サブネットにサービス エンドポイントを追加する

VM を作成するときに、既定では Azure によって仮想ネットワークが同じリソース グループに作成されます。 仮想ネットワークの名前は仮想マシンの名前に基づいています。 たとえば、仮想マシンに *myDockerVM* という名前を付ける場合、既定の仮想ネットワーク名は *myDockerVMVNET* で、サブネット名は *myDockerVMSubnet* です。

Azure Container Registry のサービス エンドポイントをサブネットに追加するには:

1. [Azure portal][azure-portal] の上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
1. 仮想ネットワークの一覧から、*myDockerVMVNET* など、仮想マシンがデプロイされる仮想ネットワークを選択します。
1. **[設定]** で、 **[サブネット]** を選択します。
1. *myDockerVMSubnet* など、仮想マシンがデプロイされるサブネットを選択します。
1. **[サービス エンドポイント]** で、**Microsoft.ContainerRegistry** を選択します。
1. **[保存]** を選択します。

![サブネットにサービス エンドポイントを追加する][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>レジストリへのネットワーク アクセスを構成する

既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからの接続を許可します。 仮想ネットワークへのアクセスを制限するには:

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で、 **[ファイアウォールと仮想ネットワーク]** を選択します。
1. 既定でアクセスを拒否するには、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。 
1. **[既存の仮想ネットワークを追加]** を選択し、サービス エンドポイントで構成した仮想ネットワークとサブネットを選択します。 **[追加]** を選択します。
1. **[保存]** を選択します。

![コンテナー レジストリの仮想ネットワークの構成][acr-vnet-portal]

「[レジストリへのアクセスの検証](#verify-access-to-the-registry)」に進みます。

## <a name="allow-access-from-an-ip-address"></a>IP アドレスからのアクセスの許可

このセクションでは、特定の IP アドレスまたは範囲からのアクセスを許可するようにコンテナー レジストリを構成します。 Azure CLI と Azure portal を使用した同等の手順が提供されます。

### <a name="allow-access-from-an-ip-address---cli"></a>IP アドレスからのアクセスの許可 - CLI

#### <a name="change-default-network-access-to-registry"></a>レジストリへの既定のネットワーク アクセスを変更する

まだ行っていない場合は、既定でアクセスを拒否するようにレジストリ構成を更新します。 次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>レジストリからネットワーク規則を削除する

以前に、VM のサブネットからのアクセスを許可するネットワーク規則を追加した場合は、サブネットのサービス エンドポイントとネットワーク規則を削除します。 [az acr network-rule remove][az-acr-network-rule-remove] コマンドでは、以前の手順で取得したコンテナー レジストリの名前とサブネットのリソース ID に置き換えます。 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>レジストリにネットワーク規則を追加する

[az acr network-rule add][az-acr-network-rule-add] コマンドを使用して、VM の IP アドレスからのアクセスを許可するネットワーク規則をレジストリに追加します。 次のコマンドでは、コンテナー レジストリの名前と VM のパブリック IP アドレスに置き換えます。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

「[レジストリへのアクセスの検証](#verify-access-to-the-registry)」に進みます。

### <a name="allow-access-from-an-ip-address---portal"></a>IP アドレスからのアクセスの許可 - ポータル

#### <a name="remove-existing-network-rule-from-registry"></a>レジストリから既存のネットワーク規則を削除する

以前に、VM のサブネットからのアクセスを許可するネットワーク規則を追加した場合は、既存の規則を削除します。 別の VM からレジストリにアクセスする場合は、このセクションをスキップします。

* Azure Container Registry のサブネットのサービス エンドポイントを削除するようにサブネット設定を更新します。 

  1. [Azure portal][azure-portal] で、仮想マシンがデプロイされている仮想ネットワークに移動します。
  1. **[設定]** で、 **[サブネット]** を選択します。
  1. 仮想マシンがデプロイされているサブネットを選択します。
  1. **[サービス エンドポイント]** で、**Microsoft.ContainerRegistry** のチェックボックスをオフにします。 
  1. **[保存]** を選択します。

* サブネットにレジストリへのアクセスを許可するネットワーク規則を削除します。

  1. ポータルで、自分のコンテナー レジストリに移動します。
  1. **[設定]** で、 **[ファイアウォールと仮想ネットワーク]** を選択します。
  1. **[仮想ネットワーク]** で、仮想ネットワークの名前を選択し、 **[削除]** を選択します。
  1. **[保存]** を選択します。

#### <a name="add-network-rule-to-registry"></a>レジストリにネットワーク規則を追加する

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で、 **[ファイアウォールと仮想ネットワーク]** を選択します。
1. まだ行っていない場合は、 **[選択されたネットワーク]** からのアクセスを許可することを選択します。 
1. **[仮想ネットワーク]** で、ネットワークが選択されていないことを確認します。
1. **[ファイアウォール]** で、VM のパブリック IP アドレスを入力します。 または、VM の IP アドレスを含むアドレス範囲を CIDR 表記法で入力します。
1. **[保存]** を選択します。

![コンテナー レジストリのファイアウォール規則の構成][acr-vnet-firewall-portal]

「[レジストリへのアクセスの検証](#verify-access-to-the-registry)」に進みます。

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

この例では、ネットワーク アクセス規則を通じてプライベート コンテナー レジストリにアクセスできることを示します。 ただし、ネットワーク アクセス規則が構成されていない別のログイン ホストからレジストリにアクセスすることはできません。 `az acr login` コマンドまたは `docker login` コマンドを使用して別のホストからログインしようとした場合、出力は次のようになります。

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>既定のレジストリ アクセスの復元

既定でアクセスを許可するようにレジストリを復元するには、構成されているネットワーク規則をすべて削除します。 次に、アクセスを許可する既定のアクションを設定します。 Azure CLI と Azure portal を使用した同等の手順が提供されます。

### <a name="restore-default-registry-access---cli"></a>既定のレジストリ アクセスの復元 - CLI

#### <a name="remove-network-rules"></a>ネットワーク規則を削除する

レジストリに対して構成されているネットワーク規則の一覧を表示するには、次の [az acr network-rule list][az-acr-network-rule-list] コマンドを実行します。

```azurecli
az acr network-rule list--name mycontainerregistry 
```

構成されている規則ごとに [az acr network-rule remove][az-acr-network-rule-remove] コマンドを実行して、各規則を削除します。 例:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>アクセスを許可

次の [az acr update][az-acr-update] コマンドでは、自分のレジストリの名前に置き換えます。
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>既定のレジストリ アクセスの復元 - ポータル


1. ポータルで、コンテナー レジストリに移動し、 **[ファイアウォールと仮想ネットワーク]** を選択します。
1. **[仮想ネットワーク]** で、各仮想ネットワークを選択し、 **[削除]** を選択します。
1. **[ファイアウォール]** で、各アドレス範囲を選択し、[削除] アイコンを選択します。
1. **[許可するアクセス元]** で **[すべてのネットワーク]** を選択します。 
1. **[保存]** を選択します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

すべての Azure リソースを同じリソース グループ内に作成し、それらが不要になった場合は、1 つの [az group delete](/cli/azure/group) コマンドを使用してリソースを削除することもできます。

```azurecli
az group delete --name myResourceGroup
```

ポータル上でリソースをクリーンアップするには、myResourceGroup リソース グループに移動します。 リソース グループが読み込まれたら、 **[リソース グループの削除]** をクリックして、リソース グループとそこに格納されているリソースを削除します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワークのいくつかのリソースと機能について簡潔に説明しました。 これらのトピックについては、Azure Virtual Network のドキュメントで詳しく説明しています。

* [Virtual Network](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [サブネット](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
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
