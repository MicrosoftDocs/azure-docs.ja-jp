---
title: Azure Cosmos DB に接続する Azure 関数の作成 | Microsoft Docs
description: Azure CLI スクリプトのサンプル - Azure Cosmos DB に接続する Azure 関数の作成
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 15a7cc1940a01486c6b660ec65b47f072dc7996e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970682"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Azure Cosmos DB に接続する Azure 関数の作成

この Azure Functions サンプル スクリプトは、関数アプリを作成し、関数を Azure Cosmos DB データベースに接続します。 作成された、接続を含むアプリ設定は、[Azure Cosmos DB のトリガーまたはバインド](../functions-bindings-cosmosdb.md)と共に使用することができます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで使う場合は、Azure CLI バージョン 2.0 以降を実行していることを確認してください。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、Cosmos DB エンドポイントとアクセス キーをアプリ設定に追加します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 任意の場所にリソース グループを作成します |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントの作成 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で関数アプリを作成します。 |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB のデータベースを作成します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。




