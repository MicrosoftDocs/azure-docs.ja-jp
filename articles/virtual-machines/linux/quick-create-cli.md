---
title: クイック スタート:Azure CLI を使用して Linux VM を作成する
description: このクイック スタートでは、Azure CLI を使用して Linux 仮想マシンを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: fd411255247e6a37b857ac11c1b0abbd4558d02a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549772"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>クイック スタート:Azure CLI で Linux 仮想マシンを作成する

このクイックスタートでは、Azure コマンドライン インターフェイス (CLI) を使用して、Linux 仮想マシン (VM) を Azure にデプロイする方法を示します。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。

このチュートリアルでは、Ubuntu 16.04 LTS をインストールします。 動作中の VM を表示するには、SSH を使用してその VM に接続し、NGINX Web サーバーをインストールする必要があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm) コマンドで VM を作成します。

次の例では、*myVM* という名前の VM を作成し、*azureuser* という名前のユーザー アカウントを追加します。 SSH キーを自動的に生成するために `--generate-ssh-keys` パラメーターが使用され、キーは既定のキーの場所 ( *~/.ssh*) に配置されます。 代わりに特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、成功した VM 作成操作を示します。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

作成した VM からの出力に含まれる実際の `publicIpAddress` を記録しておいてください。 このアドレスは、次の手順で VM にアクセスするために使います。

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く

既定では、Azure において Linux VM を作成すると SSH 接続のみが開かれます。 NGINX Web サーバーで使うために TCP ポート 80 を開くには、[az vm open-port](/cli/azure/vm) を使います。

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

通常どおりに、SSH を使って VM に接続します。 **publicIpAddress** は、VM からの前の出力で記録しておいた VM のパブリック IP アドレスに置き換えます。

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、NGINX Web サーバーをインストールします。 パッケージ ソースを更新し、最新の NGINX パッケージをインストールします。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完了したら、`exit` と入力して SSH セッションを終了します。

## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

任意の Web ブラウザーを使用して、NGINX の既定のウェルカム ページを表示します。 Web アドレスとして、自分の VM のパブリック IP アドレスを使用します。 次の例は、既定の NGINX Web サイトを示しています。

![NGINX のようこそページの表示](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な仮想マシンをデプロイし、Web トラフィック用のネットワーク ポートを開き、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。


> [!div class="nextstepaction"]
> [Azure Linux 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
