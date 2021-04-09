---
title: Azure Data Lake Storage Gen2 で .NET を使用して ACL を設定する
description: .NET を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653703"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で .NET を使用して ACL を管理する

この記事では、.NET を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、更新する方法について説明します。

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 しかし、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。

[パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [再帰的な ACL のサンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0) | [API リファレンス](/dotnet/api/azure.storage.files.datalake) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [フィードバックを送信する](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="set-up-your-project"></a>プロジェクトの設定

まず、[Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet パッケージをインストールします。

1. コマンド ウィンドウを開きます (例: Windows PowerShell)。

2. プロジェクト ディレクトリから、`dotnet add package` コマンドを使用して Azure.Storage.Files.DataLake プレビュー パッケージをインストールします。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   次に、これらの using ステートメントをコード ファイルの先頭に追加します。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成する必要があります。 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」を参照してください。

[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)を使用して、Azure AD でアプリケーションを認証できます。

パッケージをインストールした後、この using ステートメントをコード ファイルの先頭に追加します。

```csharp
using Azure.Identity;
```

クライアント ID、クライアント シークレット、およびテナント ID を取得します。 これを行うには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) ロールをセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成します。  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> その他の例については、[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)のドキュメントを参照してください。

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。

この例では、アカウント キーを使用して [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL を設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を更新する](#update-acls)」セクションを参照してください。  

ACL を "*設定*" する場合は、所有ユーザーのエントリ、所有グループのエントリ、および他のすべてのユーザーのエントリを追加する必要があります。 所有ユーザー、所有グループ、およびその他のすべてのユーザーの詳細については、「[ユーザーと ID](data-lake-storage-access-control.md#users-and-identities)」を参照してください。

ここでは、次の操作方法について説明します。

- ディレクトリの ACL を設定する
- ファイルの ACL を設定する
- ACL を再帰的に設定する

### <a name="set-the-acl-of-a-directory"></a>ディレクトリの ACL を設定する

[DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) メソッドを呼び出してディレクトリのアクセス制御リスト (ACL) を取得し、[DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 `user::rwx,group::r-x,other::rw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

また、コンテナーのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、[DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) メソッドに空の文字列 (`""`) を渡します。

### <a name="set-the-acl-of-a-file"></a>ファイルの ACL を設定する

[DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) メソッドを呼び出してファイルのアクセス制御リスト (ACL) を取得し、[DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

この例では、`my-file.txt` という名前のファイルの ACL を取得して設定します。 `user::rwx,group::r-x,other::rw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>ACL を再帰的に設定する

**DataLakeDirectoryClient.SetAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に設定します。 このメソッドに [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [List](/dotnet/api/system.collections.generic.list-1) を渡します。 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) はそれぞれ ACL エントリを定義します。

**既定の** ACL エントリを設定する場合は、[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) プロパティを **true** に設定できます。 

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 このメソッドは、既定の ACL を設定するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) のコンストラクターで使用されます。 これらの ACL エントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、.NET の[サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)をご覧ください。

## <a name="update-acls"></a>ACL を更新する

ACL を "*更新する*" 場合は、ACL を置換するのでなく ACL を変更します。 たとえば、ACL にリストされている他のセキュリティ プリンシパルに影響を与えることなく、新しいセキュリティ プリンシパルを ACL に追加することができます。  ACL を更新するのでなく、置換する場合は、この記事の「[ACL を設定する](#set-acls)」セクションを参照してください。

ここでは、次の操作方法について説明します。

- ACL を更新する
- ACL を再帰的に更新する

### <a name="update-an-acl"></a>ACL を更新する

まず、[DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) メソッドを呼び出して、ディレクトリの ACL を取得します。 ACL エントリの一覧を [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) オブジェクトの新しい [List](/dotnet/api/system.collections.generic.list-1) にコピーします。 次に、一覧内で更新するエントリを見つけて、置き換えます。 [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して ACL を設定します。

この例では、他のすべてのユーザーの ACL エントリを置き換えることで、コンテナーのルート ACL を更新します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>ACL を再帰的に更新する

ACL を再帰的に更新するには、更新したい ACL エントリを含む新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の更新操作で使用します。 既存の ACL は取得せずに、更新する ACL エントリを指定するだけです。

**DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に更新します。  このメソッドに [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [List](/dotnet/api/system.collections.generic.list-1) を渡します。 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) はそれぞれ ACL エントリを定義します。

**既定の** ACL エントリを更新する場合は、[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) プロパティを **true** に設定できます。

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。 このメソッドは、既定の ACL を更新するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) のコンストラクターで使用されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

バッチ サイズを指定してバッチ内の ACL を再帰的に更新する例については、.NET の[サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)をご覧ください。

## <a name="remove-acl-entries"></a>ACL エントリを削除する

1 つ以上の ACL エントリを削除できます。 ここでは、次の操作方法について説明します。

- ACL エントリを削除する
- ACL エントリを再帰的に削除する

### <a name="remove-an-acl-entry"></a>ACL エントリを削除する

まず、[DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) メソッドを呼び出して、ディレクトリの ACL を取得します。 ACL エントリの一覧を [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) オブジェクトの新しい [List](/dotnet/api/system.collections.generic.list-1) にコピーします。 次に、削除するエントリを見つけて、コレクションの [Remove](/dotnet/api/system.collections.ilist.remove) メソッドを呼び出します。 [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) メソッドを呼び出して、更新された ACL を設定します。

この例では、他のすべてのユーザーの ACL エントリを置き換えることで、コンテナーのルート ACL を更新します。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

ACL エントリを再帰的に削除するには、削除する ACL エントリ用に新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の削除操作で使用します。 既存の ACL は取得せずに、削除する ACL エントリを指定するだけです。 

**DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に削除します。 このメソッドに [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [List](/dotnet/api/system.collections.generic.list-1) を渡します。 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) はそれぞれ ACL エントリを定義します。 

**既定の** ACL エントリを削除する場合は、[PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) プロパティを **true** に設定できます。 

この例では、`my-parent-directory` という名前のディレクトリの ACL から ACL エントリを削除します。 このメソッドは、既定の ACL からエントリを削除するかどうかを指定する `isDefaultScope` という名前のブール型パラメーターを受け取ります。 そのパラメーターは [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) のコンストラクターで使用されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

バッチ サイズを指定してバッチ内の ACL を再帰的に削除する例については、.NET の[サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)をご覧ください。

## <a name="recover-from-failures"></a>エラーからの復旧

ACL を再帰的に変更するときに、実行時またはアクセス許可のエラーが発生する場合があります。 実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

この例では、エラーが発生した場合に継続トークンを返します。 アプリケーションでは、エラーが解決された後にこの例のメソッドを再び呼び出し、継続トークンを渡すことができます。 この例のメソッドが初めて呼び出される場合は、アプリケーションで継続トークン パラメーターに対して `null` の値を渡すことができます。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、.NET の[サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)をご覧ください。

アクセス許可エラーによって中断されずにプロセスを完了したい場合は、それを指定できます。

プロセスが中断されずに確実に完了されるようにするには、**AccessControlChangedOptions** オブジェクトを渡し、そのオブジェクトの **ContinueOnFailure** プロパティを ``true`` に設定します。

この例では、ACL エントリを再帰的に設定します。 このコードでアクセス許可エラーが発生した場合は、そのエラーが記録されて、実行が継続されます。 この例では、エラーの数をコンソールに出力します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、.NET の[サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>関連項目

- [API リファレンス ドキュメント](/dotnet/api/azure.storage.files.datalake)
- [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [フィードバックを送る](https://github.com/Azure/azure-sdk-for-net/issues)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)