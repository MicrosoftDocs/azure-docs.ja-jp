---
title: Azure Data Lake Storage Gen2 で Java を使用して ACL を設定する
description: Java 用 Azure Storage ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントでアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653702"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で Java を使用して ACL を管理する

この記事では、Java を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、更新する方法について説明します。 

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 しかし、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 

[パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API リファレンス](/java/api/overview/azure/storage-file-datalake-readme) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="set-up-your-project"></a>プロジェクトの設定

開始するには、[こちらのページ](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)にアクセスして、最新バージョンの Java ライブラリを検索します。 次に、お使いのテキスト エディターで *pom.xml* ファイルを開きます。 該当のバージョンを参照する依存関係要素を追加します。

Azure Active Directory (AD) を使用してクライアント アプリケーションを認証したい場合には、Azure Secret Client Library に依存関係を追加します。 「[プロジェクトに Secret Client Library パッケージを追加する](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project) 」を参照してください。

次に、以下の import ステートメントをコード ファイルに追加します。

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用して接続する

[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)を使用して、Azure AD でアプリケーションを認証できます。

クライアント ID、クライアント シークレット、およびテナント ID を取得します。 これを行うには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) ロールをセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> その他の例については、「[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)」のドキュメントを参照してください。

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL を設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を更新する](#update-acls)」セクションを参照してください。  

ACL を "*設定*" する場合は、所有ユーザーのエントリ、所有グループのエントリ、および他のすべてのユーザーのエントリを追加する必要があります。 所有ユーザー、所有グループ、およびその他のすべてのユーザーの詳細については、「[ユーザーと ID](data-lake-storage-access-control.md#users-and-identities)」を参照してください。

ここでは、次の操作方法について説明します。

- ディレクトリの ACL を設定する
- ファイルの ACL を設定する
- ACL を再帰的に設定する 

### <a name="set-the-acl-of-a-directory"></a>ディレクトリの ACL を設定する

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`""`) を渡します。

### <a name="set-the-acl-of-a-file"></a>ファイルの ACL を設定する

この例では、`upload-file.txt` という名前のファイルの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>ACL を再帰的に設定する

**DataLakeDirectoryClient.setAccessControlRecursive** メソッドを呼び出すことによって、ACL を再帰的に設定します。 このメソッドに [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) オブジェクトの [List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) を渡します。 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) はそれぞれ ACL エントリを定義します。

**既定の** ACL エントリを設定する場合は、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドを呼び出し、**true** の値を渡すことができます。 

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 このメソッドは、既定の ACL を設定するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドを呼び出すたびに使用されます。 これらの ACL エントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>ACL を更新する

ACL を "*更新する*" 場合は、ACL を置換するのでなく ACL を変更します。 たとえば、ACL にリストされている他のセキュリティ プリンシパルに影響を与えることなく、新しいセキュリティ プリンシパルを ACL に追加することができます。  ACL を更新するのでなく、置換する場合は、この記事の「[ACL を設定する](#set-acls)」セクションを参照してください。

ここでは、次の操作方法について説明します。

- ACL を更新する
- ACL を再帰的に更新する

### <a name="update-an-acl"></a>ACL を更新する

まず、[PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) メソッドを呼び出して、ディレクトリの ACL を取得します。 ACL エントリの一覧を [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry) 型の新しい **List** オブジェクトにコピーします。 次に、一覧内で更新するエントリを見つけて、置き換えます。 [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

この例では、他のすべてのユーザーのエントリを置き換えることで、`my-parent-directory` という名前のディレクトリの ACL を更新します。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`""`) を渡します。

### <a name="update-acls-recursively"></a>ACL を再帰的に更新する

ACL を再帰的に更新するには、更新したい ACL エントリを含む新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の更新操作で使用します。 既存の ACL は取得せずに、更新する ACL エントリを指定するだけです。

**DataLakeDirectoryClient.updateAccessControlRecursive** メソッドを呼び出して、ACL を再帰的に更新します。  このメソッドに [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) オブジェクトの [List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) を渡します。 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) はそれぞれ ACL エントリを定義します。 

**既定の** ACL エントリを更新する場合は、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドを呼び出し、**true** の値を渡すことができます。 

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。 このメソッドは、既定の ACL を更新するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドの呼び出しに使用されます。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>ACL エントリを削除する

1 つ以上の ACL エントリを削除できます。 ここでは、次の操作方法について説明します。

- ACL エントリを削除する
- ACL エントリを再帰的に削除する

### <a name="remove-an-acl-entry"></a>ACL エントリを削除する

まず、[PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) メソッドを呼び出して、ディレクトリの ACL を取得します。 ACL エントリの一覧を [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry) 型の新しい **List** オブジェクトにコピーします。 次に、削除するエントリを見つけて、**List** オブジェクトの **Remove** メソッドを呼び出します。 [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して、更新された ACL を設定します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

ACL エントリを再帰的に削除するには、削除する ACL エントリ用に新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の削除操作で使用します。 既存の ACL は取得せずに、削除する ACL エントリを指定するだけです。

**DataLakeDirectoryClient.removeAccessControlRecursive** メソッドを呼び出すことによって、ACL を再帰的に削除します。 このメソッドに [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) オブジェクトの [List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) を渡します。 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) はそれぞれ ACL エントリを定義します。 

**既定の** ACL エントリを削除する場合は、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドを呼び出し、**true** の値を渡すことができます。  

この例では、`my-parent-directory` という名前のディレクトリの ACL から ACL エントリを削除します。 このメソッドは、既定の ACL からエントリを削除するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは、[PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) の **setDefaultScope** メソッドの呼び出しに使用されます。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>エラーからの復旧

実行時エラーまたはアクセス許可エラーが発生する可能性があります。 実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

この例では、エラーが発生した場合に継続トークンを返します。 アプリケーションでは、エラーが解決された後にこの例のメソッドを再び呼び出し、継続トークンを渡すことができます。 この例のメソッドが初めて呼び出される場合は、アプリケーションで継続トークン パラメーターに対して `null` の値を渡すことができます。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

アクセス許可エラーによって中断されずにプロセスを完了したい場合は、それを指定できます。

プロセスが中断されずに確実に完了されるようにするには、[PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) オブジェクトの **setContinueOnFailure** メソッドを呼び出し、値 **true** を渡します。

この例では、ACL エントリを再帰的に設定します。 このコードでアクセス許可エラーが発生した場合は、そのエラーが記録されて、実行が継続されます。 この例では、エラーの数をコンソールに出力します。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>関連項目

- [API リファレンス ドキュメント](/java/api/overview/azure/storage-file-datalake-readme)
- [パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)