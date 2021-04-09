---
title: Azure Data Lake Storage Gen2 で Python を使用して ACL を管理する
description: Python を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653701"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で Python を使用して ACL を管理する

この記事では、Python を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、更新する方法について説明します。 

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 しかし、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 

[パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [再帰 ACL のサンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) | [API リファレンス](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="set-up-your-project"></a>プロジェクトの設定

Python 向けの Azure Data Lake Storage クライアント ライブラリを、[pip](https://pypi.org/project/pip/) を使用してインストールします。

```
pip install azure-storage-file-datalake
```

次の import ステートメントを、コード ファイルの先頭に追加します。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」を参照してください。

[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)を使用して、Azure AD でアプリケーションを認証できます。

クライアント ID、クライアント シークレット、およびテナント ID を取得します。 これを行うには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) ロールをセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> その他の例については、[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)のドキュメントを参照してください。

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- `storage_account_name` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

- `storage_account_key` プレースホルダーの値は、実際のストレージ アカウントのアクセス キーに置き換えます。

## <a name="set-acls"></a>ACL を設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を更新する](#update-acls-recursively)」セクションを参照してください。  

ここでは、次の操作方法について説明します。

- ディレクトリの ACL を設定する
- ファイルの ACL を設定する

### <a name="set-the-acl-of-a-directory"></a>ディレクトリの ACL を設定する

**DataLakeDirectoryClient.get_access_control** メソッドを呼び出してディレクトリのアクセス制御リスト (ACL) を取得し、**DataLakeDirectoryClient.set_access_control** メソッドを呼び出して ACL を設定します。

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

また、コンテナーのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**FileSystemClient._get_root_directory_client** メソッドを呼び出します。

### <a name="set-the-acl-of-a-file"></a>ファイルの ACL を設定する

**DataLakeFileClient.get_access_control** メソッドを呼び出してファイルのアクセス制御リスト (ACL) を取得し、**DataLakeFileClient.set_access_control** メソッドを呼び出して ACL を設定します。

この例では、`my-file.txt` という名前のファイルの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>ACL を再帰的に設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を再帰的に更新する](#update-acls-recursively)」セクションを参照してください。

**DataLakeDirectoryClient.set_access_control_recursive** メソッドを呼び出すことによって、ACL を再帰的に設定します。

**既定の** ACL エントリを設定する場合は、各 ACL エントリ文字列の先頭に文字列 `default:` を追加します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。

このメソッドは、既定の ACL を設定するかどうかを指定する `is_default_scope` という名前のブール型パラメーターを受け取ります。 そのパラメーターが `True` の場合、ACL エントリの一覧の前に文字列 `default:` が付きます。

これらの ACL エントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

バッチ サイズを指定してバッチ内の ACL を再帰的に処理する例については、Python の[サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)をご覧ください。

## <a name="update-acls-recursively"></a>ACL を再帰的に更新する

ACL を "*更新する*" 場合は、ACL を置換するのでなく ACL を変更します。 たとえば、ACL にリストされている他のセキュリティ プリンシパルに影響を与えることなく、新しいセキュリティ プリンシパルを ACL に追加することができます。  ACL を更新するのでなく、置換する場合は、この記事の「[ACL を設定する](#set-acls)」セクションを参照してください。

ACL を再帰的に更新するには、更新したい ACL エントリを含む新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の更新操作で使用します。 既存の ACL は取得せずに、更新する ACL エントリを指定するだけです。 **DataLakeDirectoryClient.update_access_control_recursive** メソッドを呼び出すことによって、ACL を再帰的に更新します。 **既定の** ACL エントリを更新する場合は、各 ACL エントリ文字列の先頭に文字列 `default:` を追加します。

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 このメソッドは、既定の ACL を更新するかどうかを指定する `is_default_scope` という名前のブール型パラメーターを受け取ります。 そのパラメーターが `True` の場合、更新された ACL エントリの前に文字列 `default:` が付きます。  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

バッチ サイズを指定してバッチ内の ACL を再帰的に処理する例については、Python の[サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)をご覧ください。

## <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

1 つ以上の ACL エントリを削除できます。 ACL エントリを再帰的に削除するには、削除する ACL エントリ用に新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の削除操作で使用します。 既存の ACL は取得せずに、削除する ACL エントリを指定するだけです。 

**DataLakeDirectoryClient.remove_access_control_recursive** メソッドを呼び出すことによって、ACL エントリを削除します。 **既定の** ACL エントリを削除する場合は、ACL エントリ文字列の先頭に文字列 `default:` を追加します。 

この例では、`my-parent-directory` という名前のディレクトリの ACL から ACL エントリを削除します。 このメソッドは、既定の ACL からエントリを削除するかどうかを指定する `is_default_scope` という名前のブール型パラメーターを受け取ります。 そのパラメーターが `True` の場合、更新された ACL エントリの前に文字列 `default:` が付きます。 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

バッチ サイズを指定してバッチ内の ACL を再帰的に処理する例については、Python の[サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)をご覧ください。

## <a name="recover-from-failures"></a>エラーからの復旧

実行時エラーまたはアクセス許可エラーが発生する可能性があります。 実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

この例では、エラーが発生した場合に継続トークンを返します。 アプリケーションでは、エラーが解決された後にこの例のメソッドを再び呼び出し、継続トークンを渡すことができます。 この例のメソッドが初めて呼び出される場合は、アプリケーションで継続トークン パラメーターに対して ``None`` の値を渡すことができます。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

バッチ サイズを指定してバッチ内の ACL を再帰的に処理する例については、Python の[サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)をご覧ください。

アクセス許可エラーによって中断されずにプロセスを完了したい場合は、それを指定できます。

プロセスが中断されずに確実に完了されるようにするには、**DataLakeDirectoryClient.set_access_control_recursive** メソッドに後続トークンを渡さないようにします。

この例では、ACL エントリを再帰的に設定します。 このコードでアクセス許可エラーが発生した場合は、そのエラーが記録されて、実行が継続されます。 この例では、エラーの数をコンソールに出力します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

バッチ サイズを指定してバッチ内の ACL を再帰的に処理する例については、Python の[サンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>関連項目

- [API リファレンス ドキュメント](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
- [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)