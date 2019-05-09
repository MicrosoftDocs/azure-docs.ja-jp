---
title: Azure DevOps からデプロイされる関数を Azure で作成する | Microsoft Docs
description: 関数アプリを作成して Azure DevOps から関数コードをデプロイします
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: azure-functions
ms.custom: mvc
ms.openlocfilehash: 7fe68090773902248dbcdd63fbbdbbdb06b307cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852391"
---
# <a name="create-a-function-app-and-deploy-function-code-from-azure-devops"></a>関数アプリを作成して Azure DevOps から関数コードをデプロイする

このトピックでは、Azure Functions を使用して、[消費プラン](../functions-scale.md#consumption-plan)を使用する[サーバーレス](https://azure.microsoft.com/solutions/serverless/)関数アプリを作成する方法を説明します。 関数アプリは、ユーザーの関数用のコンテナーであり、Azure DevOps リポジトリから継続的にデプロイされます。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

このトピックを完了するには、以下が必要です。

* ユーザーの関数アプリ プロジェクトが含まれ、ユーザーが管理アクセス許可を持っている Azure DevOps リポジトリ。
* Azure DevOps リポジトリにアクセスするための[個人用アクセス トークン (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルで使用する場合は、バージョン 2.0 以降をインストールして使用する必要があります。 Azure CLI のバージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure 関数アプリを作成し、Azure DevOps から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、ストレージ アカウント、関数アプリ、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | 関数アプリに必要なストレージ アカウントを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で関数アプリを作成します。 |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
