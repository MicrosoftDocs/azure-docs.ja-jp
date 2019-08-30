---
title: Azure クラシック CLI を使用して完全な Linux 環境を作成する | Microsoft Docs
description: Azure クラシック CLI を使用して、ストレージ、Linux VM、仮想ネットワークとサブネット、ロード バランサー、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: aaf91aa81be5fc4c5944dde804798a61ceffc5a6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083715"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Azure クラシック CLI を使用して完全な Linux 環境を作成する
この記事では、開発と単純なコンピューティングに役立つ VM のペアを含む単純なネットワークとロード バランサーを構築します。 ここでは、インターネット上のどこからでも接続できる、2 台のセキュリティで保護された実用的な Linux VM を構築するまで、各コマンドの説明を交えながらプロセスについて説明します。 この記事を理解すると、より複雑なネットワークや環境に進むことができます。

その過程で、Resource Manager デプロイ モデルによって提供される依存関係階層とその性能についても説明します。 システムがどのように構築されているかをいったん理解すると、 [Azure Resource Manager テンプレート](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して、より短時間でシステムを再構築することができます。 環境の各部分がどのように組み合わさっているかがわかると、それらを自動化するためのテンプレートの作成はより簡単になります。

環境には以下を含みます。

* 可用性セット内の 2 つの VM
* ポート 80 の負荷分散規則が構成されたロード バランサー
* 不要なトラフィックからVM を保護するネットワーク セキュリティ グループ ルール (NSG)

このカスタム環境を作成するには、Resource Manager モード (`azure config mode arm`) の最新の [Azure クラシック CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) が必要です。 JSON 解析ツールも必要です。 この例では、 [jq](https://stedolan.github.io/jq/)を使用します。


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure クラシック CLI](#quick-commands) - クラシック デプロイ モデルと Resource Manager デプロイ モデル用の CLI (本記事)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM を Azure にアップロードするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-walkthrough)。

[Azure クラシック CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) にログインし、Resource Manager モードを使用していることを確認します。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。

リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
azure group create -n myResourceGroup -l westeurope
```

JSON パーサーを使用して、リソース グループを確認します。

```azurecli
azure group show myResourceGroup --json | jq '.'
```

ストレージ アカウントを作成します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。 (ストレージ アカウント名は一意である必要があるため、独自の一意の名前を入力してください)。

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

JSON パーサーを使用して、ストレージ アカウントを確認します。

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

仮想ネットワークを作成する。 次の例では、`myVnet` という名前の仮想ネットワークを作成します。

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

サブネットを作成します。 次の例では、`mySubnet` という名前のサブネットを作成します。

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

JSON パーサーを使用して、仮想ネットワークとサブネットを確認します。

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

パブリック IP を作成します。 次の例では、DNS 名が `mypublicdns` で `myPublicIP` という名前のパブリック IP を作成します。 (DNS 名は一意である必要があるため、独自の一意の名前を入力してください)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

ロード バランサーを作成します。 次の例では、`myLoadBalancer` という名前のロード バランサーを作成します。

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

ロード バランサーのフロントエンド IP プールを作成して、パブリック IP を関連付けます。 次の例では、`mySubnetPool` という名前のフロントエンド IP プールを作成します。

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

ロード バランサーのバックエンド IP プールを作成します。 次の例では、`myBackEndPool` という名前のバックエンド IP プールを作成します。

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

ロード バランサーの SSH 受信ネットワーク アドレス変換 (NAT) 規則を作成します。 次の例では、2 つのロード バランサー規則 `myLoadBalancerRuleSSH1` と `myLoadBalancerRuleSSH2` を作成します。

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

ロード バランサーの Web 受信 NAT 規則を作成します。 次の例では、ロード バランサー規則 `myLoadBalancerRuleWeb` を作成します。

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

ロード バランサーの正常性プローブを作成します。 次の例では、`myHealthProbe` という名前の TCP プローブを作成します。

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

JSON パーサーを使用したロード バランサー、IP プール、および NAT 規則を確認します。

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

最初のネットワーク インターフェイス カード (NIC) を作成します。 `#####-###-###` セクションは、実際の Azure サブスクリプション ID に置き換えます。 サブスクリプション ID は、作成しているリソースを調べると、**jq** の出力に記載されています。 `azure account list` でサブスクリプション ID を表示することもできます。

次の例では、`myNic1` という名前の NIC を作成します。

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

2 つ目の NIC を作成します。 次の例では、`myNic2` という名前の NIC を作成します。

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

JSON のパーサーを使って 2 つの NIC を確認します。

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

ネットワーク セキュリティ グループの 2 つの受信規則を追加します。 次の例では、2 つの規則 `myNetworkSecurityGroupRuleSSH` と `myNetworkSecurityGroupRuleHTTP` を作成します。

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

JSON パーサーを使ってネットワーク セキュリティ グループと受信規則を確認します。

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

ネットワーク セキュリティ グループを 2 つの NIC にバインドします。

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

可用性セットを作成します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

最初の Linux VM を作成します。 次の例では、`myVM1` という名前の VM を作成します。

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

2 つ目の Linux VM を作成します。 次の例では、`myVM2` という名前の VM を作成します。

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

すべてが構築されたことを確認するのに JSON パーサーを使用します。

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

新しいインスタンスを簡単に再作成するため、テンプレートに新しい環境をエクスポートします。

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
次の手順では、環境を構築する際に各コマンドがどのように機能するかについて説明します。 これらの概念は、開発または実稼働用のカスタム環境を作成するのに役立ちます。

[Azure クラシック CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) にログインし、Resource Manager モードを使用していることを確認します。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。

## <a name="create-resource-groups-and-choose-deployment-locations"></a>リソース グループの作成とデプロイ先の選択
Azure リソース グループは、リソース デプロイの論理的な管理を可能にする構成情報とメタデータを含む論理的なデプロイ エンティティです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
azure group create --name myResourceGroup --location westeurope
```

出力:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
VM ディスクと追加するその他のデータ ディスク用のストレージ アカウントが必要になります。 リソース グループを作成したら、そのほぼ直後にストレージ アカウントを作成します。

ここでは、 `azure storage account create` コマンドを使用し、アカウントの場所、アカウントを制御するリソース グループ、必要なストレージ サポートの種類を渡します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

出力:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

`azure group show` コマンドを使ってリソース グループを調べるには、[jq](https://stedolan.github.io/jq/) ツールを `--json` Azure CLI オプションと共に使います (**jsawk** または任意の言語ライブラリを使って、JSON を解析できます)。

```azurecli
azure group show myResourceGroup --json | jq '.'
```

出力:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

CLI を使ってストレージ アカウントを調査するには、まず、アカウント名とキーを設定する必要があります。 次の例にあるストレージ アカウントの名前を選択したものに置き換えます。

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

その後、ストレージ情報は簡単に表示できるようになります。

```azurecli
azure storage container list
```

出力:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成
次に、VM を作成できるように、Azure で実行中の仮想ネットワークとサブネットを作成する必要があります。 次の例では、`192.168.0.0/16` アドレス プレフィックスで `myVnet` という名前の仮想ネットワークを作成します。

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

出力:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

ここで、もう一度 `azure group show` の --json オプションと `jq` を使用して、リソースをどのように作成するかを見てみましょう。 この段階で、`storageAccounts` リソースと `virtualNetworks` リソースが作成されています。  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

出力:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

次に、VM のデプロイ先となるサブネットを `myVnet` 仮想ネットワーク内に作成します。 既に作成したリソース (`myResourceGroup` リソース グループと `myVnet` 仮想ネットワーク) と `azure network vnet subnet create` コマンドを使用します。 次の例では、サブネット アドレス プレフィックスが `192.168.1.0/24` で `mySubnet` という名前のサブネットを追加します。

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

出力:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

サブネットは論理的に仮想ネットワーク内に存在するため、サブネットの情報を探すには、若干異なるコマンドを使用します。 使用するコマンドは `azure network vnet show`ですが、JSON 出力の確認には引き続き `jq`を使用します。

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

出力:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
パブリック IP アドレス (PIP) を作成して、ロード バランサーに割り当てます。 `azure network public-ip create` コマンドを使用して、インターネットから VM に接続できます。 既定のアドレスは動的であるため、`--domain-name-label` オプションを使って、**cloudapp.azure.com** ドメインに名前付き DNS エントリを作成します。 次の例では、DNS 名が `mypublicdns` で `myPublicIP` という名前のパブリック IP を作成します。 DNS 名は一意である必要があるため、独自の一意の DNS 名を指定します。

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

出力:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

パブリック IP アドレスは最上位のリソースでもあるため、 `azure group show`を使用して確認できます。

```azurecli
azure group show myResourceGroup --json | jq '.'
```

出力:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

完全な `azure network public-ip show` コマンドを使用して、サブドメインの完全修飾ドメイン名 (FQDN) など、リソースの詳細を調査することができます。 パブリック IP アドレス リソースは論理的に割り当てられていますが、特定のアドレスはまだ割り当てられていません。 IP アドレスを取得するには、ロード バランサーが必要ですが、まだ作成されていません。

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

出力:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>ロード バランサーと IP プールの作成
ロード バランサーを作成するときに複数の VM にトラフィックを分散できます。 メンテナンスや大きな負荷が発生した場合に、ユーザー要求に応答する複数の VM を実行することで、アプリケーションに冗長性も加わります。 次の例では、`myLoadBalancer` という名前のロード バランサーを作成します。

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

出力:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

現在ロード バランサーは全く空であるため、いくつかの IP プールを作成しましょう。 ロード バランサーには、フロントエンドに 1 つ、バックエンドに 1 つの 2 つの IP プールを作成します。 フロントエンド IP プールは公開されます。 以前に作成した PIP を割り当てる場所にもなります。 接続する VM の保存先として、バックエンド プールを使用します。 これにより、トラフィックは、VM にロード バランサーを経由できます。

まず、フロント エンド IP プールを作成します。 次の例では、`myFrontEndPool` という名前のフロントエンド プールを作成します。

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

出力:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

以前に作成した `myPublicIP` を渡すために、`--public-ip-name` スイッチを使っている方法に注意してください。 パブリック IP アドレスをロード バランサーに割り当てることで、インターネット経由で VM に到達できるようになります。

次に、バックエンド トラフィック用に 2 つ目の IP プールを作成します。 次の例では、`myBackEndPool` という名前のバックエンド プールを作成します。

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

出力:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

`azure network lb show` によって JSON 出力を調べて、ロード バランサーの様子を確認できます。

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

出力:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>ロード バランサーの NAT 規則の作成
トラフィックがロード バランサーを経由するようにするには、受信アクションまたは送信アクションを指定するネットワーク アドレス変換 (NAT) 規則を作成する必要があります。 使用するプロトコルを指定して、必要に応じて、外部ポートを内部ポートにマップします。 この環境では、SSH がロード バランサーを経由して VM に到達できるようにするいくつかの規則を作成しましょう。 VM の TCP ポート 22 (これは後で作成します) に送信する TCP ポート 4222 と 4223 を設定します。 次の例では、`myLoadBalancerRuleSSH1` という名前の規則を作成して、TCP ポート 4222 をポート 22 にマップします。

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

出力:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

SSH に対する 2 つ目の NAT 規則について、手順を繰り返します。 次の例では、`myLoadBalancerRuleSSH2` という名前の規則を作成して、TCP ポート 4223 をポート 22 にマップします。

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

さらに、規則を IP プールに接続する Web トラフィック用の TCP ポート 80 の NAT 規則の作成します。 規則を個々の VM に接続するのではなく、IP プールに接続すると、IP プールの VM を追加または削除できます。 ロード バランサーは、トラフィックのフローを自動的に調整します。 次の例では、`myLoadBalancerRuleWeb` という名前の規則を作成して、TCP ポート 80 をポート 80 にマップします。

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

出力:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブの作成
正常性プローブは、ロード バランサーの背後にある VM を定期的にチェックして、それらが定義されているとおりに動作し、要求に応答していることを確認します。 動作していない場合は、運用から削除され、ユーザーがそれらに転送されないようにします。 間隔およびタイムアウト値と共に、正常性プローブのカスタム チェックを定義できます。 正常性プローブの詳細については、 [Load Balancer のプローブ](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。 次の例では、`myHealthProbe` という名前の TCP 正常性プローブを作成します。

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

出力:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

ここでは、正常性チェックの間隔を 15 秒に指定しました。 4 回のプローブ (1 分間) までは、ホストが機能していないとロード バランサーで判断されません。

## <a name="verify-the-load-balancer"></a>ロード バランサーの確認
ロード バランサーの構成を確認できました。 実行する手順を次に示します。

1. ロード バランサーを作成します。
2. フロントエンド IP プールを作成し、パブリック IP を割り当てます。
3. VM が接続できるバックエンド IP プールを作成します。
4. 管理用に VM への SSH 通信を許可する NAT 規則と Web アプリ用に TCP ポート 80 を許可する規則を作成します。
5. VM を定期的にチェックする正常性プローブを追加します。 この正常性プローブで、機能しないまたはコンテンツを提供しない VM にユーザーがアクセスを試みなくてもよくなります。

ロード バランサーがどのように見えるようになったか確認しましょう。

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

出力:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Linux VM で使用する NIC の作成
ルールを適用するため、NIC はプログラムで使用できます。 複数でも可能になります。 次の `azure network nic create` コマンドでは、NIC をロード バックエンド IP プールに接続し、SSH トラフィックを許可する NAT 規則を関連付けています。

`#####-###-###` セクションは、実際の Azure サブスクリプション ID に置き換えます。 サブスクリプション ID は、作成しているリソースを調べると、`jq` の出力に記載されています。 `azure account list` でサブスクリプション ID を表示することもできます。

次の例では、`myNic1` という名前の NIC を作成します。

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

出力:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

詳細を確認するには、直接リソースを調査します。 リソースを確認するには、 `azure network nic show` コマンドを使用します。

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

出力:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

次に、バックエンド IP プールにもう一度フックして、2 つ目の NIC を作成します。 ここでは、2 つ目の NAT 規則が SSH トラフィックを許可します。 次の例では、`myNic2` という名前の NIC を作成します。

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>ネットワーク セキュリティ グループと規則の作成
ネットワーク セキュリティ グループと、NIC へのアクセスを統制する受信規則を作成します。 ネットワーク セキュリティ グループは、NIC またはサブネットに適用できます。 VM の送受信トラフィックのフローを制御するための規則を定義します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

NSG の受信規則を追加して、(SSH をサポートするために) ポート 22 での受信接続を許可します。 次の例では、`myNetworkSecurityGroupRuleSSH` という名前の規則を作成して、ポート 22 で TCP を許可します。

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

次に、NSG の受信規則を追加して、(Web トラフィックをサポートするために) ポート 80 での受信接続を許可します。 次の例では、`myNetworkSecurityGroupRuleHTTP` という名前の規則を作成して、ポート 80 で TCP を許可します。

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> 受信規則は、受信ネットワーク接続のフィルターです。 この例では、NSG を VM の仮想 NIC にバインドします。これは、ポート 22 へのすべての要求が VM の NIC に渡されることを意味します。 この受信規則は、エンドポイントではなく、ネットワーク接続に関するルールであり、つまり従来の配置でのことです。 ポートを開くには、`--source-port-range` を "\*" (既定値) のままにして、**任意の**要求ポートから受信要求を受け入れる必要があります。 ポートは、通常動的です。
>
>

## <a name="bind-to-the-nic"></a>NIC へのバインド
NSG を NIC にバインドします。 NIC をネットワーク セキュリティ グループと接続する必要があります。 両方のコマンドを実行して、両方の NIC を接続します。

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>可用性セットの作成
可用性セットにより、障害ドメインおよびアップグレード ドメインに VM を分散できます。 VM の可用性セットを作成します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

障害ドメインは共通の電源とネットワーク スイッチを使用する仮想マシンのグループを定義します。 既定で、可用性セット内に構成された仮想マシンは、最大 3 つの障害ドメイン間で分散されます。 つまり、これらの障害ドメインのいずれかのハードウェアの問題が、アプリを実行している各 VM に影響しません。 VM を可用性セットに配置すると、Azure は自動的に VM を障害ドメイン間で分散します。

アップグレード ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。 計画済みメンテナンス中、アップグレード ドメインの再起動は順次ではない場合がありますが、一度に再起動されるアップグレードは 1 つのみです。 また、VM を可用性サイトに配置すると、Azure は自動的に VM をアップグレード ドメイン間で分散します。

詳細については、「 [VMの可用性管理](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="create-the-linux-vms"></a>Linux VM の作成
これまでの手順で、インターネットにアクセス可能な VM をサポートするためのストレージおよびネットワーク リソースを作成しました。 ここでは、その VM を作成し、パスワードのない SSH キーを使用してそれらをセキュリティで保護します。 この場合は、最新の LTS に基づいて Ubuntu VM を作成します。 [Azure VM イメージの検索に関するページ](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)で説明されているように、`azure vm image list` を使ってそのイメージの情報を見つけます。

コマンド `azure vm image list westeurope canonical | grep LTS` を使用してイメージを選択します。 この例では、`canonical:UbuntuServer:16.04.0-LTS:16.04.201608150` を使用します。 最後のフィールドでは、今後常に直近に構築されたように `latest` を渡します (使用する文字列は `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150` です)。

この次の手順は、Linux または Mac で **ssh-keygen -t rsa -b 2048**を使用して SSH RSA の公開キーと秘密キーのペアを既に作成した経験があるユーザーであればだれでもよく知っています。 `~/.ssh` ディレクトリに証明書キーのペアがない場合、作成することができます。

* 自動的に `azure vm create --generate-ssh-keys` オプションを使用します。
* 手動では [自分で作成する手順](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用します。

または、VM を作成した後で、`--admin-password` メソッドを使って SSH 接続を認証できます。 この方法は通常安全性が低いです。

`azure vm create` コマンドにすべてのリソースと情報を指定して、VM を作成します。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

出力:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

すぐに、既定の SSH キーを使用して VM に接続できるようになります。 ロード バランサー経由で渡されるため、適切なポートを指定することを確認します。 (最初の VM では、ポート 4222 を VM に転送する NAT 規則を設定します)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

出力:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

同じように、2 つ目の VM を作成します。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

ここで、`azure vm show myResourceGroup myVM1` コマンドを使用して、これまでに作成したものを確認できます。 この時点では、Azure のロード バランサーの背後で Ubuntu VM が実行中です。この VM には、所有する SSH キー ペアのみを使用してサインインできます (パスワードは無効になっているため)。 nginx または httpd をインストールし、Web アプリをデプロイして、ロード バランサーを経由して両方の VM に到達するトラフィック フローを確認できます。

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

出力:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>環境をテンプレートとしてエクスポートします。
これで環境を構築できました。同じパラメーターを使用して追加の開発環境を作成する場合、または、一致する運用環境を作成する場合はどのようにすべきでしょうか。 リソース マネージャーでは、環境に合ったすべてのパラメーターを定義する JSON テンプレートを使用します。 この JSON テンプレートを参照することで全体の環境を構築します。 [JSON テンプレートを手動で構築](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)できます。または、既存の環境をエクスポートして JSON テンプレートを作成することもできます。

```azurecli
azure group export --name myResourceGroup
```

このコマンドで、現在の作業ディレクトリ内に `myResourceGroup.json` ファイルが作成されます。 このテンプレートから新しい環境を作成すると、ロード バランサー、ネットワーク インターフェイス、または VM を含むリソース名が表示されます。 前に示した `azure group export` コマンドに `-p` または `--includeParameterDefaultValue` パラメーターを追加することでテンプレート ファイルにこれらの名前を入力できます。 リソース名を指定する JSON テンプレートを編集するか、リソース名を指定する [parameters.json ファイルを作成](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) します。

テンプレートから環境を作成するには:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

[テンプレートからデプロイする方法に関する詳細](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご確認ください。 段階的な環境の更新、パラメーター ファイルの使用、単一の保存場所からテンプレートにアクセスする方法を確認してください。

## <a name="next-steps"></a>次の手順
これで、複数のネットワーク コンポーネントと VM の操作を開始する準備が整いました。 ここで紹介した主要なコンポーネントを使用して、アプリケーションを構築するためにこのサンプル環境を使用できます。
