---
title: Azure CLI のサンプル - アプリをインストールする
description: このスクリプトでは、Ubuntu を実行する仮想マシン スケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な Web アプリケーションをインストールします。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 78569459de9d6a632f256d25ffb0870e36ce4a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87499689"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI を使用して仮想マシン スケール セットにアプリケーションをインストールする
このスクリプトでは、Ubuntu を実行する仮想マシン スケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な Web アプリケーションをインストールします。 スクリプトを実行すると、Web ブラウザーで Web アプリにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン スケール セット、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/ad/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vmss create](/cli/azure/vmss) | 仮想マシン スケール セットを作成し、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 複数の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 このコマンドでは、使用する VM イメージと管理者の資格情報も指定します。  |
| [az vmss extension set](/cli/azure/vmss/extension) | Azure カスタム スクリプト拡張機能をインストールして、各 VM インスタンス上にデータ ディスクを準備するスクリプトを実行します。 |
| [az network lb rule create](/cli/azure/network/lb/rule) | TCP ポート 80 のトラフィックをスケール セット内の VM インスタンスに分散するロード バランサー規則を作成します。 |
| [az network public-ip show](/cli/azure/network/public-ip) | ロード バランサーによって使用される、割り当てられたパブリック IP アドレスについての情報を取得します。 |
| [az group delete](/cli/azure/ad/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure/overview)のページをご覧ください。
