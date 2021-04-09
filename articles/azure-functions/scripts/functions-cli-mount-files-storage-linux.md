---
title: ファイル共有を Python Function App にマウントする - Azure CLI
description: Azure CLI を使用して、サーバーレス Python Function App を作成し、既存のファイル共有をマウントします。
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00bf0ecc9db3cf369fd75b427dcfba686aed0ed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035123"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Azure CLI を使用してファイル共有を Python Function App にマウントする

この Azure Functions サンプル スクリプトでは、Function App を作成し、Azure Files に共有を作成します。 次に、関数からデータにアクセスできるように、共有をマウントします。  

>[!NOTE]
>作成された Function App は、Python バージョン 3.7 上で実行されます。 Azure Functions では、[Python バージョン 3.6 および 3.8](../functions-reference-python.md#python-version) もサポートされます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトは、[従量課金プラン](../consumption-plan.md)を使用して Azure Functions に関数アプリを作成します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
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
