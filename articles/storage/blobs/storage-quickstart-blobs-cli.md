---
title: クイックスタート - Azure CLI を使用して BLOB を作成する
titleSuffix: Azure Storage
description: このクイックスタートでは、Azure CLI を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行う方法を説明します。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04f793f78cef938c31e7a30aad5569a54eb461a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613109"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>クイック スタート:Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 ブラウザーで、Azure Cloud Shell を使用して操作することができます。 また、macOS、Linux、または Windows 上にインストールし、コマンド ラインから実行することもできます。 このクイック スタートでは、Azure CLI を使用して、Azure Blob Storage との間でデータをアップロードおよびダウンロードする方法を説明します。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- この記事では、Azure CLI のバージョン 2.0.46 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="authorize-access-to-blob-storage"></a>Blob Storage へのアクセスを承認する

Blob Storage へのアクセスは、Azure CLI から Azure AD の資格情報を使用するか、またはストレージ アカウントのアクセス キーを使用して承認することができます。 Azure AD の資格情報を使用することをお勧めします。 この記事では、Azure AD を使用して、Blob Storage の操作を承認する方法について説明します。

Blob Storage を対象とするデータの操作では、Azure CLI コマンドで `--auth-mode` パラメーターがサポートされます。特定の操作を承認する方法をパラメーターで指定できます。 Azure AD の資格情報を使用して承認するには、`--auth-mode` パラメーターを `login` に設定します。 詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

`--auth-mode` パラメーターがサポートされるのは、Blob Storage のデータの操作だけです。 リソース グループの作成やストレージ アカウントの作成など、管理操作の承認では自動的に Azure AD の資格情報が使用されます。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドで Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) コマンドで汎用のストレージ アカウントを作成します。 汎用のストレージ アカウントは、BLOB、ファイル、テーブル、およびキューという 4 つのサービスすべてに使用できます。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンピューター上のファイルをフォルダーで整理するように、コンテナー内の BLOB のグループを整理できます。 BLOB を格納するコンテナーは、[az storage container create](/cli/azure/storage/container) コマンドで作成します。

次の例では、Azure AD アカウントを使用して、コンテナーの作成操作を承認します。 コンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 Azure ロールの割り当ての詳細については、[Azure CLI を使用したアクセス用の Azure ロールの割り当て](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json)に関するページを参照してください。  

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Azure ロールの割り当ての反映には数分かかることがあります。

ストレージ アカウント キーを使用して、コンテナーの作成操作を承認することもできます。 Azure CLI を使用したデータ操作の承認について詳しくは、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

## <a name="upload-a-blob"></a>BLOB をアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 このクイックスタートの例では、ブロック BLOB を使用する方法を示します。

まず、ブロック BLOB にアップロードするファイルを作成します。 Azure Cloud Shell を使用している場合は、次のコマンドを使用してファイルを作成します。

```bash
vi helloworld
```

ファイルが開いたら、 **[挿入]** を押します。 「*Hello world*」と入力し、**Esc** を押します。次に、「 *:x*」と入力し、**Enter** を押します。

この例では、最後のステップで [az storage blob upload](/cli/azure/storage/blob) コマンドを使って作成したコンテナーに BLOB をアップロードします。 ファイルはルート ディレクトリに作成されているので、ファイル パスを指定する必要はありません。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

この操作では、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 続行する前に、希望する数のファイルをアップロードします。

同時に複数のファイルをアップロードするには、[az storage blob upload-batch](/cli/azure/storage/blob) コマンドを使用できます。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、[az storage blob list](/cli/azure/storage/blob) コマンドを使います。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>BLOB をダウンロードする

前にアップロードした BLOB をダウンロードするには、[az storage blob download](/cli/azure/storage/blob) コマンドを使います。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>AzCopy でのデータ転送

AzCopy コマンド ライン ユーティリティは、Azure Storage に対する高速かつスクリプトで制御可能なデータ転送を実現します。 Blob Storage や Azure Files との間のデータ転送に AzCopy を使用できます。 最新バージョンである AzCopy v10 の詳細については、「[AzCopy を使ってみる](../common/storage-use-azcopy-v10.md)」を参照してください。 Blob Storage での AzCopy v10 の使用については、「[AzCopy と Blob Storage でデータを転送する](../common/storage-use-azcopy-v10.md#transfer-data)」を参照してください。

次の例では、AzCopy を使用してローカル ファイルを BLOB にアップロードします。 サンプルの値は忘れずに実際の値に置き換えてください。

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ストレージ アカウントなど、このクイックスタートの一部として作成したリソースを削除する場合は、[az group delete](/cli/azure/group) コマンドを使用してリソース グループを削除します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ローカル ファイル システムと Azure Blob Storage 内のコンテナーとの間でファイルを転送する方法について学習しました。 Azure CLI を使用した BLOB ストレージの操作をさらに学習するには、BLOB ストレージ用の Azure CLI サンプルを調べてください。

> [!div class="nextstepaction"]
> [Blob Storage の Azure CLI サンプル](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
