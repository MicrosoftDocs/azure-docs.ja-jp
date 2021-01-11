---
title: Azure Data Lake Storage Gen2 でファイルと ACL に Azure CLI を使用する
description: Azure CLI を使用して、階層型名前空間を持つストレージ アカウントでディレクトリ、ファイル、ディレクトリのアクセス制御リスト (ACL) を管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6140260b75580270b365e59358d97e0a54c7b4a7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503941"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Azure CLI を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、[Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用して、階層型名前空間を持つストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

[サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [フィードバックを送る](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。
> * Azure CLI バージョン `2.6.0` 以上。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>正しいバージョンの Azure CLI がインストールされていることを確認する

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を開きます。または、Azure CLI をローカルに[インストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.6.0` 以上であることを確認します。

   ```azurecli
    az --version
   ```
   Azure CLI のバージョンが `2.6.0` より低い場合は、新しいバージョンをインストールします。 「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

## <a name="connect-to-the-account"></a>アカウントに接続する

1. Azure CLI をローカルで使用している場合は、login コマンドを実行します。

   ```azurecli
   az login
   ```

   CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

   それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。 次に、ブラウザーでアカウントの資格情報を使用してサインインします。

   さまざまな認証方法の詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](../common/authorize-data-operations-cli.md)」を参照してください。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

> [!NOTE]
> この記事に示す例は、Azure Active Directory (AD) 認証を示しています。 認証方法の詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](../common/authorize-data-operations-cli.md)」を参照してください。

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

## <a name="manage-permissions"></a>アクセス許可の管理

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してコマンドを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

### <a name="get-an-acl"></a>ACL を取得する

`az storage fs access show` コマンドを使用して、**ディレクトリ**の ACL を取得します。

この例では、ディレクトリの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access show` コマンドを使用して、**ファイル**のアクセス許可を取得します。 

この例では、ファイルの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

次の画像は、ディレクトリの ACL を取得した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可があります。 所有グループには、読み取りと実行のアクセス許可のみがあります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="set-an-acl"></a>ACL を設定する

`az storage fs access set` コマンドを使用して、**ディレクトリ**の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに*既定の* ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access set` コマンドを使用して、**ファイル**の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのファイルに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

次の画像は、ファイルの ACL を設定した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

この例では、所有ユーザーと所有グループには、読み取りと書き込みのアクセス許可のみがあります。 他のすべてのユーザーには、書き込みと実行のアクセス許可があります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="update-an-acl"></a>ACL を更新する

`az storage fs access set` コマンドを使用して、このアクセス許可を設定することもできます。 

ディレクトリまたはファイルの ACL を更新するには、`-permissions` パラメーターを短縮形の ACL に設定します。

この例では、**ディレクトリ**の ACL を更新します。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、**ファイル**の ACL を更新します。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

`--owner` または `group` パラメーターをユーザーのエンティティ ID またはユーザー プリンシパル名 (UPN) に設定して、ディレクトリまたはファイルの所有ユーザーとグループを更新することもできます。 

この例では、ディレクトリの所有者を変更します。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、ファイルの所有者を変更します。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>関連項目

* [サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [フィードバックのご提供](https://github.com/Azure/azure-cli-extensions/issues)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


