---
title: Azure CLI スクリプトのサンプル - SignalR Service の作成
description: Azure CLI スクリプトのサンプル - SignalR Service の作成
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 93674574bceb24b75b9af36708ddfe7e77ebf0fe
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565834"
---
# <a name="create-a-signalr-service"></a>SignalR Service の作成 

このサンプル スクリプトでは、ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、Azure CLI の *signalr* 拡張機能を使います。 このサンプル スクリプトを使う前に、次のコマンドを実行して Azure CLI の *signalr* 拡張機能をインストールしてください。

```azurecli-interactive
az extension add -n signalr
```

このスクリプトでは、新しい SignalR Service リソースと新しいリソース グループを作成します。 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Azure SignalR Service リソースを作成します。 |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | キーを一覧表示します。これらのキーは、SignalR でリアルタイム コンテンツの更新をプッシュする際、アプリケーションによって使われます。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure SignalR Service のその他の CLI サンプル スクリプトについては、[Azure SignalR サービスのドキュメント](../signalr-reference-cli.md)をご覧ください。
