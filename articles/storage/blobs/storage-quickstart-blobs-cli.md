---
title: Azure クイック スタート - Azure CLI を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、オブジェクト (BLOB) ストレージで Azure CLI を使用します。 その後、CLI を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 11bd639d86c6ad9a9f373ac26dc271817bc46b08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773088"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して BLOB をアップロード、ダウンロード、および一覧表示する

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 ブラウザーで、Azure Cloud Shell を使用して操作することができます。 また、macOS、Linux、または Windows 上にインストールし、コマンド ラインから実行することもできます。 このクイック スタートでは、Azure CLI を使用して、Azure Blob Storage との間でデータをアップロードおよびダウンロードする方法を説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 `az --version` を実行して、バージョンを判断します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンピューター上のファイルをフォルダーで整理するように、BLOB のグループを整理できます。

BLOB を格納するコンテナーは、[az storage container create](/cli/azure/storage/container) コマンドで作成します。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>BLOB をアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 BLOB ストレージに格納されるほとんどのファイルは、ブロック BLOB として格納されます。 追加 BLOB は、ログ記録などのため、既存のコンテンツを変更することなく既存の BLOB にデータを追加する必要がある場合に使います。 ページ BLOB は、IaaS 仮想マシンの VHD ファイルをバックアップします。

まず、BLOB にアップロードするファイルを作成します。
Azure Cloud Shell を使用している場合は、次の手順を使用してファイルを作成します。`vi helloworld` を実行し、ファイルが開いたら、**Insert** キーを押して、"Hello world" と入力し、**Esc** キーを押して、`:x` を入力し **Enter** キーを押します。

この例では、最後のステップで [az storage blob upload](/cli/azure/storage/blob) コマンドを使って作成したコンテナーに BLOB をアップロードします。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

前に説明した方法を使用して、Azure Cloud Shell にファイルを作成した場合は、この CLI コマンドを代わりに使用できます (このファイルはベース ディレクトリで作成されているため、パスを指定する必要はないことに注意してください。通常、パスを指定する必要はありません)。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld \
    --file helloworld
```

この操作では、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 続行する前に、希望する数のファイルをアップロードします。

同時に複数のファイルをアップロードするには、[az storage blob upload-batch](/cli/azure/storage/blob) コマンドを使用できます。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、[az storage blob list](/cli/azure/storage/blob) コマンドを使います。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>BLOB をダウンロードする

前にアップロードした BLOB をダウンロードするには、[az storage blob download](/cli/azure/storage/blob) コマンドを使います。

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>AzCopy でのデータ転送

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ユーティリティは、Azure Storage 用のスクリプト可能な高性能データ転送のためのもう 1 つのオプションです。 AzCopy を使って、Blob Storage、File Storage、および Table Storage との間で双方向にデータを転送できます。

次に示す AzCopy コマンドの簡単な例では、*myfile.txt* ファイルを *mystoragecontainer* コンテナーにアップロードしています。

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成したストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、[az group delete](/cli/azure/group) コマンドでリソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ローカル ディスクと Azure Blob Storage のコンテナーとの間でファイルを転送する方法について学習しました。 Azure Storage の BLOB の他の処理について詳しくは、Azure Blob Storage の使用に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [方法:Azure CLI での Blob Storage の操作](storage-how-to-use-blobs-cli.md)
