---
title: クイック スタート:JavaScript 用 Azure Blob Storage クライアント ライブラリ v10
description: JavaScript 用 Azure Storage クライアント ライブラリ v10 を使用して Node.js で BLOB とコンテナーを作成、アップロード、削除します
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 2371c789b9e4a9fc70f4207fd8a634e419c97912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599461"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>クイック スタート:Node.js で JavaScript v10 SDK を使用して BLOB を管理する

このクイックスタートでは、Node.js を使用して BLOB を管理する方法について説明します。 BLOB は、大量のテキストやバイナリ データ (画像、ドキュメント、ストリーミング メディア、アーカイブ データなど) を保持できるオブジェクトです。 ここでは、BLOB のアップロード、ダウンロード、一覧表示、削除のほか、コンテナーの管理を行います。

> [!NOTE]
> このクイックスタートでは、Azure Blob Storage クライアント ライブラリのレガシー バージョンを使用します。 最新バージョンで始めるには、「[クイックスタート: Node.js の JavaScript v12 SDK を使用して BLOB を管理する](storage-quickstart-blobs-nodejs.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Storage のアカウント [ストレージ アカウントの作成](../common/storage-account-create.md)。
- [Node.js](https://nodejs.org/en/download/)。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイック スタートで使用する[サンプル アプリケーション](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git)は、シンプルな Node.js コンソール アプリケーションです。 開始するには、次のコマンドを使用して、お使いのマシンにリポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

次に、アプリケーション用のフォルダーを変更します。

```bash
cd azure-storage-js-v10-quickstart
```

ここで、好みのコード編集環境でフォルダーを開きます。

## <a name="configure-your-storage-credentials"></a>ストレージの資格情報を構成する

アプリケーションを実行する前に、ストレージ アカウントのセキュリティ資格情報を指定する必要があります。 サンプル リポジトリには、 *.env.example* という名前のファイルが含まれています。 このファイル名から *.example* 拡張子を削除して、 *.env* というファイル名に変更します。 *.env* ファイルで、*AZURE_STORAGE_ACCOUNT_NAME* キーの後にアカウント名の値を追加し、*AZURE_STORAGE_ACCOUNT_ACCESS_KEY* キーの後にアクセス キーの値を追加します。

## <a name="install-required-packages"></a>必要なパッケージをインストールする

アプリケーション ディレクトリで *npm install* を実行して、アプリケーションに必要なパッケージをインストールします。

```bash
npm install
```

## <a name="run-the-sample"></a>サンプルを実行する

これで依存関係がインストールされたので、次のコマンドを発行することによってサンプルを実行できます。

```bash
npm start
```

アプリからの出力は次の例のようになります。

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

このクイックスタートで新しいストレージ アカウントを使用している場合は、"*Containers:* " のラベルの下に *demo* コンテナーのみが表示される可能性があります。

## <a name="understanding-the-code"></a>コードについて

サンプルでは、最初に Azure BLOB ストレージの名前空間からクラスと関数をインポートします。 インポートされる各アイテムについては、サンプルでそれらが使用されるコンテキストで説明します。

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

資格情報は、適切なコンテキストに基づいて環境変数から読み取られます。

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

*dotenv* モジュールは、デバッグのためにアプリをローカルで実行しているときに環境変数を読み込みます。 値は *.env* という名前のファイルで定義され、現在の実行コンテキストに読み込まれます。 運用環境では、これらの値はサーバー構成によって提供されます。このコードが、スクリプトが "運用" 環境で実行されて "*いない*" 場合にのみ実行されるのはそのためです。

モジュールの次のブロックは、ファイル システムとのインターフェイスのためにインポートされます。

```javascript
const fs = require('fs');
const path = require('path');
```

各モジュールの目的は次のとおりです。 

- *fs* は、ファイル システムを操作するために使用されるネイティブの Node.js モジュールです

- *path* は、ファイルの絶対パスを決定するために必要であり、BLOB ストレージにファイルをアップロードするときに使用されます

次に、環境変数の値が読み取られて、定数に格納されます。

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
次の一連の定数は、アップロード操作中にファイル サイズの計算の意図を明らかにするのに役立ちます。

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

API によって行われた要求は、一定の時間後にタイムアウトに設定される場合があります。 [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) クラスは要求がタイムアウトする方法を管理し、このサンプルで使用されるタイムアウトを定義するには次の定数が使用されます。

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>コードの呼び出し

JavaScript の *async/await* 構文をサポートするために、すべての呼び出し元コードが *execute* という名前の関数内にラップされます。 その後、*execute* が呼び出され、promise として処理されます。

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

次のコードはすべて、execute 関数内の `// commands...` コメントが記載された場所で実行されます。

最初に、名前やサンプル コンテンツを割り当てたり、Blob Storage にアップロードするローカル ファイルを指したりする関連する変数が宣言されます。

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

アカウントの資格情報を使用してパイプラインが作成され、パイプラインは REST API への要求の送信方法を管理します。 パイプラインはスレッドセーフであり、再試行ポリシー、ログ、HTTP 応答の逆シリアル化ルールなどのロジックが指定されています。

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

このコード ブロックでは次のクラスが使用されます。

- [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) クラスでは、要求パイプラインに提供するためにストレージ アカウントの資格情報がラップされます。

- [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) クラスでは、新しいパイプラインが作成されます。

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) では、REST API で使用される URL がモデル化されます。 このクラスのインスタンスを使用すると、コンテナーの一覧の表示や、コンテナーの URL を生成するためのコンテキスト情報の提供といったアクションを実行できます。

*ServiceURL* のインスタンスを [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) および [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) のインスタンスと併用して、ストレージ アカウントのコンテナーと BLOB を管理します。

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

*containerURL* 変数と *blockBlobURL* 変数は、ストレージ アカウントを操作するためにサンプル全体で再利用されます。 

この時点では、コンテナーはストレージ アカウントに存在しません。 *ContainerURL* のインスタンスは操作できる URL を表します。 このインスタンスを使用して、コンテナーを作成および削除できます。 このコンテナーの場所は、次のような場所に相当します。

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

*blockBlobURL* は個々の BLOB の管理に使用され、BLOB の内容をアップロード、ダウンロード、削除することができます。 ここで表される URL は次のような場所です。

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

コンテナーと同様、ブロック BLOB はまだ存在しません。 *blockBlobURL* 変数は、後で内容をアップロードすることで BLOB を作成するために使用されます。

### <a name="using-the-aborter-class"></a>Aborter クラスを使用する

API によって行われた要求は、一定の時間後にタイムアウトに設定される場合があります。 *Aborter* クラスは、要求のタイムアウト方法を管理するために使用されます。次のコードによって作成されるコンテキストでは、一連の要求は 30 分だけ実行できます。

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Aborter を使用すると、次のようにして要求を制御できます。

- 要求のバッチに対して与えられる時間数を指定します
- バッチ内で個々の要求が実行する必要のある時間を指定します
- ユーザーが要求をキャンセルできるようにします
- *Aborter.none* 静的メンバーを使用して、すべての要求がタイムアウトするのをまとめて停止します

### <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、*ContainerURL* の *create* メソッドを使用します。

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

コンテナーの名前は *ContainerURL.fromServiceURL(serviceURL, containerName)* を呼び出すときに定義するので、コンテナーを作成するために必要なのは *create* メソッドを呼び出すことだけです。

### <a name="show-container-names"></a>コンテナー名を表示する

アカウントには膨大な数のコンテナーを格納できます。 次のコードでは、セグメント化された形式でコンテナーのリストを取得する方法を示します。これにより、多数のコンテナーを順番に処理することができます。 *showContainerNames* 関数には、*ServiceURL* と *Aborter* のインスタンスを渡します。

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

*showContainerNames* 関数は、*listContainersSegment* メソッドを使用して、ストレージ アカウントにコンテナー名のバッチを要求します。

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

応答が返されたら、*containerItems* を反復処理してコンソールに名前を表示します。 

### <a name="upload-text"></a>テキストをアップロードする

テキストを BLOB にアップロードするには、*upload* メソッドを使用します。

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

ここでは、テキストとその長さをメソッドに渡します。

### <a name="upload-a-local-file"></a>ローカル ファイルをアップロードする

ローカル ファイルをコンテナーにアップロードするには、コンテナーの URL とファイルへのパスが必要です。

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

*uploadLocalFile* 関数では *uploadFileToBlockBlob* 関数が呼び出されて、ファイルのパスと、ブロック BLOB の格納先のインスタンスが、引数として渡されます。

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>ストリームをアップロードする

ストリームのアップロードもサポートされています。 このサンプルでは、ローカル ファイルをストリームとして開き、アップロード メソッドに渡します。

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

*uploadStream* 関数は *uploadStreamToBlockBlob* を呼び出して、ストリームをストレージ コンテナーにアップロードします。

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

アップロード中に、*uploadStreamToBlockBlob* では、再試行が必要な場合のために、ストリームからのデータをキャッシュするバッファーを割り当てます。 各バッファーによって個別のアップロード要求が作成されるので、*maxBuffers* の値では使用されるバッファーの最大数を指定します。 理想的には、バッファーを増やすと速くなりますが、メモリ使用率が高くなります。 バッファーの数が増えて、クライアントではなくネットワークやディスクがボトルネックになると、アップロードの速度は横ばいになります。

### <a name="show-blob-names"></a>BLOB の名前を表示する

アカウントに多くのコンテナーが含まれるのと同様に、各コンテナーには膨大な数の BLOB が含まれる可能性があります。 コンテナー内の各 BLOB には、*ContainerURL* クラスのインスタンスを使用してアクセスできます。

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

*showBlobNames* 関数は *listBlobFlatSegment* を呼び出して、コンテナーに BLOB のバッチを要求します。

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>BLOB をダウンロードする

BLOB が作成されたら、*download* メソッドを使用して内容をダウンロードできます。

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

応答はストリームとして返されます。 この例では、次の *streamToString* ヘルパー関数を使用してストリームを文字列に変換します。

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>BLOB を削除する

*BlockBlobURL* インスタンスの *delete* メソッドは、コンテナーから BLOB を削除します。

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>コンテナーを削除する

*ContainerURL* インスタンスの *delete* メソッドは、ストレージ アカウントからコンテナーを削除します。

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ストレージ アカウントに書き込まれたデータはすべて、サンプル コードの最後で自動的に削除されます。 

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Node.js を使用して Azure BLOB ストレージ内の BLOB とコンテナーを管理する方法を示しました。 この SDK の使用方法をさらに詳しく学習するには、GitHub リポジトリを参照してください。

> [!div class="nextstepaction"]
> [Azure Storage v10 SDK for JavaScript リポジトリ](https://github.com/Azure/azure-storage-js)
> [Azure Storage JavaScript API リファレンス](/javascript/api/overview/azure/storage-overview)
