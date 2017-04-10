---
title: "Azure CLI を使用した Linux 仮想マシンの管理 | Microsoft Docs"
description: "チュートリアル - Azure CLI を使用した Linux 仮想マシンの管理"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Azure CLI を使用した Linux 仮想マシンの管理

このチュートリアルでは、仮想マシンを作成し、ディスクの追加、ソフトウェアのインストールの自動化、仮想マシン スナップショットの作成などの日常的な管理タスクを実行します。 

このチュートリアルに取り組む前に、最新の [Azure CLI 2.0](/cli/azure/install-azure-cli) がインストールされていることを確認してください。

## <a name="step-1--log-in-to-azure"></a>手順 1 - Azure へのログイン

まずターミナルを開き、[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインします。

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>手順 2 - リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group#create) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、`myResourceGroup` という名前のリソース グループが `westeurope` リージョンに作成されます。 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>手順 3 - 構成の準備

仮想マシンのデプロイでは、パッケージのインストール、ファイルの作成、スクリプトの実行などの構成作業を **cloud-init** で自動化することができます。 このチュートリアルでは、次に示す 2 つの項目の構成を自動化します。

- NGINX Web サーバーのインストール
- VM に対する 2 台目のディスクのプロビジョニング

**cloud-init** の構成は VM のデプロイ時に実行されます。そのため **cloud-init** の構成は、仮想マシンの作成前に定義しておく必要があります。

`cloud-init.txt` という名前のファイルを作成し、次の内容をコピーします。 この構成は、NGINX パッケージをインストールし、2 台目のディスクをフォーマットしてマウントするためのコマンドを実行します。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>手順 4 - 仮想マシンの作成

仮想マシンを作成するには、[az vm create](https://docs.microsoft.com/cli/azure/vm#create) コマンドを使用します。 

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 この例では、Ubuntu を実行する `myVM` という名前の仮想マシンを作成します。 `--data-disk-sizes-gb` 引数で 50 GB のディスクを作成して VM に接続します。 `--custom-data` 引数に cloud-init 構成を指定すると、その構成が VM に反映されます。 最後に、SSH キーが存在しなければ、それも作成されます。

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

VM が作成されると、Azure CLI で以下の情報が出力されます。 パブリック IP アドレスを書き留めておいてください。この仮想マシンにアクセスするときに、このアドレスが必要になります。 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

VM がデプロイされている間、**cloud-init** の構成が完了するまでに数分かかる場合があります。 

## <a name="step-5--configure-firewall"></a>手順 5 - ファイアウォールの構成

Azure [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) (NSG) は、仮想マシンの受信トラフィックと送信トラフィックを制御します。 特定のポートまたは特定のポート範囲に対するネットワーク トラフィックが、ネットワーク セキュリティ グループの規則によって許可または拒否されます。 また、これらの規則に送信元アドレス プレフィックスを含めれば、あらかじめ決められた送信元からのトラフィックにのみ仮想マシンとの通信を許可することができます。

前のセクションで、NGINX Web サーバーをインストールしました。 ポート 80 での受信トラフィックを許可するネットワーク セキュリティ グループの規則がなければ、この Web サーバーにインターネットからアクセスすることはできません。 次の手順で NSG の規則を作成し、ポート 80 で受信接続を許可してみましょう。

### <a name="create-nsg-rule"></a>NSG 規則の作成

受信 NSG 規則を作成するには、[az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) コマンドを使用します。 次の例では、仮想マシンのポート `80` を開放しています。

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

それではブラウザーで仮想マシンのパブリック IP アドレスにアクセスしてみましょう。 NSG 規則が適用され、既定の NGINX Web サイトが表示されます。

![NGINX の既定のサイト](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>手順 6 - 仮想マシンのスナップショット

ディスクのスナップショットを作成すると、特定の時点のディスクに対する読み取り専用のコピーが作成されます。 この手順では、VM のオペレーティング システム ディスクに対するスナップショットを作成します。 OS ディスクのスナップショットがあれば、仮想マシンをすばやく特定の状態に復元できます。また、スナップショットを使って同じ状態の仮想マシンを新たに作成することも可能です。

### <a name="create-snapshot"></a>スナップショットの作成

スナップショットを作成する前に、ディスクの ID または名前が必要です。 ディスク ID を取得するには、[az vm show](https://docs.microsoft.com/cli/azure/vm#show) コマンドを使用します。 この例ではディスク ID を変数に格納し、後の手順で使用します。

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

ディスクの ID を取得したら、次のコマンドでスナップショットを作成します。

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>スナップショットからのディスクの作成

このスナップショットをディスクに変換すれば、それを使って仮想マシンを作成し直すことができます。

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>スナップショットからの仮想マシンの復元

実際に仮想マシンを復元してみましょう。既存の仮想マシンは削除します。 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

仮想マシンを作成し直すと、既存のネットワーク インターフェイスが再利用されます。 これによってネットワークのセキュリティ構成が確実に維持されます。

ネットワーク インターフェイス名を取得するには、[az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list) コマンドを使用します。 この例では、この名前を `nic` という変数に格納しています。次の手順でこの変数を使用します。

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

スナップショット ディスクから新しい仮想マシンを作成します。

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

新しいパブリック IP アドレスをメモしておき、そのアドレスにインターネット ブラウザーでアクセスします。 復元された仮想マシンで NGINX が実行されていることがわかります。 

### <a name="reconfigure-data-disk"></a>データ ディスクの再構成

では、データ ディスクを仮想マシンに再度接続してみましょう。 

まず、[az disk list](https://docs.microsoft.com/cli/azure/disk#list) コマンドでデータ ディスクの名前を見つけます。 この例では、このディスク名を `datadisk` という変数に格納しています。次の手順でこの変数を使用します。

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

ディスクを接続するには、[az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) コマンドを使用します。

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

さらにこのディスクをオペレーティング システムにマウントする必要があります。 ディスクをマウントするには、仮想マシンに接続して、たとえば `sudo mount /dev/sdc1 /datadrive` を実行します。マウント操作は、好きな方法で行ってください。 

## <a name="step-7--management-tasks"></a>手順 7 - 管理タスク

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、スクリプトを作成して自動化したい場合もあるでしょう。 日常的な管理タスクの多くは、Azure CLI を使ってコマンド ラインやスクリプトから実行できます。 

### <a name="get-ip-address"></a>IP アドレスの取得

仮想マシンのプライベート IP アドレスとパブリック IP アドレスを取得するには、次のコマンドを使用します。  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>仮想マシンのサイズを変更する

Azure 仮想マシンのサイズを変更するには、選択した Azure リージョンで利用できるサイズの名前を把握する必要があります。 これらのサイズは、[az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes) コマンドで確認できます。

```azurecli
az vm list-sizes --location westeurope --output table
```

仮想マシンのサイズを変更するには、[az vm resize](https://docs.microsoft.com/cli/azure/vm#resize) コマンドを使用します。 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>仮想マシンの停止

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>仮想マシンの起動

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Delete resource group

リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

チュートリアル - [負荷分散された仮想マシンの作成](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

サンプル - [Azure CLI のサンプル スクリプト](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
