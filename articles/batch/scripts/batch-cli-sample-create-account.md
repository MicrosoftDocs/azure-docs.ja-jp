---
title: Azure CLI スクリプトの例 - Batch アカウントの作成 - Batch サービス
description: このスクリプトは、Batch サービス モードで Azure Batch アカウントを作成し、アカウントのさまざまなプロパティを照会および更新する方法を示します。
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2349b6b373f271a5aa0f169e5a9ebc9f58f6f608
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076812"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI の例: Batch サービス モードでの Batch アカウントの作成

このスクリプトは、Batch サービス モードで Azure Batch アカウントを作成し、アカウントのさまざまなプロパティを照会および更新する方法を示します。 既定の Batch サービス モードで Batch アカウントを作成するときに、Batch サービスによって内部的にコンピューティング ノードが割り当てられます。 割り当てられるコンピューティング ノードは個々の vCPU (コア) クォータの対象になります。また、アカウントは、共有キー資格情報または Azure Active Directory トークンによって認証することができます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch アカウントを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Batch アカウントのプロパティを更新します。  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | 指定された Batch アカウントの詳細を取得します。  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | 指定された Batch アカウントのアクセス キーを取得します。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
