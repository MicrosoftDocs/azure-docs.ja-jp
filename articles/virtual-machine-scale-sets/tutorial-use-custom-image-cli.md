---
title: チュートリアル - Azure CLI を使用したスケール セットのカスタム VM イメージの使用 | Microsoft Docs
description: Azure CLI を使用して仮想マシン スケール セットをデプロイするためのカスタム VM イメージを作成する方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aafec48f86ee032b112e9bb1100f82fbb3b363ed
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118396"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用した仮想マシン スケール セットのカスタム イメージの作成および使用
スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 VM インスタンスをデプロイした後のタスクの数を減らすには、カスタム VM イメージを使用できます。 このカスタム VM イメージには、すべての必要なアプリケーション インストールまたは構成が含まれます。 スケール セットで作成されたすべての VM インスタンスは、カスタム VM イメージを使用し、アプリケーション トラフィックを処理できる状態になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM の作成とカスタマイズ
> * VM のプロビジョニング解除と汎用化
> * カスタム VM イメージの作成
> * カスタム VM イメージを使用するスケール セットのデプロイ

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.29 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。


## <a name="create-and-configure-a-source-vm"></a>ソース VM の作成と構成

>[!NOTE]
> このチュートリアルでは、汎用化された VM イメージを作成および使用する手順について説明します。 特殊化された VM イメージからスケール セットを作成することはできません。

最初に [az group create](/cli/azure/group) を使用してリソース グループを作成し、次に [az vm create](/cli/azure/vm) を使用して VM を作成します。 この VM は、カスタム VM イメージのソースとして使用されます。 次の例では、*myResourceGroup* という名前のリソース グループに *myVM* という名前の VM を作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM のパブリック IP アドレスは、[az vm create](/cli/azure/vm) コマンドの出力に示されます。 次に示すように、VM のパブリック IP アドレスに SSH 接続します。

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

VM をカスタマイズするために、基本的な Web サーバーをインストールしてみましょう。 スケール セット内の VM インスタンスがデプロイされると、Web アプリケーションの実行に必要なパッケージがすべて揃うことになります。 次のように `apt-get` を使用して *NGINX* をインストールします。

```bash
sudo apt-get install -y nginx
```

カスタム イメージとして使用する VM を準備するための最後の手順は、VM をプロビジョニング解除することです。 この手順では、マシンに固有の情報を VM から削除して、1 つのイメージから多数の VM をデプロイできるようにします。 VM をプロビジョニング解除すると、ホスト名は *localhost.localdomain* にリセットされます。 SSH ホスト キー、ネームサーバー構成、ルート パスワード、およびキャッシュされた DHCP リースも削除されます。

VM をプロビジョニング解除するには、Azure VM エージェント (*waagent*) を使用します。 Azure VM エージェントはすべての VM にインストールされ、Azure プラットフォームとの通信に使用されます。 `-force` パラメーターは、マシン固有の情報をリセットするためのプロンプトを受け入れるようにエージェントに指示します。

```bash
sudo waagent -deprovision+user -force
```

VM への SSH 接続を閉じます。

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>ソース VM からのカスタム VM イメージの作成
これで Nginx Web サーバーがインストールされ、ソース VM がカスタマイズされました。 次に、スケール セットで使用するカスタム VM イメージを作成しましょう。

イメージを作成するには、VM の割り当てを解除する必要があります。 [az vm deallocate](/cli//azure/vm) で VM の割り当てを解除します。 次に、[az vm generalize](/cli//azure/vm) を使用して VM の状態を汎用化済みとして設定します。これにより、Azure プラットフォームは、カスタム イメージを使用するための VM の準備が整ったことを認識します。 イメージを作成できるのは、汎用化された VM からのみです。


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

VM を割り当て解除して汎用化するには数分かかることがあります。

次に、[az image create](/cli//azure/image) を使用して VM のイメージを作成します。 次の例では、VM から *myImage* という名前のイメージを作成します。

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>カスタム VM イメージからのスケール セットの作成
[az vmss create](/cli/azure/vmss#az-vmss-create) を使用して、スケール セットを作成します。 *UbuntuLTS* や *CentOS* などのプラットフォーム イメージの代わりに、カスタム VM イメージの名前を指定します。 次の例では、前の手順の *myImage* という名前のカスタム イメージを使用する、*myScaleSet* という名前のスケール セットを作成します。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="test-your-scale-set"></a>スケール セットのテスト
トラフィックがスケール セットに到達できるようにし、Web サーバーが正常に動作することを確認するには、[az network lb rule create](/cli/azure/network/lb/rule) を使用してロード バランサー規則を作成します。 次の例では、*TCP* ポート *80* のトラフィックを許可する *myLoadBalancerRuleWeb* という名前の規則を作成します。

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

スケール セットが動作していることを確認するには、[az network public-ip show](/cli/azure/network/public-ip) を使用してロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 次の例に示すように、既定の NGINX Web ページが表示されます。

![カスタム VM イメージから実行されている Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure CLI を使用してスケール セットにカスタム VM イメージを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * VM の作成とカスタマイズ
> * VM のプロビジョニング解除と汎用化
> * カスタム VM イメージの作成
> * カスタム VM イメージを使用するスケール セットのデプロイ

次のチュートリアルに進み、アプリケーションをスケール セットにデプロイする方法を学習してください。

> [!div class="nextstepaction"]
> [アプリケーションをスケール セットにデプロイする](tutorial-install-apps-cli.md)
