---
title: ファイル共有を Python Function App にマウントする - Azure CLI
description: Azure CLI を使用して、サーバーレス Python Function App を作成し、既存のファイル共有をマウントします。
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498210"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLI を使用してファイル共有を Python Function App にマウントする

この Azure Functions サンプル スクリプトでは、Function App を作成し、Azure Files に共有を作成します。 次に、関数からデータにアクセスできるように、共有をマウントします。  

>[!NOTE]
>作成された Function App は、Python バージョン 3.7 上で実行されます。 Azure Functions では、[Python バージョン 3.6 および 3.8](../functions-reference-python.md#python-version) もサポートされます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 例は Bash シェル用に記述されているため、Windows コマンド プロンプトで実行できるように変更する必要があります。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、[従量課金プラン](../functions-scale.md#consumption-plan)を使用して Azure Function App が作成されます。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Azure Storage アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Function App を作成します。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | ストレージ アカウントに Azure Files 共有を作成します。 | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | 共有内にディレクトリを作成します。 |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | 共有を Function App にマウントします。 |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Function App にマウントされたファイル共有を表示します。 | 

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
