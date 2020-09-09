---
title: Azure Data Lake Storage Gen2 の ACL を再帰的に設定する | Microsoft Docs
description: 親ディレクトリの既存の子項目に対して、アクセス制御リストを再帰的に追加、更新、および削除することができます。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: cbf5d8286d6f181c69cd090df6cf595934cd547e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942025"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL) を再帰的に設定する

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 また、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。

> [!NOTE]
> アクセス リストを再帰的に設定する機能はパブリック プレビュー段階であり、すべてのリージョンで使用できます。  

[ライブラリ](#libraries) | [サンプル](#code-samples) | [ベスト プラクティス](#best-practice-guidelines) | [フィードバックの送信](#provide-feedback)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。

- 再帰的な ACL プロセスを実行するための適切なアクセス許可。 適切なアクセス許可には、次のいずれかが含まれます。 

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)。   

  - 再帰的な ACL プロセスを適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。 

- ディレクトリとファイルに ACL を適用する方法を理解していること。 「[Azure Data Lake Storage Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。 

PowerShell、.NET SDK、および Python SDK のインストール ガイドについては、この記事の「**プロジェクトの設定**」セクションを参照してください。

## <a name="set-up-your-project"></a>プロジェクトの設定

必要なライブラリをインストールします。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET Framework がインストールされていることを確認します。 「[.NET Framework のダウンロード](https://dotnet.microsoft.com/download/dotnet-framework)」を参照してください。
 
2. 次のコマンドを使用して、インストールされている PowerShell のバージョンが `5.1` 以降であることを確認します。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   お使いの PowerShell のバージョンをアップグレードするには、「[既存の Windows PowerShell をアップグレードする](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)」を参照してください。
    
3. PowerShellGet モジュールの最新バージョンをインストールします。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. PowerShell コンソールを閉じてから、再度開きます。

5. **Az.Storage** プレビュー モジュールをインストールします。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)」を参照してください。

### <a name="net"></a>[.NET](#tab/dotnet)

1. コマンド ウィンドウを開きます (例: Windows PowerShell)。

2. プロジェクト ディレクトリから、`dotnet add package` コマンドを使用して Azure.Storage.Files.DataLake プレビュー パッケージをインストールします。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.3.0-dev.20200811.1 -s https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json
   ```

3. これらの using ステートメントをコード ファイルの先頭に追加します。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. [Python 向けの Azure Data Lake Storage クライアント ライブラリ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)をダウンロードします。

2. ダウンロードしたライブラリを、[pip](https://pypi.org/project/pip/) を使用してインストールします。

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

次の import ステートメントを、コード ファイルの先頭に追加します。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>アカウントに接続する

接続には、Azure Active Directory (AD) を使用するか、アカウント キーを使用することができます。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Windows PowerShell コマンド ウィンドウを開き、`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、ディレクトリを作成して管理するストレージ アカウントのサブスクリプションに設定します。 この例では、`<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

次に、コマンドでストレージ アカウントに対する承認を取得する方法を選択します。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>オプション 1: Azure Active Directory (AD) を使用して承認を取得する

この方法では、システムによって、ユーザー アカウントに適切なロールベースのアクセス制御 (RBAC) の割り当てと ACL のアクセス許可が付与されます。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

次の表は、サポートされている各ロールとその ACL 設定機能を示しています。

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>オプション 2:ストレージ アカウント キーを使用して承認を取得する

この方法では、RBAC アクセス許可または ACL アクセス許可はシステムによってチェックされません。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

この記事のスニペットを使用するには、ストレージ アカウントを表す [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) インスタンスを作成する必要があります。

#### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)を使用して、Azure AD でアプリケーションを認証できます。

パッケージをインストールした後、この using ステートメントをコード ファイルの先頭に追加します。

```csharp
using Azure.Identity;
```

クライアント ID、クライアント シークレット、およびテナント ID を取得します。 これを行うには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[ロールベースのアクセス制御 (RBAC) ロール](../../role-based-access-control/overview.md)をセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) インスタンスを作成します。  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

このアプローチはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) インスタンスを作成します。

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> その他の例については、[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)のドキュメントを参照してください。

### <a name="python"></a>[Python](#tab/python)

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。 このプロセスの一環として、次のいずれかの[ロールベースのアクセス制御 (RBAC) ロール](../../role-based-access-control/overview.md)をセキュリティ プリンシパルに割り当てる必要があります。 

|Role|ACL 設定機能|
|--|--|
|[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|アカウント内のすべてのディレクトリとファイル。|
|[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|セキュリティ プリンシパルによって所有されているディレクトリとファイルのみ。|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> その他の例については、[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)のドキュメントを参照してください。

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

- `storage_account_key` プレースホルダーの値は、実際のストレージ アカウントのアクセス キーに置き換えます。

---

## <a name="set-an-acl-recursively"></a>ACL を再帰的に設定する

ACL は再帰的に設定できます。  

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Set-AzDataLakeGen2AclRecursive` コマンドレットを使用して、ACL を再帰的に設定します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient.SetAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に設定します。 このメソッドに [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) の [List](/dotnet/api/system.collections.generic.list-1) を渡します。 [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) はそれぞれ ACL エントリを定義します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.set_access_control_recursive** メソッドを呼び出すことによって、ACL を再帰的に設定します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>ACL を再帰的に更新する

既存の ACL を再帰的に更新することができます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**Update-AzDataLakeGen2AclRecursive** コマンドレットを使用して、ACL を再帰的に更新します。 

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に更新します。 

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.update_access_control_recursive** メソッドを呼び出すことによって、ACL を再帰的に更新します。 

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

1 つ以上の ACL エントリを再帰的に削除できます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**Remove-AzDataLakeGen2AclRecursive** コマンドレットを使用して、ACL エントリを再帰的に削除します。 

この例では、コンテナーのルート ディレクトリから ACL エントリを削除します。  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** メソッドを呼び出すことによって、ACL を再帰的に削除します。 

この例では、`my-parent-directory` という名前のディレクトリの ACL から ACL エントリを削除します。 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.remove_access_control_recursive** メソッドを呼び出すことによって、ACL エントリを削除します。 

この例では、`my-parent-directory` という名前のディレクトリの ACL から ACL エントリを削除します。 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>エラーからの復旧

実行時エラーまたはアクセス許可エラーが発生する可能性があります。 実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

変数に結果を返します。 失敗したエントリをフォーマットしたテーブルにパイプで渡します。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

テーブルの出力に基づいて、アクセス許可のエラーを修正し、継続トークンを使用して実行を再開できます。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

この例では、エラーが発生した場合に継続トークンを返します。 アプリケーションでは、エラーが解決された後にこの例のメソッドを再び呼び出し、継続トークンを渡すことができます。 この例のメソッドが初めて呼び出される場合は、アプリケーションで継続トークン パラメーターに対して ``null`` の値を渡すことができます。 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, null, continuationToken: continuationToken);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

この例では、エラーが発生した場合に継続トークンを返します。 アプリケーションでは、エラーが解決された後にこの例のメソッドを再び呼び出し、継続トークンを渡すことができます。 この例のメソッドが初めて呼び出される場合は、アプリケーションで継続トークン パラメーターに対して ``None`` の値を渡すことができます。 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>リソース

このセクションには、ライブラリとコード サンプルへのリンクが含まれています。

#### <a name="libraries"></a>ライブラリ

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>コード サンプル

- PowerShell:[Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D) | [サンプル](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0) | [サンプル](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0) | [サンプル](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>ベスト プラクティス ガイドライン

このセクションでは、ACL を再帰的に設定するためのベスト プラクティス ガイドラインについて説明します。 

#### <a name="handling-runtime-errors"></a>実行時エラーの処理

実行時エラーは、さまざまな理由で発生する可能性があります (たとえば、停電やクライアント接続の問題など)。 実行時エラーが発生した場合は、再帰的な ACL プロセスを再開します。 ACL は、悪影響を及ぼすことなく項目に再適用できます。 

#### <a name="handling-permission-errors-403"></a>アクセス許可エラー (403) の処理

再帰的な ACL プロセスの実行中にアクセス制御の例外が発生した場合、AD の[セキュリティ プリンシパル](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)に、ディレクトリ階層内にある 1 つ以上の子項目に ACL を適用するための十分なアクセス許可がない可能性があります。 アクセス許可エラーが発生すると、プロセスが停止し、継続トークンが提供されます。 アクセス許可の問題を修正してから、継続トークンを使用して残りのデータセットを処理します。 既に正常に処理済みのディレクトリとファイルをもう一度処理する必要はありません。 再帰的な ACL プロセスを再開することも選択できます。 ACL は、悪影響を及ぼすことなく項目に再適用できます。 

#### <a name="credentials"></a>資格情報 

ターゲット ストレージ アカウントまたはコンテナーのスコープ内の[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられている Azure AD セキュリティ プリンシパルをプロビジョニングすることをお勧めします。 

#### <a name="performance"></a>パフォーマンス 

待機時間を短縮するには、ストレージ アカウントと同じリージョンにある Azure 仮想マシン (VM) で再帰的な ACL プロセスを実行することをお勧めします。 

#### <a name="acl-limits"></a>ACL の制限

ディレクトリまたはファイルに適用できる ACL の最大数は、アクセス ACL が 32 個と既定 ACL が 32 個です。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に関するページを参照してください。

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>フィードバックの送信または問題の報告

次のいずれかのページを使用して、フィードバックを送信したり、問題を報告したりすることができます。[PowerShell](https://github.com/Azure/azure-powershell/issues/new?assignees=&labels=triage&template=az-module-bug-report.md&title=)、[.NET](https://github.com/Azure/azure-sdk-for-net/issues/new?assignees=&labels=&template=bug_report.md&title=)、[Python](https://github.com/Azure/azure-sdk-for-python/issues/new?assignees=&labels=&template=bug_report.md&title=)

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [既知の問題](data-lake-storage-known-issues.md)


