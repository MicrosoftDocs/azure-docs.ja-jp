---
title: "Function App の作成と GitHub からの関数コードのデプロイ | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Function App の作成と GitHub からの関数コードのデプロイ"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d67e85f91c80efe464fceb1105243bedfba83a0f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Function App の作成と GitHub からの関数コードのデプロイ

このサンプル スクリプトでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して関連リソースと共に Function App を作成し、GitHub レポジトリから関数コードをデプロイします。(継続的なデプロイは行いません)。 GitHub から関数コードを継続的に配信する場合は、[Function App の作成と関数と GitHub から継続的なデプロイ](functions-cli-create-function-app-github-continuous.md)に関するページをご覧ください

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、GitHub から関数コードをデプロイします。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "GitHub からのデプロイで Function App を作成する")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure Function App を作成します。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Function App を Git または Mercurial レポジトリと関連付けます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。

