---
title: クイックスタート - Azure CLI を使用して仮想マシン スケール セットを作成する
description: Azure CLI を使用して仮想マシン スケール セットをすばやく作成する方法を説明します。実際に自分でデプロイしてみましょう。
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5ed6ff00e2ec9a47cec0290fa88ef3b554287607
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518160"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>クイック スタート:Azure CLI を使用して仮想マシン スケール セットを作成する
仮想マシン スケール セットを使用すると、自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 その後、Azure ロード バランサーがトラフィックをスケール セット内の VM インスタンスに分散します。 このクイック スタートでは、Azure CLI を使用して仮想マシン スケール セットを作成し、サンプル アプリケーションをデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.29 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 


## <a name="create-a-scale-set"></a>スケール セットを作成する
スケール セットを作成する前に、[az group create](/cli/azure/group) を使ってリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss) を使って仮想マシン スケール セットを作成します。 以下の例では、*myScaleSet* という名前のスケール セットを作成します。このスケール セットは、変更が適用されると自動的に更新するように設定され、SSH キーが *~/.ssh/id_rsa* に存在しない場合は生成します。 これらの SSH キーは、VM インスタンスにログインする必要がある場合に使用されます。 SSH キーの既存のセットを使用するには、代わりに `--ssh-key-value` パラメーターを使用して、キーの場所を指定します。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 VM インスタンスにアプリケーションをインストールするスクリプトをダウンロードして実行するために、Azure カスタム スクリプト拡張機能が使用されます。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-linux.md)」を参照してください。

カスタム スクリプト拡張機能を使用して、基本的な NGINX Web サーバーをインストールします。 次のように、[az vmss extension set](/cli/azure/vmss/extension) を使用して、NGINX をインストールするカスタム スクリプト拡張機能を適用します。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>アプリケーションへのトラフィックを許可する
スケール セットが作成されたとき、Azure ロード バランサーが自動的にデプロイされました。 ロード バランサーは、スケール セット内の VM インスタンスにトラフィックを分散します。 トラフィックがサンプル Web アプリケーションに到達できるようにするには、[az network lb rule create](/cli/azure/network/lb/rule) を使用してロード バランサー規則を作成します。 次の例では、*myLoadBalancerRuleWeb* という名前の規則を作成します。

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


## <a name="test-your-scale-set"></a>スケール セットのテスト
動作中のスケール セットを表示するには、Web ブラウザーでサンプル Web アプリケーションにアクセスします。 ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip) を使用します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![NGINX の既定の Web ページ](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする
必要がなくなったら、次のように [az group delete](/cli/azure/group) を使用して、リソース グループ、スケール セット、およびすべての関連リソースを削除できます。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>次のステップ
このクイック スタートでは、基本的なスケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な NGINX Web サーバーを VM インスタンスにインストールしました。 さらに学習するには、Azure 仮想マシン スケール セットを作成および管理する方法についてのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure 仮想マシン スケール セットの作成と管理](tutorial-create-and-manage-cli.md)
