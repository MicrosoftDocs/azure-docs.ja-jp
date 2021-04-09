---
title: Azure CLI を使用してデータを管理する (Azure Data Lake Storage Gen2)
description: Azure CLI を使用して、階層型名前空間を持つストレージ アカウントでディレクトリとファイルを管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650356"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure CLI を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する

この記事では、[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/) を使用して、階層型名前空間を持つストレージ アカウントでディレクトリとファイルを作成および管理する方法について説明します。

ディレクトリとファイルのアクセス制御リスト (ACL) を取得、設定、更新する方法については、「[Azure Data Lake Storage Gen2 で Azure CLI を使用して ACL を管理する](data-lake-storage-acl-cli.md)」を参照してください。

[サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [フィードバックを送る](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>正しいバージョンの Azure CLI がインストールされていることを確認する

1. [Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.6.0` 以上であることを確認します。

   ```azurecli
    az --version
   ```

   Azure CLI のバージョンが `2.6.0` より低い場合は、新しいバージョンをインストールします。 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="connect-to-the-account"></a>アカウントに接続する

1. Azure CLI をローカルで使用している場合は、login コマンドを実行します。

   ```azurecli
   az login
   ```

   CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

   それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。 次に、ブラウザーでアカウントの資格情報を使用してサインインします。

   さまざまな認証方法の詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md)」を参照してください。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

> [!NOTE]
> この記事に示す例は、Azure Active Directory (Azure AD) 認証を示しています。 認証方法の詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md)」を参照してください。

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 `az storage fs create` コマンドを使用して、ファイル システムを作成できます。 

この例では、`my-file-system` という名前のコンテナーを作成します。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>コンテナーのプロパティを表示する

`az storage fs show` コマンドを使用して、コンテナーのプロパティをコンソールに出力できます。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>コンテナーの内容を一覧表示する

`az storage fs file list` コマンドを使用して、ディレクトリの内容を一覧表示します。

この例では、`my-file-system` という名前のコンテナーの内容を一覧表示します。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>コンテナーを削除する

`az storage fs delete` コマンドを使用してコンテナーを削除します。

この例では、`my-file-system` という名前のコンテナーを削除します。 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>ディレクトリを作成する

`az storage fs directory create` コマンドを使用して、ディレクトリ参照を作成します。 

この例では、`mystorageaccount` という名前のアカウントにある `my-file-system` という名前のコンテナーに `my-directory` という名前のディレクトリを追加します。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>ディレクトリのプロパティを表示する

`az storage fs directory show` コマンドを使用して、ディレクトリのプロパティをコンソールに出力できます。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

`az storage fs directory move` コマンドを使用して、ディレクトリの名前変更または移動を行います。

この例では、同じコンテナー内でディレクトリの名前を `my-directory` から `my-new-directory` に変更します。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

この例では、ディレクトリを `my-second-file-system` という名前のコンテナーに移動します。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

`az storage fs directory delete` コマンドを使用してディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>ディレクトリが存在するかどうか確認する

`az storage fs directory exists` コマンドを使用して、コンテナーに特定のディレクトリが存在するかどうかを確認します。

この例では、`my-file-system` コンテナーに、`my-directory` という名前のディレクトリが存在するかどうかを確認します。 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

`az storage fs file download` コマンドを使用して、ディレクトリからファイルをダウンロードします。

この例では、`my-directory` という名前のディレクトリから `upload.txt` という名前のファイルをダウンロードします。 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

`az storage fs file list` コマンドを使用して、ディレクトリの内容を一覧表示します。

この例では、`mystorageaccount` という名前のストレージ アカウントの `my-file-system` コンテナーにある `my-directory` という名前のディレクトリの内容を一覧表示します。 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

`az storage fs directory upload` コマンドを使用して、ファイルをディレクトリにアップロードします。

この例では、`upload.txt` という名前のファイルを `my-directory` という名前のディレクトリにアップロードします。 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>ファイルのプロパティを表示する

`az storage fs file show` コマンドを使用して、ファイルのプロパティをコンソールに出力できます。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>ファイルを名前変更または移動する

`az storage fs file move` コマンドを使用して、ファイルの名前変更または移動を行います。

この例では、ファイルの名前を `my-file.txt` から `my-file-renamed.txt` に変更します。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>ファイルを削除する

`az storage fs file delete` コマンドを使用してファイルを削除します。

この例では、`my-file.txt` という名前のファイルを削除します。

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>関連項目

- [サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [フィードバックのご提供](https://github.com/Azure/azure-cli-extensions/issues)
- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure Data Lake Storage Gen2 で Azure CLI を使用して ACL を管理する](data-lake-storage-acl-cli.md)