---
title: Azure Data Lake Storage Gen2 で JavaScript (Node.js) を使用してデータを管理する
description: JavaScript 用 Azure Storage Data Lake クライアント ライブラリを使用して、階層型名前空間が有効になっているストレージ アカウントでディレクトリとファイルを管理します。
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 678af3e2fb4111593ece0cc2cdf3811cf0e793a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774764"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Node.js の JavaScript SDK を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する

この記事では、階層型名前空間が有効になっているストレージ アカウントで、Node.js を使用してディレクトリとファイルを作成および管理する方法を示します。

ディレクトリとファイルのアクセス制御リスト (ACL) を取得、設定、更新する方法については、[Azure Data Lake Storage Gen2 での Node.js の JavaScript SDK を使用した ACL の管理](data-lake-storage-acl-javascript.md)に関する記事を参照してください。

[パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- このパッケージを Node.js アプリケーションで使用する予定の場合、Node.js 8.0.0 以降が必要です。

## <a name="set-up-your-project"></a>プロジェクトの設定

ターミナル ウィンドウを開いてから次のコマンドを入力して、JavaScript 用の Data Lake クライアント ライブラリをインストールします。

```javascript
npm install @azure/storage-file-datalake
```

このステートメントをコード ファイルの先頭に配置して、`storage-file-datalake` パッケージをインポートします。 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
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
> この承認方法は、Node.js アプリケーションに対してのみ機能します。 ブラウザーでコードを実行する予定の場合、Azure Active Directory (Azure AD) を使用して承認できます。

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用して接続する

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

- [パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [フィードバックを送る](https://github.com/Azure/azure-sdk-for-java/issues)