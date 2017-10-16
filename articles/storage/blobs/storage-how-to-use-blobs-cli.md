---
title: "Azure CLI を使用する Azure Blob Storage (オブジェクトの記憶域) での操作の実行 | Microsoft Docs"
description: "Azure Blob Storage で BLOB のアップロードとダウンロードを行う方法と、ストレージ アカウントで BLOB へのアクセスを管理する Secure Access Signature (SAS) を構築する方法について説明します。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Azure CLI を使用して Blob Storage の操作を実行する

Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化オブジェクト データを格納するためのサービスです。 このチュートリアルでは、BLOB のアップロード、ダウンロード、および削除など、Azure Blob Storage での基本的な操作について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * コンテナーを作成する
> * コンテナーにファイル (BLOB) をアップロードする
> * コンテナー内の BLOB を一覧表示する
> * コンテナーから BLOB をダウンロードする
> * ストレージ アカウント間で BLOB をコピーする
> * BLOB を削除する
> * BLOB のプロパティとメタデータを表示および変更する
> * Shared Access Signature (SAS) を使用してセキュリティを管理する

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーはコンピューター上のディレクトリに似ており、ディレクトリ内のファイルを整理するように、コンテナー内の BLOB のグループを整理することができます。 ストレージ アカウントは、任意の数のコンテナーを持つことができます。 コンテナーには最大 500 TB (ストレージ アカウントの最大データ サイズ) の BLOB データを格納できます。

BLOB を格納するコンテナーは、[az storage container create](/cli/azure/storage/container#create) コマンドで作成します。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

コンテナー名は英文字または数字で始まる必要があり、英文字、数字、およびハイフン文字 (-) のみを含めることができます。 BLOB とコンテナーの名前付けの詳細については、「[Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」(コンテナー、BLOB、メタデータの名前付けと参照) を参照してください。

## <a name="enable-public-read-access-for-a-container"></a>コンテナーのパブリック読み取りアクセスを有効にする

新しく作成されたコンテナーは、既定ではプライベートです。 つまり、[Secure Access Signature](#create-a-shared-access-signature-sas) またはストレージ アカウントのアクセス キーがなければ、誰もコンテナーにアクセスできません。 この動作を変更するには、Azure CLI を使用して、コンテナーのアクセス許可を次に示す 3 つのレベルのいずれかに設定します。

| | |
|---|---|
| `--public-access off` | (既定) パブリック読み取りアクセスなし |
| `--public-access blob` | BLOB 専用のパブリック読み取りアクセス |
| `--public-access container` | BLOB に対するパブリック読み取りアクセスで、コンテナー内の BLOB を一覧表示できます。 |

`blob` または `container` に対するパブリック アクセスを設定するときは、インターネット上の全員に読み取り専用アクセスを有効にします。 たとえば、Web サイトに BLOB として格納されている画像を表示する場合、パブリック読み取りアクセスを有効にする必要があります。 読み取り/書き込みアクセスを有効にする場合は、代わりに [Shared Access Signature (SAS)](#create-a-shared-access-signature-sas) を使用する必要があります。

[az storage container set-permission](/cli/azure/storage/container#create) コマンドを使用して、コンテナーのパブリック読み取りアクセスを有効にします。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>コンテナーに BLOB をアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 ブロック BLOB は、Azure Storage に格納される最も一般的な種類の BLOB です。 追加 BLOB は、ログ記録などのため、既存のコンテンツを変更することなく既存の BLOB にデータを追加する必要がある場合に使います。 ページ BLOB は、IaaS 仮想マシンの VHD ファイルをバックアップします。

この例では、最後のステップで [az storage blob upload](/cli/azure/storage/blob#upload) コマンドを使って作成したコンテナーに BLOB をアップロードします。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

この操作では、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 いくつかのファイルをアップロードして、次の手順で BLOB を一覧表示すると、複数のエントリが表示されます。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、[az storage blob list](/cli/azure/storage/blob#list) コマンドを使います。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

サンプル出力:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>BLOB をダウンロードする

前の手順でアップロードした BLOB を [az storage blob download](/cli/azure/storage/blob#download) コマンドを使用してダウンロードします。

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>ストレージ アカウント間で BLOB をコピーする

BLOB は、ストレージ アカウント内、またはストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。

次の例は、BLOB をあるストレージ アカウントから別のストレージ アカウントにコピーする方法を示しています。 まず、ソース ストレージ アカウントにコンテナーを作成し、その BLOB にパブリックな読み取りアクセス権を指定します。 次にコンテナーにファイルをアップロードし、最後にそのコンテナーから目的のストレージ アカウントのコンテナーに BLOB をコピーします。

この例のコピー操作が成功するためには、目的のストレージ アカウントにコピー先となるコンテナーがあらかじめ存在していることが必要です。 加えて、`--source-uri` 引数に指定したコピー元の BLOB が、Shared Access Signature (SAS) トークンを含んでいるか、この例のようにパブリックにアクセス可能であることが必要です。

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>BLOB を削除する

[az storage blob delete](/cli/azure/storage/blob#delete) コマンドを使用して、コンテナーから BLOB を削除します。

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>BLOB のプロパティとメタデータを表示および変更する

各 BLOB にはサービスで定義されたプロパティがいくつかあり、[az storage blob show](/cli/azure/storage/blob#show) コマンドを使用して表示できます。たとえば、名前、種類、長さなどのプロパティがあります。 [az storage blob metadata update](/cli/azure/storage/blob/metadata#update) コマンドでプロパティとその値を指定して、BLOB を構成することもできます。

この例では、まずサービスで定義された BLOB のプロパティを表示し、次に独自のメタデータ プロパティのうち 2 つを使用して BLOB を更新します。 最後に、[az storage blob metadata show](/cli/azure/storage/blob/metadata#show) コマンドを使用して、BLOB のメタデータ プロパティとその値を表示します。

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Shared Access Signature (SAS) を作成する

Shared Access Signature (SAS) を使用すると、ストレージ アカウントのアクセス キーを知らせずに、自分のストレージ アカウントのオブジェクトへの制限付きアクセスを許可することができます。 プライベート リソースへのアクセスを許可する URI を作成するには、目的のアクセス許可と有効期間を指定した SAS トークンを作成し、リソースの URL にクエリ文字列として付加して、完全な SAS URI を構成します。 この SAS URI (リソースの URL と SAS トークン) を使用するユーザーは誰でも、SAS トークンの有効期間中にアクセスできます。 たとえば、他のユーザーが表示できるように、2 分間のプライベート BLOB への読み取りアクセスを許可することができます。

以下の手順では、コンテナーに対するパブリック アクセスを無効にし、プライベートのみのアクセスをテストしてから、SAS URI の生成とテストを行います。

### <a name="disable-container-public-access"></a>コンテナーのパブリック アクセスを無効にする

まずコンテナーのアクセス レベルを `off` に設定します。 その結果、Shared Access Signature またはストレージ アカウントのアクセス キーなしではコンテナーまたはその BLOB にアクセスできなくなります。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>プライベート アクセスを検証する

そのコンテナー内の BLOB に対してパブリック読み取りアクセスがないことを検証するには、[az storage blob url](/cli/azure/storage/blob#url) コマンドを使用して、BLOB のいずれかに対する URL を取得します。

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

プライベート ブラウザー ウィンドウで BLOB の URL に移動します。 BLOB はプライベートであり、Shared Access Signature を含めていないため、`ResourceNotFound` エラーが表示されます。

### <a name="create-a-sas-uri"></a>SAS URI を作成する

ここでは、BLOB へのアクセスを許可する SAS URI を作成します。 次の例では、まず [az storage blob url](/cli/azure/storage/blob#url) コマンドを使用して、変数に BLOB の URL を設定します。次に、[az storage blob generate-sas](/cli/azure/storage/blob#generate-sas) コマンドを使用して生成された SAS トークンを別の変数に設定します。 最後に、`?` クエリ文字列の区切り文字で区切られた 2 つの要素を連結して、BLOB の完全な SAS URI を出力します。

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>SAS URI をテストする

プライベート ブラウザー ウィンドウで、前のコード スニペットで `echo` コマンドを使用して表示した完全な SAS URI に移動します。 今回はエラーが表示されないので、BLOB の表示またはダウンロードを行うことができます。

URL の期限が切れるまで (この例では 2 分間) 十分待ってから、別のプライベート ブラウザー ウィンドウの URL に移動します。 SAS トークンが期限切れのため、`AuthenticationFailed` エラーが表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したストレージ アカウントや、アップロードした BLOB など、リソース グループのリソースが不要になった場合は、[az group delete](/cli/azure/group#delete) コマンドでリソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Storage で BLOB を操作する方法の基本について説明しました。

> [!div class="checklist"]
> * コンテナーを作成する
> * コンテナーにファイル (BLOB) をアップロードする
> * コンテナー内の BLOB を一覧表示する
> * コンテナーから BLOB をダウンロードする
> * ストレージ アカウント間で BLOB をコピーする
> * BLOB を削除する
> * BLOB のプロパティとメタデータを表示および変更する
> * Shared Access Signature (SAS) を使用してセキュリティを管理する

次のリソースは、Azure CLI を使用する場合のその他の情報と、ストレージ アカウントでリソースを使用する場合について説明しています。

* Azure CLI
  * [Azure CLI 2.0 を使用してログインする](/cli/azure/authenticate-azure-cli) - Azure CLI スクリプトの無人実行の場合、[サービス プリンシパル](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal)を介した非対話型のログインなど、CLI を使用したさまざまな認証方法について説明します。
  * [Azure CLI 2.0 コマンド リファレンス](/cli/azure/)
* Microsoft Azure ストレージ エクスプローラー
  * [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
