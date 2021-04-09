---
title: プライベート リンクを設定する
description: コンテナー レジストリにプライベート エンドポイントを設定し、ローカル仮想ネットワークでプライベート リンク経由のアクセスを有効にします。 プライベート リンク アクセスは、Premium サービス レベルの機能です。
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342302"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Azure Private Link を使用して Azure Container Registry にプライベートで接続する


[Azure Private Link](../private-link/private-link-overview.md) を利用し、レジストリ エンドポイントに仮想ネットワーク プライベート IP アドレスを割り当てることでレジストリへのアクセスを制限します。 仮想ネットワーク上のクライアントとレジストリのプライベート エンドポイント間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットにさらされないようにします。 また、Private Link を使用すると、[Azure ExpressRoute](../expressroute/expressroute-introduction.MD) プライベート ピアリングまたは [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)を介して、オンプレミスからのプライベート レジストリへのアクセスを有効にすることもできます。

設定がレジストリの割り当てられたプライベート IP アドレスに解決されるように、レジストリのプライベート エンドポイント用に [DNS 設定を構成する](../private-link/private-endpoint-overview.md#dns-configuration)ことができます。 DNS 構成では、ネットワーク内のクライアントとサービスは、レジストリの完全修飾ドメイン名 (*myregistry.azurecr.io* など) で引き続きレジストリにアクセスできます。 

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 現時点では、1 つのレジストリに対して最大 10 個のプライベート エンドポイントを設定できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のレベル](container-registry-skus.md)に関するページを参照してください。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>前提条件

* この記事の Azure CLI の手順を使用する場合は、Azure CLI バージョン 2.6.0 以降をお勧めします。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。 または、[Azure Cloud Shell](../cloud-shell/quickstart.md) で実行します。
* コンテナー レジストリがまだない場合は、1 つ作成し (Premium レベルが必要)、Microsoft Container Registry から `mcr.microsoft.com/hello-world` などのサンプル パブリック イメージを[インポート](container-registry-import-images.md)します。 たとえば、[Azure portal][quickstart-portal] または [Azure CLI][quickstart-cli] を使用してレジストリを作成します。
* 別の Azure サブスクリプションのプライベート リンクを使用してレジストリ アクセスを構成するには、そのサブスクリプションで Azure Container Registry のリソース プロバイダーを登録する必要があります。 次に例を示します。

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

この記事の Azure CLI 例では、次の環境変数を使用します。 ご利用の環境に適した値に置き換えてください。 すべての例は、Bash シェル用に書式設定されています。

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>プライベート リンクを設定する - CLI

### <a name="get-network-and-subnet-names"></a>ネットワークとサブネットの名前を取得する

まだない場合は、プライベート リンクを設定するための仮想ネットワークとサブネットの名前が必要になります。 この例では、VM とレジストリのプライベート エンドポイントに同じサブネットを使用します。 しかし、多くのシナリオでは、別のサブネットにエンドポイントを設定します。 

VM を作成するときに、既定では Azure によって仮想ネットワークが同じリソース グループに作成されます。 仮想ネットワークの名前は仮想マシンの名前に基づいています。 たとえば、仮想マシンに *myDockerVM* という名前を付ける場合、既定の仮想ネットワーク名は *myDockerVMVNET* で、サブネット名は *myDockerVMSubnet* です。 [az network vnet list][az-network-vnet-list] コマンドを実行して、環境変数にこれらの値を設定します。

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>サブネットでネットワーク ポリシーを無効にする

プライベート エンドポイントのサブネットで、ネットワーク セキュリティ グループなどの[ネットワーク ポリシーを無効にします](../private-link/disable-private-endpoint-network-policy.md)。 [az network vnet subnet update][az-network-vnet-subnet-update] を使用して、サブネット構成を更新します。

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

プライベート Azure コンテナー レジストリ ドメイン用の[プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)を作成します。 後の手順で、この DNS ゾーン内にレジストリ ドメインの DNS レコードを作成します。

プライベート ゾーンを使用して Azure コンテナー レジストリの既定の DNS 解決をオーバーライドするには、ゾーンに **privatelink.azurecr.io** という名前を付ける必要があります。 次の [az network private-dns zone create][az-network-private-dns-zone-create] コマンドを実行して、プライベート ゾーンを作成します。

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>関連付けリンクを作成する

[az network private-dns link vnet create][az-network-private-dns-link-vnet-create] を実行して、プライベート ゾーンを仮想ネットワークに関連付けます。 この例では、*myDNSLink* というリンクを作成します。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>プライベート レジストリ エンドポイントを作成する

このセクションでは、仮想ネットワーク内にレジストリのプライベート エンドポイントを作成します。 まず、レジストリのリソース ID を取得します。

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

[az network private endpoint create][az-network-private-endpoint-create] コマンドを実行して、レジストリのプライベート エンドポイントを作成します。

次の例では、*myPrivateEndpoint* というエンドポイントと *myConnection* というサービス接続を作成します。 エンドポイントのコンテナー レジストリ リソースを指定するには、`--group-ids registry` を渡します。

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>プライベート IP アドレスを取得する

[az network private endpoint show][az-network-private-endpoint-show] を実行し、ネットワーク インターフェイス ID について、エンドポイントにクエリを実行します。

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

この例のネットワーク インターフェイスには、コンテナーレジストリの 2 つのプライベート IP アドレスが関連付けられています。1 つはレジストリ自体用で、もう 1 つはレジストリのデータ エンドポイント用です。 次の [az resource show][az-resource-show] コマンドでは、コンテナー レジストリとレジストリのデータ エンドポイント用のプライベート IP アドレスが取得されます。

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> レジストリが [geo レプリケート](container-registry-geo-replication.md)されている場合、レジストリ レプリカごとに追加のデータ エンドポイントを照会します。

### <a name="create-dns-records-in-the-private-zone"></a>プライベート ゾーンに DNS レコードを作成する

次のコマンドでは、レジストリ エンドポイントとそのデータ エンドポイントのプライベート ゾーンに DNS レコードを作成します。 たとえば、*westeurope* リージョンに *myregistry* という名前のレジストリがある場合、エンドポイント名は `myregistry.azurecr.io` と `myregistry.westeurope.data.azurecr.io` になります。 

> [!NOTE]
> レジストリが [geo レプリケートされている](container-registry-geo-replication.md)場合、レプリカのデータ エンドポイント IP ごとに追加の DNS レコードを作成します。

まず、[az network private-dns record-set a create][az-network-private-dns-record-set-a-create] を実行し、レジストリ エンドポイントとデータ エンドポイント用の空の A レコード セットを作成します。

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

[az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] コマンドを実行し、レジストリ エンドポイントとデータ エンドポイント用の A レコードを作成します。

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

これで、プライベート リンクが構成され、使用できるようになりました。

## <a name="set-up-private-link---portal"></a>プライベート リンクを設定する - ポータル

レジストリを作成するときにプライベート リンクを設定するか、既存のレジストリにプライベート リンクを追加します。 次の手順では、仮想ネットワークとサブネットがテスト用の VM で既に設定されていることを前提としています。 [新しい仮想ネットワークとサブネットを作成する](../virtual-network/quick-create-portal.md)こともできます。

### <a name="create-a-private-endpoint---new-registry"></a>プライベート エンドポイントの作成 - 新しいレジストリ

1. ポータルでレジストリを作成するとき、 **[基本]** タブの **[SKU]** で **[Premium]** を選択します。
1. **[ネットワーク]** タブを選択します。
1. **[ネットワーク接続]** で **[プライベート エンドポイント]** を選択し、 **[+ 追加]** を選択します。
1. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | 既存のグループの名前を入力するか、新しいものを作成します。|
    | 名前 | 一意の名前を入力します。 |
    | サブリソース |**レジストリ** を選択します|
    | **ネットワーク** | |
    | 仮想ネットワーク| *myDockerVMVNET* など、仮想マシンがデプロイされている仮想ネットワークを選択します。 |
    | Subnet | 仮想マシンがデプロイされている *myDockerVMSubnet* などのサブネットを選択します。 |
    |**プライベート DNS の統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*(新規) privatelink.azurecr.io* を選択します |
    |||
1. 残りのレジストリ設定を構成し、 **[確認および作成]** を選択します。

  ![プライベート エンドポイントを使用してレジストリを作成する](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>プライベート エンドポイントの作成 - 既存のレジストリ

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で **[ネットワーク]** を選択します。
1. **[プライベート エンドポイント]** タブで **[+ プライベートエンドポイント]** を選択します。
1. **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | 既存のグループの名前を入力するか、新しいものを作成します。|
    | **インスタンスの詳細** |  |
    | 名前 | 名前を入力します。 |
    |リージョン|リージョンを選択します。|
    |||
5. **次へ:リソース** を選択します。
6. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | **[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **[Microsoft.ContainerRegistry/registries]** を選択します。 |
    | リソース |レジストリの名前を選択します|
    |ターゲット サブリソース |**レジストリ** を選択します|
    |||
7. **次へ:構成** を選択します。
8. 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| *myDockerVMVNET* など、仮想マシンがデプロイされている仮想ネットワークを選択します。 |
    | Subnet | 仮想マシンがデプロイされている *myDockerVMSubnet* などのサブネットを選択します。 |
    |**プライベート DNS の統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*(新規) privatelink.azurecr.io* を選択します |
    |||

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
2. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

プライベート エンドポイントが作成された後、ポータルの **[プライベート エンドポイント]** ページにプライベート ゾーンの DNS 設定が表示されます。

1. ポータルで、コンテナー レジストリに移動し、 **[設定]、[ネットワーク]** の順に選択します。
1. **[プライベート エンドポイント]** タブで、作成したプライベートエンドポイントを選択します。
1. **[概要]** ページで、リンク設定とカスタム DNS 設定を確認します。

  ![エンドポイントの DNS 設定](./media/container-registry-private-link/private-endpoint-overview.png)

これで、プライベート リンクが構成され、使用できるようになりました。

## <a name="disable-public-access"></a>パブリック アクセスを無効にする

さまざまなシナリオで、パブリック ネットワークからのレジストリ アクセスを無効にします。 この構成により、仮想ネットワークの外にいるクライアントがレジストリ エンドポイントに到達するのを防ぐことができます。 

### <a name="disable-public-access---cli"></a>パブリック アクセスを無効にする - CLI

Azure CLI を使用してパブリック アクセスを無効にするには、[az acr update][az-acr-update] を実行し、`--public-network-enabled` を `false` に設定します。 

> [!NOTE]
> `public-network-enabled` 引数には、Azure CLI 2.6.0 以降が必要です。 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>パブリック アクセスを無効にする - ポータル

1. ポータルで、コンテナー レジストリに移動し、 **[設定] > [ネットワーク]** の順に選択します。
1. **[パブリック アクセス]** タブの **[Allow public network access]\(パブリック ネットワーク アクセスを許可する\)** で、 **[無効]** を選択します。 次に、 **[保存]** を選択します。

## <a name="validate-private-link-connection"></a>プライベート リンク接続を確認する

プライベート エンドポイントのサブネット内のリソースがプライベート IP アドレスを使用してレジストリに接続され、適切なプライベート DNS ゾーンの統合が行われていることを確認する必要があります。

プライベート リンク接続を確認するには、仮想ネットワークに設定した仮想マシンに SSH 接続します。

`nslookup` や `dig` などのユーティリティを実行し、プライベート リンク経由でレジストリの IP アドレスを検索します。 次に例を示します。

```bash
dig $REGISTRY_NAME.azurecr.io
```

出力例では、サブネットのアドレス空間にあるレジストリの IP アドレスが示されています。

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

この結果と、パブリック エンドポイント経由の同じレジストリの `dig` 出力のパブリック IP アドレスを比較します。

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>プライベート リンク経由のレジストリ操作

また、サブネット内の仮想マシンからレジストリ操作を実行できることを確認します。 仮想マシンへの SSH 接続を行い、[az acr login][az-acr-login] を実行してレジストリにログインします。 VM の構成によっては、次のコマンドの前に `sudo` を付けることが必要になる場合があります。

```bash
az acr login --name $REGISTRY_NAME
```

`docker pull` などのレジストリ操作を実行して、サンプル イメージをレジストリからプルします。 `hello-world:v1` を、レジストリに適したイメージとタグに置き換え、前にレジストリ ログイン サーバー名 (すべて小文字) を付けます。

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker によってイメージが VM に正常にプルされます。

## <a name="manage-private-endpoint-connections"></a>プライベート エンドポイント接続を管理する

Azure portal を使用するか、[az acr private-endpoint-connection][az-acr-private-endpoint-connection] コマンド グループのコマンドを使って、レジストリのプライベート エンドポイント接続を管理します。 操作には、レジストリのプライベート エンドポイント接続の承認、削除、一覧表示、拒否、または詳細の表示が含まれます。

たとえば、レジストリのプライベート エンドポイント接続の一覧を表示するには、[az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] コマンドを実行します。 次に例を示します。

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

この記事の手順を使用してプライベート エンドポイント接続を設定すると、レジストリでは、レジストリに対する Azure RBAC アクセス許可を持つクライアントとサービスからの接続が自動的に受け入れられます。 エンドポイントを設定して、手動による接続の承認を要求することができます。 プライベート エンドポイント接続を承認および拒否する方法については、「[プライベート エンドポイント接続を管理する](../private-link/manage-private-endpoint.md)」を参照してください。

## <a name="add-zone-records-for-replicas"></a>レプリカのゾーン レコードを追加する

この記事で紹介したように、プライベート エンドポイント接続をレジストリに追加するとき、そのレジストリとそのデータ エンドポイントのために、レジストリが[レプリケート](container-registry-geo-replication.md)されるリージョンで `privatelink.azurecr.io` ゾーンの DNS レコードを作成します。 

新しいレプリカを後で追加する場合、そのリージョンでデータ エンドポイントの新しいゾーン レコードを手動で追加する必要があります。 たとえば、場所 *northeurope* で *myregistry* のレプリカを作成する場合、`myregistry.northeurope.data.azurecr.io` のゾーン レコードを追加します。 手順については、この記事の「[プライベート ゾーンに DNS レコードを作成する](#create-dns-records-in-the-private-zone)」を参照してください。

## <a name="dns-configuration-options"></a>DNS の構成オプション

この例のプライベート エンドポイントは、基本仮想ネットワークに関連付けられているプライベート DNS ゾーンと統合されます。 このセットアップでは、Azure から提供される DNS サー日を直接利用し、レジストリのパブリック FQDN を仮想ネットワークのそのプライベート IP アドレスに解決します。 

プライベート リンクでは、カスタム DNS ソリューションによるものも含め、プライベート ゾーンを使用する追加の DNS 構成シナリオがサポートされます。 たとえば、カスタム DNS ソリューションを仮想ネットワークにデプロイしたり、ネットワークのオンプレミスで VPN ゲートウェイまたは Azure ExpressRoute を利用して仮想ネットワークに接続したりします。 

そのようなシナリオでレジストリのパブリック FQDN をプライベート IP アドレスに解決するには、Azure DNS サービスのサーバーレベル フォワーダーを構成する必要があります (168.63.129.16)。 構成のオプションと手順は、厳密には、既存のネットワークや DNS によって異なります。 サンプルが必要であれば、[Azure プライベート エンドポイント DNS 構成](../private-link/private-endpoint-dns.md)に関するページを参照してください。

> [!IMPORTANT]
> 高可用性を実現するために複数のリージョンでプライベート エンドポイントを作成した場合は、リージョンごとに個別のリソース グループを使用し、仮想ネットワークとそれに関連付けられたプライベート DNS ゾーンをそこに配置することをお勧めします。 この構成を使用すると、同じプライベート DNS ゾーンの共有による予期しない DNS 解決が発生するのを防ぎます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

すべての Azure リソースを同じリソース グループ内に作成し、それらが不要になった場合は、1 つの [az group delete](/cli/azure/group) コマンドを使用してリソースを削除することもできます。

```azurecli
az group delete --name $RESOURCE_GROUP
```

ポータルでリソースをクリーンアップするには、リソース グループに移動します。 リソース グループが読み込まれたら、 **[リソース グループの削除]** をクリックして、リソース グループとそこに格納されているリソースを削除します。

## <a name="next-steps"></a>次のステップ

* Private Link の詳細については、[Azure Private Link](../private-link/private-link-overview.md) に関するドキュメントを参照してください。
* クライアント ファイアウォールの内側からレジストリ アクセス規則を設定する必要がある場合は、「[ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](container-registry-firewall-access-rules.md)」を参照してください。

<!-- LINKS - external -->
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
