---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181789"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドで Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) コマンドで汎用のストレージ アカウントを作成します。 汎用のストレージ アカウントは、BLOB、ファイル、テーブル、およびキューという 4 つのサービスすべてに使用できます。 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>ストレージ アカウントの資格情報を指定する

Azure CLI では、このチュートリアルのほとんどのコマンドにストレージ アカウントの資格情報が必要です。 機能の実行時にいくつかのオプションもありますが、最も簡単な指定方法は、`AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_KEY` 環境変数を設定することです。

まず、[az storage account keys list](/cli/azure/storage/account/keys) コマンドを使用してストレージ アカウント キーを表示します。

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

次に `AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_KEY` 環境変数を設定します。 この設定には、Bash シェルで `export` コマンドを使用します。

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
