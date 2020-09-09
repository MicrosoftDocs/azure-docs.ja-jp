---
title: ドメイン名のための DNS ゾーンとレコードを作成する - Azure CLI - Azure DNS
description: この Azure CLI スクリプトの例では、ドメイン名のための DNS ゾーンとレコードを作成する方法を示しています
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1b0f08132ace5de7108702b61e89517fd28b4673
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494912"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI スクリプトの例: DNS ゾーンとレコードの作成

この Azure CLI スクリプトの例では、ドメイン名のための DNS ゾーンとレコードを作成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、DNS ゾーン、およびすべての関連リソースを削除します。

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Azure DNS ゾーンを作成します。 |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | DNS ゾーンに *A* レコードを追加します。 |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | DNS ゾーン内のすべての *A* レコード セットを一覧表示します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

