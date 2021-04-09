---
title: 多層アプリケーション用の仮想ネットワークの作成 - Azure CLI スクリプト サンプル
description: 多層アプリケーション用の仮想ネットワークの作成 - Azure CLI スクリプト サンプル。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54ad2327af40041c7bde07095e7f5d8ed1375015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313928"
---
# <a name="create-a-virtual-network-for-multi-tier-applications-using-an-azure-cli-script-sample"></a>Azure CLI スクリプト サンプルを使用して多層アプリケーション用の仮想ネットワークを作成する

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 このスクリプトを実行すると、2 つの仮想マシンが、Web サーバーと MySQL ソフトウェアをデプロイできる各サブネットに 1 つずつ作成されます。

Azure [Cloud Shell](https://shell.azure.com/bash) から、またはローカルの Azure CLI インストールからスクリプトを実行することができます。 ローカルで CLI を使用する場合、このスクリプトでは、バージョン 2.0.28 以降を実行する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成する必要もあります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>サンプル スクリプト


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [az network subnet create](/cli/azure/network/vnet/subnet) | バックエンド サブネットを作成します。 |
| [az network public-ip create](/cli/azure/network/public-ip) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [az network nic create](/cli/azure/network/nic) | 仮想ネットワーク インターフェイスを作成し、それらを仮想ネットワークのフロントエンドおよびバックエンド サブネットに接続します。 |
| [az network nsg create](/cli/azure/network/nsg) | フロントエンドおよびバックエンド サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) を作成します。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |特定のサブネットに対して特定のポートを許可またはブロックする NSG ルールを作成します。 |
| [az vm create](/cli/azure/vm) | 仮想マシンを作成し、NIC を各 VM にアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [az group delete](/cli/azure/group) | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

他の仮想ネットワーク CLI スクリプトのサンプルについては、[仮想ネットワーク CLI のサンプル](../cli-samples.md)に関するページを参照してください。
