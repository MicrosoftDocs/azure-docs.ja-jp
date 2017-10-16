---
title: "Azure クイックスタート - Azure CLI を使用して Azure Blob Storage との間でオブジェクトを転送する | Microsoft Docs"
description: "Azure CLI を使って Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: marsma
ms.openlocfilehash: c9b7e7a1fbc6b67821183ce31bdf2527de490c92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Azure CLI を使用して Azure Blob Storage との間でオブジェクトを転送する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイックスタートでは、Azure CLI を使って、Azure Blob Storage にデータをアップロードしたり、Azure Blob Storage からデータをダウンロードしたりする方法を詳しく説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンテナーを使うと、コンピューターでディレクトリにファイルを整理するように、BLOB のグループを整理できます。

BLOB を格納するためのコンテナーを、[az storage container create](/cli/azure/storage/container#create) コマンドで作成します。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>BLOB をアップロードする

Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。 BLOB ストレージに格納されるほとんどのファイルは、ブロック BLOB として格納されます。 追加 BLOB は、ログ記録などのため、既存のコンテンツを変更することなく既存の BLOB にデータを追加する必要がある場合に使います。 ページ BLOB は、IaaS 仮想マシンの VHD ファイルをバックアップします。

この例では、最後のステップで [az storage blob upload](/cli/azure/storage/blob#upload) コマンドを使って作成したコンテナーに BLOB をアップロードします。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

この操作では、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 続行する前に、希望する数のファイルをアップロードします。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、[az storage blob list](/cli/azure/storage/blob#list) コマンドを使います。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>BLOB をダウンロードする

前にアップロードした BLOB をダウンロードするには、[az storage blob download](/cli/azure/storage/blob#download) コマンドを使います。

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

このクイック スタートで作成したストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、[az group delete](/cli/azure/group#delete) コマンドでリソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ローカル ディスクと Azure Blob Storage のコンテナーとの間でファイルを転送する方法について学習しました。 Azure Storage の BLOB の他の処理について詳しくは、Azure Blob Storage の使用に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [方法: Azure CLI での Blob Storage の操作](storage-how-to-use-blobs-cli.md)
