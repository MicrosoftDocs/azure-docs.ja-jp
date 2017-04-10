---
title: "Azure CLI 2.0 を使用した Linux 環境の作成 | Microsoft Docs"
description: "Azure CLI 2.0 を使用して、ストレージ、Linux VM、仮想ネットワークとサブネット、ロード バランサー、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4cff286de1abd492ce7276c300b50d71f06345b
ms.openlocfilehash: f07a326aa2fcd659f69265001293c9ed332bb842
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用して完全な Linux 環境を作成する
この記事では、開発と単純なコンピューティングに役立つ VM のペアを含む単純なネットワークとロード バランサーを構築します。 ここでは、インターネット上のどこからでも接続できる、2 台のセキュリティで保護された実用的な Linux VM を構築するまで、各コマンドの説明を交えながらプロセスについて説明します。 この記事を理解すると、より複雑なネットワークや環境に進むことができます。 この記事では、Azure CLI 2.0 を使用して前述の環境を構築する方法について説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

その過程で、Resource Manager デプロイメント モデルによって提供される依存関係階層とその性能についても説明します。 システムがどのように構築されているかをいったん理解すると、 [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して、より短時間でシステムを再構築することができます。 環境の各部分がどのように組み合わさっているかがわかると、それらを自動化するためのテンプレートの作成はより簡単になります。

環境には以下を含みます。

* 可用性セット内の&2; つの VM
* ポート 80 の負荷分散規則が構成されたロード バランサー
* 不要なトラフィックからVM を保護するネットワーク セキュリティ グループ ルール (NSG)

![基本的な環境の概要](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM を Azure にアップロードするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-walkthrough)。

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。

このカスタム環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

最初に、[az group create](/cli/azure/group#create) でリソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

この手順は省略可能です。 Azure CLI 2.0 で VM を作成するときの既定の操作では、Azure Managed Disks を使用します。 Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。 非管理対象ディスクを使用する場合は、[az storage account create](/cli/azure/storage/account#create) を使用して、ストレージ アカウントを作成する必要があります。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。 (ストレージ アカウント名は一意である必要があるため、独自の一意の名前を入力してください。)

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

[az network vnet create](/cli/azure/network/vnet#create) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成します。

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

[az network public-ip create](/cli/azure/network/public-ip#create) でパブリック IP を作成します。 次の例では、DNS 名が `mypublicdns` で `myPublicIP` という名前のパブリック IP を作成します。 (DNS 名は一意である必要があるため、独自の一意の名前を入力してください。)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

[az network lb create](/cli/azure/network/lb#create) でロード バランサーを作成します。 次の例をご覧ください。

- `myLoadBalancer` という名前のロード バランサーを作成する
- パブリック IP `myPublicIP` を関連付ける
- `mySubnetPool` という名前のフロントエンド IP プールを作成する
- `myBackEndPool` という名前のバックエンド IP プールを作成する

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

[az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create) で、ロード バランサーの SSH 受信ネットワーク アドレス変換 (NAT) 規則を作成します。 次の例では、2 つのロード バランサー規則 `myLoadBalancerRuleSSH1` と `myLoadBalancerRuleSSH2` を作成します。

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

[az network lb probe create](/cli/azure/network/lb/probe#create) で、ロード バランサーの正常性プローブを作成します。 次の例では、`myHealthProbe` という名前の TCP プローブを作成します。

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

[az network lb rule create](/cli/azure/network/lb/rule#create) で、ロード バランサーの Web 受信 NAT 規則を作成します。 次の例では、`myLoadBalancerRuleWeb` という名前のロード バランサー規則を作成し、それを `myHealthProbe` プローブに関連付けます。

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

[az network lb show](/cli/azure/network/lb#show) で、ロード バランサー、IP プール、NAT 規則を確認します。

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

[az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create) で、ネットワーク セキュリティ グループの&2; つの受信規則を追加します。 次の例では、2 つの規則 `myNetworkSecurityGroupRuleSSH` と `myNetworkSecurityGroupRuleHTTP` を作成します。

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

[az network nic create](/cli/azure/network/nic#create) で、最初のネットワーク インターフェイス カード (NIC) を作成します。 次の例では、`myNic1` という名前の NIC を作成し、それをロード バランサー `myLoadBalancer` と適切なプールに接続し、`myNetworkSecurityGroup` にも接続します。

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

**az network nic create** で、2 つ目の NIC を作成します。 次の例では、`myNic2` という名前の NIC を作成します。

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

[az vm availability-set create](/cli/azure/vm/availability-set#create) で、可用性セットを作成します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

[az vm create](/cli/azure/vm#create) で、最初の Linux VM を作成します。 次の例では、Azure Managed Disks を使用して、`myVM1` という名前の VM を作成します。 非管理対象ディスクを使用する場合は、下記の追加の注意事項を参照してください。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Azure Managed Disks を使用する場合は、この手順をスキップします。 非管理対象ディスクを使用して、前の手順でストレージ アカウントを作成した場合は、この後のコマンドにいくつかのパラメーターを追加する必要があります。 この後のコマンドに次のパラメーターを追加して、`mystorageaccount` という名前のストレージ アカウントに非管理対象ディスクを作成します。 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

**az vm create** で、2 つ目の Linux VM を作成します。 次の例では、`myVM2` という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

ここでも、既定の Azure Managed Disks を使用しない場合は、この後のコマンドに次のパラメーターを追加して、`mystorageaccount` という名前のストレージ アカウントに非管理対象ディスクを作成します。

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

[az vm show](/cli/azure/vm#show) で、すべて正しく構築されたことを確認します。

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

新しいインスタンスを簡単に再作成するため、[az group export](/cli/azure/group#export) でテンプレートに新しい環境をエクスポートします。

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
次の手順では、環境を構築する際に各コマンドがどのように機能するかについて説明します。 これらの概念は、開発または実稼働用のカスタム環境を作成するのに役立ちます。

[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/#login) で Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。

## <a name="create-resource-groups-and-choose-deployment-locations"></a>リソース グループの作成とデプロイ先の選択
Azure リソース グループは、リソース デプロイの論理的な管理を可能にする構成情報とメタデータを含む論理的なデプロイ エンティティです。 [az group create](/cli/azure/group#create) で、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

既定では、出力は、JSON (JavaScript Object Notation) にあります。 たとえば、一覧またはテーブルとして出力するには [az configure --output](/cli/azure/#configure) を使います。 出力形式で&1; 回のみ変更するために `--output` を任意のコマンドに追加することもできます。 次の例では、**az group create** コマンドからの JSON の出力を示します。

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
この手順は省略可能です。 Azure CLI 2.0 で VM を作成するときの既定の操作では、Azure Managed Disks を使用します。 これらのディスクは Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。 Azure Managed Disks を使用する場合は、「[仮想ネットワークとサブネットの作成](#create-a-virtual-network-and-subnet)」に進んでください。 

非管理対象ディスクを使用する場合は、VM ディスクおよび追加するすべてのデータ ディスク用に、ストレージ アカウントを作成する必要があります。

ここでは、[az storage account create](/cli/azure/storage/account#create) コマンドを使い、アカウントの場所、アカウントを制御するリソース グループ、必要なストレージ サポートの種類を渡します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

出力:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

CLI を使ってストレージ アカウントを調査するには、まず、アカウント名とキーを設定する必要があります。 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) を使います。 次の例にあるストレージ アカウントの名前を選択したものに置き換えます。

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

次に、[az storage container list](/cli/azure/storage/container#list) でストレージ情報を表示できます。

```azurecli
az storage container list
```

出力:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成
次に、VM を作成できるように、Azure で実行中の仮想ネットワークとサブネットを作成する必要があります。 次の例では、[az network vnet create](/cli/azure/network/vnet#create) を使って、`192.168.0.0/16` のアドレス プレフィックスを持つ `myVnet` という名前の仮想ネットワークと、`192.168.1.0/24` のサブネット アドレス プレフィックスを持つ `mySubnet` という名前のサブネットを作成します。

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

出力には、仮想ネットワーク内で論理的に作成されるサブネットが表示されます。

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
パブリック IP アドレス (PIP) を作成して、ロード バランサーに割り当てます。 [az network public-ip create](/cli/azure/network/public-ip#create) コマンドを使って、インターネットから VM に接続できます。 既定のアドレスは動的であるため、`--domain-name-label` オプションを使って、**cloudapp.azure.com** ドメインに名前付き DNS エントリを作成します。 次の例では、DNS 名が `mypublicdns` で `myPublicIP` という名前のパブリック IP を作成します。 DNS 名は一意である必要があるため、独自の一意の DNS 名を指定します。

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

出力:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

パブリック IP アドレス リソースは論理的に割り当てられていますが、特定のアドレスはまだ割り当てられていません。 IP アドレスを取得するには、ロード バランサーが必要ですが、まだ作成されていません。

## <a name="create-a-load-balancer-and-ip-pools"></a>ロード バランサーと IP プールの作成
ロード バランサーを作成するときに複数の VM にトラフィックを分散できます。 メンテナンスや大きな負荷が発生した場合に、ユーザー要求に応答する複数の VM を実行することで、アプリケーションに冗長性も加わります。 次の例では、[az network lb create](/cli/azure/network/lb#create) を使って、`myLoadBalancer` という名前のロード バランサー、`myFrontEndPool` という名前のフロントエンド IP プールを作成し、`myPublicIP` リソースを接続します。

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

出力:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

以前に作成した `myPublicIP` を渡すために、`--public-ip-address` スイッチを使っている方法に注意してください。 パブリック IP アドレスをロード バランサーに割り当てることで、インターネット経由で VM に到達できるようになります。

接続する VM の保存先として、バックエンド プールを使用します。 これにより、トラフィックは、VM にロード バランサーを経由できます。 [az network lb address-pool create](/cli/azure/network/lb/address-pool#create) で、バックエンド トラフィック用の IP プールを作成しましょう。 次の例では、`myBackEndPool` という名前のバックエンド プールを作成します。

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

出力の抜粋:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>ロード バランサーの NAT 規則の作成
トラフィックがロード バランサーを経由するようにするには、受信アクションまたは送信アクションを指定するネットワーク アドレス変換 (NAT) 規則を作成する必要があります。 使用するプロトコルを指定して、必要に応じて、外部ポートを内部ポートにマップします。 この環境では、SSH がロード バランサーを経由して VM に到達できるようにするいくつかの規則を [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create) で作成しましょう。 VM の TCP ポート 22 (これは後で作成します) に送信する TCP ポート 4222 と 4223 を設定します。 次の例では、`myLoadBalancerRuleSSH1` という名前の規則を作成して、TCP ポート 4222 をポート 22 にマップします。

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

出力:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

SSH に対する&2; つ目の NAT 規則について、手順を繰り返します。 次の例では、`myLoadBalancerRuleSSH2` という名前の規則を作成して、TCP ポート 4223 をポート 22 にマップします。

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブの作成
正常性プローブは、ロード バランサーの背後にある VM を定期的にチェックして、それらが定義されているとおりに動作し、要求に応答していることを確認します。 動作していない場合は、運用から削除され、ユーザーがそれらに転送されないようにします。 間隔およびタイムアウト値と共に、正常性プローブのカスタム チェックを定義できます。 正常性プローブの詳細については、 [Load Balancer のプローブ](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。 次の例では、[az network lb probe create](/cli/azure/network/lb/probe#create) を使って `myHealthProbe` という名前の TCP 正常性プローブを作成します。

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

出力:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

ここでは、正常性チェックの間隔を 15 秒に指定しました。 4 回のプローブ (1 分間) までは、ホストが機能していないとロード バランサーで判断されません。

さらに、規則を IP プールに接続する Web トラフィック用の TCP ポート 80 の NAT 規則の作成します。 規則を個々の VM に接続するのではなく、IP プールに接続すると、IP プールの VM を追加または削除できます。 ロード バランサーは、トラフィックのフローを自動的に調整します。 次の例では、[az network lb rule create](/cli/azure/network/lb/rule#create) を使って、TCP ポート 80 をポート 80 にマップする `myLoadBalancerRuleWeb` という名前の規則を作成し、`myHealthProbe` という名前の正常性プローブを接続します。

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

出力:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>ロード バランサーの確認
ロード バランサーの構成を確認できました。 実行する手順を次に示します。

1. ロード バランサーを作成します。
2. フロントエンド IP プールを作成し、パブリック IP を割り当てます。
3. VM が接続できるバックエンド IP プールを作成します。
4. 管理用に VM への SSH 通信を許可する NAT 規則と Web アプリ用に TCP ポート 80 を許可する規則を作成します。
5. VM を定期的にチェックする正常性プローブを追加します。 この正常性プローブで、機能しないまたはコンテンツを提供しない VM にユーザーがアクセスを試みなくてもよくなります。

[az network lb show](/cli/azure/network/lb#show) で、ロード バランサーがどのように見えるようになったか確認しましょう。

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

出力:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>ネットワーク セキュリティ グループと規則の作成
ネットワーク セキュリティ グループと、NIC へのアクセスを統制する受信規則を作成します。 ネットワーク セキュリティ グループは、NIC またはサブネットに適用できます。 VM の送受信トラフィックのフローを制御するための規則を定義します。 次の例では、[az network nsg create](/cli/azure/network/nsg#create) を使って、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create) で NSG の受信規則を追加して、(SSH をサポートするために) ポート 22 での受信接続を許可します。 次の例では、`myNetworkSecurityGroupRuleSSH` という名前の規則を作成して、ポート 22 で TCP を許可します。

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

次に、NSG の受信規則を追加して、(Web トラフィックをサポートするために) ポート 80 での受信接続を許可します。 次の例では、`myNetworkSecurityGroupRuleHTTP` という名前の規則を作成して、ポート 80 で TCP を許可します。

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> 受信規則は、受信ネットワーク接続のフィルターです。 この例では、NSG を VM の仮想 NIC にバインドします。これは、ポート 22 へのすべての要求が VM の NIC に渡されることを意味します。 この受信規則は、エンドポイントではなく、ネットワーク接続に関するルールであり、つまり従来の配置でのことです。 ポートを開くには、`--source-port-range` を "\*" (既定値) のままにして、**任意の**要求ポートから受信要求を受け入れる必要があります。 ポートは、通常動的です。

[az network nsg show](/cli/azure/network/nsg#show) で、ネットワーク セキュリティ グループと規則を調べます。

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

出力:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Linux VM で使用する NIC の作成
ルールを適用するため、NIC はプログラムで使用できます。 複数でも可能になります。 次の [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) コマンドでは、NIC をロード バックエンド IP プールに接続し、SSH トラフィックとネットワーク セキュリティ グループを許可する NAT 規則を関連付けています。

次の例では、`myNic1` という名前の NIC を作成します。

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

出力:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

次に、バックエンド IP プールにもう一度フックして、2 つ目の NIC を作成します。 ここでは、2 つ目の NAT 規則が SSH トラフィックを許可します。 次の例では、`myNic2` という名前の NIC を作成します。

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>可用性セットの作成
可用性セットにより、障害ドメインおよびアップグレード ドメインに VM を分散できます。 [az vm availability-set create](/cli/azure/vm/availability-set#create) で、VM の可用性セットを作成しましょう。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

障害ドメインは共通の電源とネットワーク スイッチを使用する仮想マシンのグループを定義します。 既定で、可用性セット内に構成された仮想マシンは、最大&3; つの障害ドメイン間で分散されます。 つまり、これらの障害ドメインのいずれかのハードウェアの問題が、アプリを実行している各 VM に影響しません。 VM を可用性セットに配置すると、Azure は自動的に VM を障害ドメイン間で分散します。

アップグレード ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。 計画済みメンテナンス中、アップグレード ドメインの再起動は順次ではない場合がありますが、一度に再起動されるアップグレードは&1; つのみです。 また、VM を可用性サイトに配置すると、Azure は自動的に VM をアップグレード ドメイン間で分散します。

詳細については、「 [VMの可用性管理](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。


## <a name="create-the-linux-vms"></a>Linux VM の作成
これで、インターネットにアクセス可能な VM をサポートするためのネットワーク リソースが作成されました。 ここでは、その VM を作成し、パスワードのない SSH キーを使用してそれらをセキュリティで保護します。 この場合は、最新の LTS に基づいて Ubuntu VM を作成します。 [Azure VM イメージの検索](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事で説明されているように、[az vm image list](/cli/azure/vm/image#list) を使ってそのイメージの情報を見つけます。

認証に使う SSH キーも指定します。 SSH キーがない場合は、[こちらの手順](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に従って作成できます。 または、VM を作成した後で、`--admin-password` メソッドを使って SSH 接続を認証できます。 この方法は通常安全性が低いです。

[az vm create](/cli/azure/vm#create) コマンドですべてのリソースと情報を指定して、VM を作成します。 次の例では、Azure Managed Disks を使用して、`myVM1` という名前の VM を作成します。 非管理対象ディスクを使用する場合は、下記の追加の注意事項を参照してください。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Azure Managed Disks を使用する場合は、この手順をスキップします。 非管理対象ディスクを使用して、前の手順でストレージ アカウントを作成した場合は、この後のコマンドにいくつかのパラメーターを追加する必要があります。 この後のコマンドに次のパラメーターを追加して、`mystorageaccount` という名前のストレージ アカウントに非管理対象ディスクを作成します。 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

出力:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

すぐに、既定の SSH キーを使用して VM に接続できるようになります。 ロード バランサー経由で渡されるため、適切なポートを指定することを確認します。 (最初の VM では、ポート 4222 を VM に転送する NAT 規則を設定します)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
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
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

同じように、2 つ目の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

ここでも、既定の Azure Managed Disks を使用しない場合は、この後のコマンドに次のパラメーターを追加して、`mystorageaccount` という名前のストレージ アカウントに非管理対象ディスクを作成します。

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

この時点では、Azure のロード バランサーの背後で Ubuntu VM が実行中です。この VM には、所有する SSH キー ペアのみを使用してサインインできます (パスワードは無効になっているため)。 nginx または httpd をインストールし、Web アプリをデプロイして、ロード バランサーを経由して両方の VM に到達するトラフィック フローを確認できます。


## <a name="export-the-environment-as-a-template"></a>環境をテンプレートとしてエクスポートします。
これで環境を構築できました。同じパラメーターを使用して追加の開発環境を作成する場合、または、一致する運用環境を作成する場合はどのようにすべきでしょうか。 リソース マネージャーでは、環境に合ったすべてのパラメーターを定義する JSON テンプレートを使用します。 この JSON テンプレートを参照することで全体の環境を構築します。 [JSON テンプレートを手動で構築](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)できます。または、既存の環境をエクスポートして JSON テンプレートを作成することもできます。 [az group export](/cli/azure/group#export) を使って、リソース グループを次のようにエクスポートします。

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

このコマンドで、現在の作業ディレクトリ内に `myResourceGroup.json` ファイルが作成されます。 このテンプレートから新しい環境を作成すると、ロード バランサー、ネットワーク インターフェイス、または VM を含むリソース名が表示されます。 前に示した **az group export** コマンドに `--include-parameter-default-value` パラメーターを追加するとテンプレート ファイルにこれらの名前を入力できます。 リソース名を指定する JSON テンプレートを編集するか、リソース名を指定する [parameters.json ファイルを作成](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) します。

テンプレートから環境を作成するには、次のように [az group deployment create](/cli/azure/group/deployment#create) を使います。

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

[テンプレートからデプロイする方法に関する詳細](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご確認ください。 段階的な環境の更新、パラメーター ファイルの使用、単一の保存場所からテンプレートにアクセスする方法を確認してください。

## <a name="next-steps"></a>次のステップ
これで、複数のネットワーク コンポーネントと VM の操作を開始する準備が整いました。 ここで紹介した主要なコンポーネントを使用して、アプリケーションを構築するためにこのサンプル環境を使用できます。

