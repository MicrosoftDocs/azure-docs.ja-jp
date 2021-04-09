---
title: 仮想ネットワークの作成 - クイック スタート - Azure CLI
titlesuffix: Azure Virtual Network
description: このクイック スタートでは、Azure CLI を使用して仮想ネットワークを作成する方法について説明します。 仮想ネットワークでは、Azure リソースが互いに通信を行ったり、インターネットと通信したりできるようになります。
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3f4cd0a09c64c8c89116bf3a7dec40bae9f05f71
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199069"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用した仮想ネットワークの作成

仮想ネットワークによって、仮想マシン (VM) などの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。 

このクイック スタートでは、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成したら、2 つの VM を仮想ネットワークにデプロイします。 次にインターネットから VM に接続し、新しい仮想ネットワークを介してプライベートで通信します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する

仮想ネットワークを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 この例では、**Eastus** の場所内に **CreateVNetQS-rg** という名前のリソース グループを作成します。

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 この例では、**default** という名前のサブネットを使って、**myVNet** という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワーク内に 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 

既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 

`--no-wait` オプションを使用すると、バックグラウンドで VM が作成されます。 次の手順に進むことができます。 

この例では、**myVM1** という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

前の手順では、`--no-wait` オプションを使用しました。 今度は 2 つ目の VM を **myVM2** という名前で作成しましょう。

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI の出力メッセージ

VM の作成には数分かかります。 Azure で VM が作成された後、Azure CLI では次のような出力が返されます。

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>VM のパブリック IP

**myVM2** のパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

このコマンドでは、`<publicIpAddress>` を **myVM2** VM のパブリック IP アドレスに置き換えます。

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 間の通信

**myVM2** VM と **myVM1** VM の間のプライベートな通信を確認するには、次のコマンドを入力します。

```bash
ping myVM1 -c 4
```

*10.0.0.4* から 4 つの応答を受信します。

**myVM2** VM との SSH セッションを終了します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのすべてのリソースを削除できます。

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次のようにします。 

* 既定の仮想ネットワークと 2 つの VM を作成しました。 
* インターネットから 1 つの VM に接続し、2 つの VM の間でプライベート通信を行いました。

仮想ネットワークでは、VM 間のプライベート通信に制限はありません。 

各種 VM ネットワーク通信の構成の詳細については、次の記事に進んでください。
> [!div class="nextstepaction"]
> [ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)
