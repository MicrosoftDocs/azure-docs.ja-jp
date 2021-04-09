---
title: マネージド リソース グループの取得と VM のサイズ変更 - Azure CLI
description: Azure マネージド アプリケーションのマネージド リソース グループを取得する Azure CLI サンプル スクリプトを紹介します。 このスクリプトでは、VM のサイズを変更します。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497819"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Azure CLI でマネージド リソース グループ内のリソースを取得し、VM のサイズを変更します

このスクリプトはマネージド リソース グループからリソースを取得し、そのリソース グループ内の VM のサイズを変更します。


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージド アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | マネージド アプリケーションの一覧を表示します。 クエリの値を指定して結果にフォーカスします。 |
| [az resource list](/cli/azure/resource#az-resource-list) | リソースの一覧を表示します。 リソース グループとクエリの値を指定して結果にフォーカスします。 |
| [az vm resize](/cli/azure/vm#az-vm-resize) | 仮想マシンのサイズを更新します。 |


## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
