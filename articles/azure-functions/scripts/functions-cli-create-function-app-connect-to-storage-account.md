---
title: 接続ストレージを使用して関数アプリを作成する - Azure CLI
description: Azure CLI スクリプトのサンプル - Azure Storage に接続する Azure 関数の作成
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3eec4e22f80d1efd180dc6770a781ee14db680be
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498550"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>名前付きストレージ アカウント接続を使用して関数アプリを作成する 

この Azure Functions サンプル スクリプトは、関数アプリを作成し、関数を Azure Storage アカウントに接続します。 作成された、接続を含むアプリ設定は、[ストレージのトリガーまたはバインド](../functions-bindings-storage-blob.md)と共に使用することができます。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで使う場合は、Azure CLI バージョン 2.0 以降を実行していることを確認してください。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、ストレージ接続文字列をアプリ設定に追加します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 任意の場所にリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | サーバーレスの[従量課金プラン](../functions-scale.md#consumption-plan)で関数アプリを作成します。 |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | アカウント用の接続文字列を取得します。 |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 関数アプリのアプリ設定として接続文字列を設定します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
