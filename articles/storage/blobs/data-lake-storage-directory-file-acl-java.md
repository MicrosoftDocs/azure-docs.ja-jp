---
title: ファイル と ACL 用の Azure Data Lake Storage Gen2 Java SDK
description: Java 用 Azure Storage ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、ディレクトリとファイル、およびディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0f7dfda46c7a4af8143c33b1b45001a88d3bed88
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319390"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、Java を使用してディレクトリ、ファイル、アクセス許可を作成および管理する方法を示します。 

[パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。

## <a name="set-up-your-project"></a>プロジェクトの設定

開始するには、[こちらのページ](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)にアクセスして、最新バージョンの Java ライブラリを検索します。 次に、お使いのテキスト エディターで *pom.xml* ファイルを開きます。 該当のバージョンを参照する依存関係要素を追加します。

Azure Active Directory (AD) を使用してクライアント アプリケーションを認証したい場合には、Azure Secret Client Library に依存関係を追加します。 「[プロジェクトに Secret Client Library パッケージを追加する](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project) 」を参照してください。

次に、以下の import ステートメントをコード ファイルに追加します。

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>アカウントに接続する 

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用して接続する

[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して、**DataLakeServiceClient** インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンの取得する](../common/storage-auth-aad-app.md)」を参照してください。

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> その他の例については、「[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)」のドキュメントを参照してください。


## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 これは、**DataLakeServiceClient.createFileSystem** メソッドを呼び出すことで作成できます。

この例では、`my-file-system` という名前のコンテナーを作成します。 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>ディレクトリを作成する

**DataLakeFileSystemClient.createDirectory** メソッドを呼び出して、ディレクトリ参照を作成します。

この例では、`my-directory` という名前のディレクトリをコンテナーに追加してから、`my-subdirectory` という名前のサブディレクトリを追加します。 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

**DataLakeDirectoryClient.rename** メソッドを呼び出して、ディレクトリを名前変更または移動します。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-subdirectory-renamed` に変更します。

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

この例では、`my-subdirectory-renamed` という名前のディレクトリを `my-directory-2` という名前のディレクトリのサブディレクトリに移動します。 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

**DataLakeDirectoryClient.deleteWithResponse** メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>ディレクトリ ACL を管理する

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`""`) を渡します。

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

まず、**DataLakeFileClient** クラスのインスタンスを作成して、ターゲット ディレクトリにファイル参照を作成します。 **DataLakeFileClient.append** メソッドを呼び出して、ファイルをアップロードします。 **DataLakeFileClient.FlushAsync** メソッドを呼び出して、アップロードを確実に完了します。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> ファイルサイズが大きい場合は、コードで **DataLakeFileClient.append** メソッドを複数回呼び出す必要があります。 代わりに、**DataLakeFileClient.uploadFromFile** メソッドの使用を検討してください。 この方法により、1 回の呼び出しでファイル全体をアップロードできます。 
>
> 次のセクションの例を参照してください。

## <a name="upload-a-large-file-to-a-directory"></a>大きなファイルをディレクトリにアップロードする

**DataLakeFileClient.append** メソッドを複数回呼び出すことなく、大きなファイルをアップロードするには、**DataLakeFileClient.uploadFromFile** メソッドを使用します。

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>ファイル ACL を管理する

この例では、`upload-file.txt` という名前のファイルの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Store Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

まず、ダウンロードするファイルを表す **DataLakeFileClient** インスタンスを作成します。 **DataLakeFileClient.read** メソッドを使用してファイルを読み取ります。 任意の .NET ファイル処理 API を使用して、ストリームからファイルにバイトを保存します。 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

この例では、`my-directory` という名前のディレクトリにある各ファイルの名前を出力します。

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>関連項目

* [API リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [パッケージ (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)