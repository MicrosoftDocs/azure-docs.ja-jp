---
title: Azure Data Lake Storage Gen2 でファイルと ACL に JavaScript を使用する
description: JavaScript 用の Azure Storage Data Lake クライアント ライブラリを使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、ディレクトリとファイル、およびディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-javascript
ms.openlocfilehash: 648a47b451e8857c1cd84c2bcecdaae7fcaa065d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430537"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>JavaScript を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、階層型名前空間 (HNS) が有効になっているストレージ アカウントで、JavaScript を使用してディレクトリ、ファイル、アクセス許可を作成および管理する方法を示します。 

[パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。
> * このパッケージを Node.js アプリケーションで使用する予定の場合、Node.js 8.0.0 以降が必要です。

## <a name="set-up-your-project"></a>プロジェクトの設定

ターミナル ウィンドウを開いてから次のコマンドを入力して、JavaScript 用の Data Lake クライアント ライブラリをインストールします。

```javascript
npm install @azure/storage-file-datalake
```

このステートメントをコード ファイルの先頭に配置して、`storage-file-datalake` パッケージをインポートします。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>アカウントに接続する 

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> この承認方法は、Node.js アプリケーションに対してのみ機能します。 ブラウザーでコードを実行する予定の場合、Azure Active Directory (AD) を使用して承認できます。 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

[JS 用 Azure ID クライアント ライブラリ](https://www.npmjs.com/package/@azure/identity) を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> その他の例については、[JS 用 Azure ID クライアント ライブラリ](https://www.npmjs.com/package/@azure/identity)のドキュメントを参照してください。

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 これは、**FileSystemClient** インスタンスを取得し、次に **FileSystemClient.Create** メソッドを呼び出すことで作成できます。

この例では、`my-file-system` という名前のコンテナーを作成します。 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>ディレクトリを作成する

ディレクトリ参照は、**DirectoryClient** インスタンスを取得し、次に **DirectoryClient.create** メソッドを呼び出すことによって作成します。

この例では、`my-directory` という名前のディレクトリをコンテナーに追加します。 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

**DirectoryClient.rename** メソッドを呼び出して、ディレクトリの名前変更または移動を行います。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-directory-renamed` に変更します。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

この例では、`my-directory-renamed` という名前のディレクトリを `my-directory-2` という名前のディレクトリのサブディレクトリに移動します。 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

**DirectoryClient.delete** メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>ディレクトリ ACL を管理する

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Store Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

また、コンテナのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**DataLakeFileSystemClient.getDirectoryClient** メソッドに空の文字列 (`/`) を渡します。

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

まず、ファイルを読み取ります。 この例では、Node.js `fs` モジュールを使用しています。 次に、**FileClient** インスタンスを作成してから **FileClient.create** メソッドを呼び出して、ターゲット ディレクトリ内にファイル参照を作成します。 **FileClient.append** メソッドを呼び出して、ファイルをアップロードします。 **FileClient.flush** メソッドを呼び出して、アップロードの完了を確認します。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>ファイル ACL を管理する

この例では、`upload-file.txt` という名前のファイルの ACL を取得して設定します。 この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーには読み取りアクセスを付与します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、アクセスを承認するためにアプリケーションで使用されているセキュリティ プリンシパルに、[ストレージ BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Store Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

まず、ダウンロードするファイルを表す **FileSystemClient** インスタンスを作成します。 **FileSystemClient.read** メソッドを使用してファイルを読み取ります。 次に、ファイルを書き込みます。 この例では、Node.js `fs` モジュールを使用してそれを行います。 

> [!NOTE]
> このファイル ダウンロード方法は、Node.js アプリケーションに対してのみ機能します。 ブラウザーでコードを実行する予定の場合は、[JavaScript 用の Azure Storage ファイル Data Lake クライアント ライブラリ](https://www.npmjs.com/package/@azure/storage-file-datalake)の Readme ファイルで、ブラウザーでこれを行う方法の例を参照してください。 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

この例では、`my-directory` という名前のディレクトリにある、それぞれのディレクトリおよびファイルの名前を出力します。

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>関連項目

* [パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)