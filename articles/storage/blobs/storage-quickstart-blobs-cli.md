---
title: クイックスタート - Azure CLI を使用して BLOB を作成する
titleSuffix: Azure Storage
description: このクイックスタートでは、Azure CLI を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行う方法を説明します。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892925"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>クイック スタート:Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 ブラウザーで、Azure Cloud Shell を使用して操作することができます。 また、macOS、Linux、または Windows 上にインストールし、コマンド ラインから実行することもできます。 このクイック スタートでは、Azure CLI を使用して、Azure Blob Storage との間でデータをアップロードおよびダウンロードする方法を説明します。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 `az --version` を実行して、バージョンを判断します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンピューター上のファイルをフォルダーで整理するように、BLOB のグループを整理できます。

BLOB を格納するコンテナーは、[az storage container create](/cli/azure/storage/container) コマンドで作成します。

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>BLOB をアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 このクイックスタートの例では、ブロック BLOB を使用する方法を示します。

まず、ブロック BLOB にアップロードするファイルを作成します。 Azure Cloud Shell を使用している場合は、次のコマンドを使用してファイルを作成します。

```bash
vi helloworld
```

ファイルが開いたら、 **[挿入]** を押します。 「*Hello world*」と入力し、**Esc** を押します。次に、「 *:x*」と入力し、**Enter** を押します。

この例では、最後のステップで [az storage blob upload](/cli/azure/storage/blob) コマンドを使って作成したコンテナーに BLOB をアップロードします。 ファイルがルート ディレクトリに作成された後でファイル パスを指定する必要はありません。

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

この操作では、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 続行する前に、希望する数のファイルをアップロードします。

同時に複数のファイルをアップロードするには、[az storage blob upload-batch](/cli/azure/storage/blob) コマンドを使用できます。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、[az storage blob list](/cli/azure/storage/blob) コマンドを使います。

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>BLOB をダウンロードする

前にアップロードした BLOB をダウンロードするには、[az storage blob download](/cli/azure/storage/blob) コマンドを使います。

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>AzCopy でのデータ転送

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ユーティリティは、Azure Storage 用のスクリプト可能な高性能データ転送のためのもう 1 つのオプションです。 AzCopy を使用すると、BLOB、ファイル、およびテーブル ストレージとの間でデータを転送できます。

次の例では、AzCopy を使用して、*myfile.txt* という名前のファイルを *sample-container* コンテナーにアップロードします。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートで作成したストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、[az group delete](/cli/azure/group) コマンドでリソース グループを削除します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ローカル ファイル システムと Azure Blob Storage 内のコンテナーとの間でファイルを転送する方法について学習しました。 Azure Storage の BLOB の他の処理について詳しくは、Azure Blob Storage の使用に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [方法: Azure CLI での Blob Storage の操作](storage-how-to-use-blobs-cli.md)
