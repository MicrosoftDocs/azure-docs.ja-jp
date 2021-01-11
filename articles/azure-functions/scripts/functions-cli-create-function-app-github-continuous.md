---
title: GitHub デプロイを使用して Function App を作成する - Azure CLI
description: Azure Functions を使用して、Function App を作成し、GitHub リポジトリから関数コードをデプロイします。
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f7c272fa95d2697d41cb751e39ef72b0da86a12
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498523"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>GitHub からデプロイされる Function App を Azure で作成する

この Azure Functions のサンプル スクリプトでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して、Function App と関連リソースが作成されます。 また、GitHub レポジトリから継続的にデプロイされるように関数コードを構成します。 

このサンプルでは、以下が必要です。

* 管理アクセス許可があり、関数コードを含む GitHub レポジトリ。
* GitHub アカウントの[個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、バージョン 2.0 以降をインストールして使用する必要があります。 Azure CLI のバージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、GitHub から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Function App に必要なストレージ アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で Function App を作成し、Git リポジトリまたは Mercurial リポジトリにそれを関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
