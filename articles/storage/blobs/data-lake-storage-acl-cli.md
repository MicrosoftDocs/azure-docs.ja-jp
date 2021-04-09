---
title: Azure Data Lake Storage Gen2 で Azure CLI を使用して ACL を設定する
description: Azure CLI を使用して、階層型名前空間を持つストレージ アカウントのアクセス制御リスト (ACL) を管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563166"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で Azure CLI を使用して ACL を管理する

この記事では、[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/) を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、および更新する方法について説明します。

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 しかし、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。

[参照](/cli/azure/storage/fs/access) | [サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [フィードバックの提供](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.14.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (Azure AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>正しいバージョンの Azure CLI がインストールされていることを確認する

1. [Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.14.0` 以上であることを確認します。

   ```azurecli
    az --version
   ```

   Azure CLI のバージョンが `2.14.0` より低い場合は、新しいバージョンをインストールします。 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

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

## <a name="get-acls"></a>ACL を取得する

`az storage fs access show` コマンドを使用して、**ディレクトリ** の ACL を取得します。

この例では、ディレクトリの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access show` コマンドを使用して、**ファイル** のアクセス許可を取得します。 

この例では、ファイルの ACL を取得して、その ACL をコンソールに出力します。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

次の画像は、ディレクトリの ACL を取得した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可があります。 所有グループには、読み取りと実行のアクセス許可のみがあります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

## <a name="set-acls"></a>ACL を設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を更新する](#update-acls)」セクションを参照してください。  

ACL を "*設定*" する場合は、所有ユーザーのエントリ、所有グループのエントリ、および他のすべてのユーザーのエントリを追加する必要があります。 所有ユーザー、所有グループ、およびその他のすべてのユーザーの詳細については、「[ユーザーと ID](data-lake-storage-access-control.md#users-and-identities)」を参照してください。

ここでは、次の操作方法について説明します。

- ACL を設定する
- ACL を再帰的に設定する

### <a name="set-an-acl"></a>ACL を設定する

`az storage fs access set` コマンドを使用して、**ディレクトリ** の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、所有ユーザー、所有グループ、またはその他のユーザーのディレクトリに *既定の* ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access set` コマンドを使用して、**ファイル** の ACL を設定します。 

この例では、所有ユーザー、所有グループ、またはその他のユーザーのファイルに ACL を設定し、その ACL をコンソールに出力します。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 特定のグループまたはユーザーの ACL を設定するには、それぞれのオブジェクト ID を使用します。 たとえば、`group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` または `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。

次の画像は、ファイルの ACL を設定した後の出力を示しています。

![ACL 出力を取得する 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

この例では、所有ユーザーと所有グループには、読み取りと書き込みのアクセス許可のみがあります。 他のすべてのユーザーには、書き込みと実行のアクセス許可があります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="set-acls-recursively"></a>ACL を再帰的に設定する

[az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) コマンドを使用して、ACL を再帰的に設定します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **既定** の ACL エントリを設定する場合は、各エントリにプレフィックス `default:` を追加します。 たとえば、`default:user::rwx` または `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x` です。

## <a name="update-acls"></a>ACL を更新する

ACL を "*更新する*" 場合は、ACL を置換するのでなく ACL を変更します。 たとえば、ACL にリストされている他のセキュリティ プリンシパルに影響を与えることなく、新しいセキュリティ プリンシパルを ACL に追加することができます。  ACL を更新するのでなく、置換する場合は、この記事の「[ACL を設定する](#set-acls)」セクションを参照してください。

ACL を更新するには、更新したい ACL エントリを含む新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の更新操作で使用します。 既存の ACL は取得せずに、更新する ACL エントリを指定するだけです。

ここでは、次の操作方法について説明します。

- ACL を更新する
- ACL を再帰的に更新する

### <a name="update-an-acl"></a>ACL を更新する

`az storage fs access set` コマンドを使用して、このアクセス許可を設定することもできます。 

ディレクトリまたはファイルの ACL を更新するには、`-permissions` パラメーターを短縮形の ACL に設定します。

この例では、**ディレクトリ** の ACL を更新します。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、**ファイル** の ACL を更新します。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 特定のグループまたはユーザーの ACL を更新するには、それぞれのオブジェクト ID を使用します。 たとえば、`group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` または `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。

`--owner` または `group` パラメーターをユーザーのエンティティ ID またはユーザー プリンシパル名 (UPN) に設定して、ディレクトリまたはファイルの所有ユーザーとグループを更新することもできます。

この例では、ディレクトリの所有者を変更します。

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

この例では、ファイルの所有者を変更します。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>ACL を再帰的に更新する

[az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) コマンドを使用して、ACL を再帰的に更新します。

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **既定** の ACL エントリを更新する場合は、各エントリにプレフィックス `default:` を追加します。 たとえば、`default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x` のようにします。

## <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

1 つ以上の ACL エントリを再帰的に削除できます。 ACL エントリを削除するには、削除する ACL エントリ用に新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の削除操作で使用します。 既存の ACL は取得せずに、削除する ACL エントリを指定するだけです。

[az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) コマンドを使用して、ACL のエントリを削除します。

この例では、コンテナーのルート ディレクトリから ACL エントリを削除します。  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **既定** の ACL エントリを削除する場合は、各エントリにプレフィックス `default:` を追加します。 たとえば、`default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` のようにします。

## <a name="recover-from-failures"></a>エラーからの復旧

ACL を再帰的に変更するときに、実行時またはアクセス許可のエラーが発生する場合があります。 実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

障害が発生した場合は、`--continue-on-failure` パラメーターを `false` に設定することにより、後続トークンを返すことができます。 エラーに対処した後、コマンドを再度実行し、`--continuation` パラメーターを後続トークンに設定することにより、障害が発生した時点からプロセスを再開できます。

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

アクセス許可エラーによって中断されずにプロセスを完了したい場合は、それを指定できます。

プロセスが中断されずに確実に完了されるようにするには、`--continue-on-failure` パラメーターを `true` に設定します。

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>関連項目

- [サンプル](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [フィードバックのご提供](https://github.com/Azure/azure-cli-extensions/issues)
- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)