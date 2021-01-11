---
title: DevOps デプロイを使用して Function App を作成する - Azure CLI
description: Function App を作成して Azure DevOps から関数コードをデプロイします
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 773a08646f7a69e1ed828621bad48a6c6729eb88
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498528"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Azure DevOps からデプロイされる関数を Azure で作成する

このトピックでは、Azure Functions を使用して、[従量課金プラン](../functions-scale.md#consumption-plan)を使用する[サーバーレス](https://azure.microsoft.com/solutions/serverless/)Function App を作成する方法を説明します。 Function App は、ユーザーの関数用のコンテナーであり、Azure DevOps リポジトリから継続的にデプロイされます。 

このトピックを完了するには、以下が必要です。

* ユーザーの Function App プロジェクトが含まれ、ユーザーが管理アクセス許可を持っている Azure DevOps リポジトリ。
* Azure DevOps リポジトリにアクセスするための[個人用アクセス トークン (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、バージョン 2.0 以降をインストールして使用する必要があります。 Azure CLI のバージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、Azure DevOps から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、ストレージ アカウント、Function App、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Function App に必要なストレージ アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で Function App を作成します。 |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
