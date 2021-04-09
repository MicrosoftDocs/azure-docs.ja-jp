---
title: クイック スタート:Azure Blob Storage ライブラリ v12 - JavaScript
description: このクイックスタートでは、JavaScript 用 Azure Blob Storage クライアント ライブラリ バージョン 12 を使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 0883fd1546ac3b55ec4211e5048569556858de29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179920"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>クイック スタート:Node.js の JavaScript v12 SDK を使用して BLOB を管理する

このクイックスタートでは、Node.js を使用して BLOB を管理する方法について説明します。 BLOB は、大量のテキストやバイナリ データ (画像、ドキュメント、ストリーミング メディア、アーカイブ データなど) を保持できるオブジェクトです。 ここでは、BLOB のアップロード、ダウンロード、一覧表示のほか、コンテナーの作成と削除を行います。

その他のリソース:

* [API リファレンス ドキュメント](/javascript/api/@azure/storage-blob)
* [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-blob)
* [サンプル](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Storage のアカウント [ストレージ アカウントの作成](../common/storage-account-create.md)。
- [Node.js](https://nodejs.org/en/download/)。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>設定

このセクションでは、JavaScript 用 Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

*blob-quickstart-v12* という名前の JavaScript アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、プロジェクト用に新しいディレクトリを作成します。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 新しく作成された *blob-quickstart-v12* ディレクトリに切り替えます。

    ```console
    cd blob-quickstart-v12
    ```

1. *package.json* という名前の新しいテキスト ファイルを作成します。 このファイルには、Node.js プロジェクトが定義されます。 このファイルを *blob-quickstart-v12* ディレクトリに保存します。 ファイルのコンテンツを次に示します。

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    必要に応じて、`author` フィールドにご自身の名前を入力できます。

### <a name="install-the-package"></a>パッケージをインストールする

まだ *blob-quickstart-v12* ディレクトリにいる間に、`npm install` コマンドを使用して、JavaScript パッケージ用 Azure Blob Storage クライアント ライブラリをインストールします。 このコマンドでは、*package.json* ファイルを読み取り、JavaScript パッケージ用 Azure Blob Storage クライアント ライブラリ v12 と、依存しているすべてのライブラリをインストールします。

```console
npm install
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで別の新しいテキスト ファイルを開く
1. `require` の呼び出しを追加して Azure および Node.js モジュールを読み込む
1. 基本的な例外処理を含め、プログラムの構造を作成します

    コードは次のとおりです。

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. *blob-quickstart-v12* ディレクトリに新しいファイルを *blob-quickstart-v12.js* として保存します。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Blob Storage は、大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。 Blob Storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のコンテナー
* コンテナー内の BLOB

次の図に、これらのリソースの関係を示します。

![Blob Storage のアーキテクチャ図](./media/storage-blobs-introduction/blob1.png)

これらのリソースとやり取りするには、以下の JavaScript クラスを使用します。

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient):`BlobServiceClient` クラスを使用して、Azure Storage リソースと BLOB コンテナーを操作できます。
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient):`ContainerClient` クラスを使用して、Azure Storage コンテナーとその BLOB を操作できます。
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient):`BlobClient` クラスを使用して、Azure Storage BLOB を操作できます。

## <a name="code-examples"></a>コード例

これらのコード例のスニペットでは、JavaScript 用 Azure Blob Storage クライアント ライブラリを使用して以下を実行する方法を示します。

* [接続文字列を取得する](#get-the-connection-string)
* [コンテナーの作成](#create-a-container)
* [コンテナーに BLOB をアップロードする](#upload-blobs-to-a-container)
* [コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-container)
* [BLOB をダウンロードする](#download-blobs)
* [コンテナーの削除](#delete-a-container)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

次のコードを `main` 関数内に追加します。

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>コンテナーを作成する

新しいコンテナーの名前を決定します。 次のコードでは、確実に一意になるように、コンテナー名に UUID 値を追加します。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) メソッドを呼び出して、[BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) クラスのインスタンスを作成します。 次に、[getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) メソッドを呼び出して、コンテナーへの参照を取得します。 最後に、[create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) を呼び出して、ストレージ アカウントにコンテナーを実際に作成します。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>コンテナーに BLOB をアップロードする

次のコード スニペット:

1. BLOB にアップロードするテキスト文字列を作成します。
1. 「[コンテナーを作成する](#create-a-container)」セクションからの [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) 上で [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) メソッドを呼び出すことで、[BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) オブジェクトへの参照を取得します。
1. [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) メソッドを呼び出して、テキスト文字列データを BLOB にアップロードします。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) メソッドを呼び出して、コンテナー内の BLOB を一覧表示します。 この場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) メソッドを呼び出して、以前に作成した BLOB をダウンロードします。 コード例には、Node.js の読み取り可能なストリームを 1 つの文字列に読み取るために使用される `streamToString` というヘルパー関数が含まれています。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

`main` 関数の "*後*" に、このヘルパー関数を追加します。

```javascript
// A helper function used to read a Node.js readable stream into a string
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
```

### <a name="delete-a-container"></a>コンテナーを削除する

次のコードでは、[delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) メソッドを使用して、コンテナー全体を削除することで、アプリによって作成されたリソースをクリーンアップします。 必要に応じてローカル ファイルを削除することもできます。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>コードの実行

このアプリでは、テキスト文字列が作成され、BLOB ストレージにアップロードされます。 例では、コンテナー内の BLOB を一覧表示し、BLOB をダウンロードして、ダウンロードしたデータを表示します。

コンソール プロンプトから、*blob-quickstart-v12.js* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node blob-quickstart-v12.js
```

アプリの出力は、次の例のようになります。

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

デバッガーでコードをステップ実行し、プロセス全体について [Azure portal](https://portal.azure.com) 上でチェックします。 コンテナーが作成されていることを確認するために、チェックを行います。 コンテナー内で BLOB を開いて、コンテンツを表示することができます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、JavaScript を使用して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明しました。

チュートリアル、サンプル、クイックスタートなどのドキュメントについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [JavaScript 開発者センターの Azure](/azure/developer/javascript/)

* Azure BLOB ストレージを使用する Web アプリをデプロイする方法については、「[チュートリアル: Azure Storage を使用してクラウドに画像データをアップロードする](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)」を参照してください。
* Blob Storage のサンプル アプリの詳細については、[Azure Blob Storage クライアント ライブラリ v12 JavaScript サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples) ページを参照してください。
* 詳細については、[JavaScript 用 Azure Blob Storage クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)に関するページを参照してください。
