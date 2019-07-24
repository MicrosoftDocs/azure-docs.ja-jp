---
title: Node.js v2 用のクライアント ライブラリを使用して Azure Storage 内に BLOB を作成する方法
description: オブジェクト (BLOB) ストレージ内にストレージ アカウントとコンテナーを作成します。 その後、Node.js v2 用の Azure Storage クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: tamram
ms.openlocfilehash: 182315c705360d254c3bf342cd9c64ffafa0c021
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750076"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Node.js v2 用のクライアント ライブラリを使用して BLOB をアップロード、ダウンロード、および一覧表示する方法

このハウツー ガイドでは、Node.js v2 用のクライアント ライブラリを使用して、Azure Blob Storage に対して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明します。

> [!TIP]
> Node.js 用の Azure Storage クライアント ライブラリの最新バージョンは v10 です。 可能であれば、クライアント ライブラリの最新バージョンを使用することをお勧めします。 v10 の使用を開始する場合は、[クイック スタート: JavaScrip 用の Azure Storage クライアント ライブラリ v10 を使用して BLOB のアップロード、ダウンロード、一覧表示、および削除を行う方法 (プレビュー)](storage-quickstart-blobs-nodejs-v10.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[Azure portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) で Azure ストレージ アカウントを作成します。 アカウントの作成については、「[ストレージ アカウントの作成](../common/storage-quickstart-create-account.md)」を参照してください。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)は、単純な Node.js コンソール アプリケーションです。 開始するには、次のコマンドを使用して、お使いのマシンにリポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

アプリケーションを開くには、*storage-blobs-node-quickstart* フォルダーを探して、任意のコード編集環境で開きます。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションを実行する前に、ストレージ アカウントの接続文字列を指定する必要があります。 サンプル リポジトリには、*.env.example* という名前のファイルが含まれています。 このファイル名から *.example* 拡張子を削除して、*.env* というファイル名に変更できます。 *.env* ファイル内で、*AZURE_STORAGE_CONNECTION_STRING* キーの後ろに接続文字列の値を追加します。

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

スクリプトからの出力は次のようになります。

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

このサンプル用の新しいストレージ アカウントを使用している場合は、 "*Containers*" のラベルの下にコンテナー名が表示されない可能性があります。

## <a name="understanding-the-code"></a>コードについて
最初の式は、環境変数に値を読み込むために使用されます。

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

*dotenv* モジュールは、デバッグのためにアプリをローカルで実行しているときに環境変数を読み込みます。 値は *.env* という名前のファイルで定義され、現在の実行コンテキストに読み込まれます。 運用コンテキストでは、これらの値はサーバー構成によって提供されます。このコードが、スクリプトが "運用" コンテキストで実行されていない場合にのみ実行されるのはそのためです。

```javascript
const path = require('path');
const storage = require('azure-storage');
```

各モジュールの目的は次のとおりです。 

*.env* という名前のファイルを現在の実行コンテキストに読み込みます。
- *path*: Blob Storage にアップロードするファイルの絶対ファイル パスを決定するのに必要
- *azure-storage*: Node.js 用の [Azure Storage クライアント ライブラリ](https://docs.microsoft.com/javascript/api/azure-storage) モジュール

次に、**blobService** 変数が Azure Blob service の新しいインスタンスとして初期化されます。

```javascript
const blobService = storage.createBlobService();
```

次の実装では、*blobService* の各関数は *Promise* にラップされます。これにより、JavaScript の *async* 関数と *await* 演算子にアクセスして [Azure Storage API](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) のコールバックの性質を合理化できます。 各関数に対して正常な応答が返されると、アクションに固有のメッセージと関連データを使用して Promise が解決されます。

### <a name="list-containers"></a>コンテナーの一覧表示

*listContainers* 関数は、グループ内のコンテナーのコレクションを返す [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) を呼び出します。

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

グループのサイズは、[ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest) で構成できます。 *listContainersSegmented* を呼び出すと、BLOB メタデータが [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest) インスタンスの配列として返されます。 結果は 5,000 の増分バッチ (セグメント) で返されます。 コンテナー内に 5,000 を超える BLOB がある場合は、結果に *continuationToken* の値が含まれます。 BLOB コンテナーから後続のセグメントを一覧表示するには、この継続トークンを 2 番目の引数として *listContainersSegment* に戻すことができます。

### <a name="create-a-container"></a>コンテナーを作成する

*createContainer* 関数は [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) を呼び出し、BLOB に適切なアクセス レベルを設定します。

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists** の 2 番目のパラメーター ("*オプション*") は、[publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) の値を受け取ります。 *publicAccessLevel* の値 *blob* は、特定の BLOB データがパブリックに公開されることを示します。 この設定は、コンテナーの内容を一覧表示する権限を付与する *container* レベル アクセスとは対照的です。

**createContainerIfNotExists** を使用すると、コンテナーが既に存在する場合でも、アプリケーションはエラーを返すことなく *createContainer* コマンドを複数回実行できます。 運用環境では、アプリケーション全体で同じコンテナーが使用されるため、多くの場合 **createContainerIfNotExists** は 1 回だけ呼び出されます。 このような場合は、ポータルや Azure CLI を使用して、事前にコンテナーを作成できます。

### <a name="upload-text"></a>テキストをアップロードする

*uploadString* 関数は、[createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) を呼び出して BLOB コンテナーに任意の文字列を書き込みます (または上書きします)。

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>ローカル ファイルをアップロードする

*uploadLocalFile* 関数は、[createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) を使用して、ファイル システムから Blob Storage にファイルをアップロードして書き込みます (または上書きします)。 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
BLOB へのコンテンツのアップロードに使用できる他のアプローチとしては、[text](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) や [streams](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--) の操作があります。 ファイルが Blob Storage にアップロードされたことを確認するには、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、自分のアカウントでデータを表示します。

### <a name="list-the-blobs"></a>BLOB を一覧表示する

*listBlobs* 関数は、[listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) メソッドを呼び出して、コンテナー内の BLOB メタデータの一覧を返します。 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

*listBlobsSegmented* を呼び出すと、[BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest) インスタンスの配列として BLOB メタデータが返されます。 結果は 5,000 の増分バッチ (セグメント) で返されます。 コンテナー内に 5,000 を超える BLOB がある場合は、結果に **continuationToken** の値が含まれます。 BLOB コンテナーから後続のセグメントを一覧表示するには、この継続トークンを 2 番目の引数として **listBlobSegmented** に戻します。

### <a name="download-a-blob"></a>BLOB をダウンロードする

*downloadBlob* 関数は、[getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) を使用して、BLOB のコンテンツを指定された絶対ファイル パスにダウンロードします。

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
ここでに示されている実装では、ソースを変更し、BLOB のコンテンツを文字列として返します。 [ローカル ファイル](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)に直接ダウンロードするだけでなく、BLOB を[ストリーム](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)としてダウンロードすることもできます。

### <a name="delete-a-blob"></a>BLOB を削除する

*deleteBlob* 関数は、[deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--) 関数を呼び出します。 名前が示すとおり、この関数は BLOB が既に削除されていてもエラーを返しません。

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>コンテナーを削除する

コンテナーは、Blob service の *deleteContainer* メソッドを呼び出し、コンテナー名を渡すことによって削除されます。

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>コードの呼び出し

JavaScript の *async/await* 構文をサポートするために、すべての呼び出し元コードが *execute* という名前の関数内にラップされます。 その後、execute が呼び出され、promise として処理されます。

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
次のコードはすべて、execute 関数内の `// commands` コメントが記載された場所で実行されます。

最初に、名前やサンプル コンテンツを割り当てたり、Blob Storage にアップロードするローカル ファイルを指したりする関連する変数が宣言されます。

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

ストレージ アカウント内のコンテナーを一覧表示するために、listContainers 関数が呼び出され、返されたコンテナーの一覧が出力ウィンドウにログ記録されます。

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

コンテナーの一覧が使用可能になったら、配列の *findIndex* メソッドを使用して、作成するコンテナーが既に存在するかどうかを確認できます。 コンテナーが存在しない場合は、そのコンテナーが作成されます。

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
次に、文字列とローカル ファイルが Blob Storage にアップロードされます。

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
BLOB を一覧表示するプロセスは、コンテナーの一覧表示と同じです。 *listBlobs* を呼び出すと、コンテナー内の BLOB の配列が返され、出力ウィンドウにログ記録されます。

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

BLOB をダウンロードするために、応答がキャプチャされ、BLOB の値にアクセスするために使用されます。 その応答から、readableStreamBody が文字列に変換され、出力ウィンドウにログ記録されます。

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

最後に、BLOB とコンテナーがストレージ アカウントから削除されます。

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
ストレージ アカウントに書き込まれたデータはすべて、サンプル コードの最後で自動的に削除されます。 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>BLOB を使用する Node.js アプリケーションを開発するためのリソース

Blob Storage を使用する Node.js 開発については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- GitHub で Azure Storage 用の [Node.js クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-node)を確認し、インストールします。

### <a name="client-library-reference-and-samples"></a>クライアント ライブラリ リファレンスとサンプル

- Node.js クライアント ライブラリの詳細については、[Node.js API リファレンス](https://docs.microsoft.com/javascript/api/overview/azure/storage)を参照してください。
- Node.js クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)を確認します。

## <a name="next-steps"></a>次の手順

この記事では、Node.js を使ってローカル ディスクと Azure Blob Storage の間でファイルをアップロードする方法について説明しました。 Blob Storage の操作の詳細を学習するには、GitHub リポジトリに進んでください。

> [!div class="nextstepaction"]
> [Node.js 用の Microsoft Azure Storage SDK およびブラウザー用の JavaScript](https://github.com/Azure/azure-storage-node)
