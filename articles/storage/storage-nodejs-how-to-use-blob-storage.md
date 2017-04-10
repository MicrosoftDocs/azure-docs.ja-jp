---
title: "Node.js から Blob Storage を使用する方法 | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
services: storage
documentationcenter: nodejs
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: b9041713f3d084fde77a34ef7956a2c59312245e
ms.openlocfilehash: d48f82d8fea89b45af14dd2154d69889c95a5e2f


---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Node.js から BLOB ストレージを使用する方法
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>概要
この記事では、BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Node.js API を使用して記述されています。 紹介するシナリオには、アップロード、一覧表示、ダウンロード、および blob を削除する方法が含まれます。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成
Node.js アプリケーションを作成する手順については、「[Azure App Service での Node.js Web アプリの作成]」、「[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ]」 (Windows PowerShell の使用)、または「[WebMatrix を使用した Node.js Web アプリの構築と Azure へのデプロイ]」をご覧ください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する
1. **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使って、サンプル アプリケーションを作成したフォルダーに移動します。
2. コマンド ウィンドウに「 **npm install azure-storage** 」と入力します。 コマンドの出力は次のコード例に似ています。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. 手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。 このフォルダーで **azure-storage** パッケージを検索します。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### <a name="import-the-package"></a>パッケージをインポートする
メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage の接続文字列の設定
Azure モジュールは、Azure ストレージ アカウントに接続するために必要な情報として、環境変数 `AZURE_STORAGE_ACCOUNT` と `AZURE_STORAGE_ACCESS_KEY`、または `AZURE_STORAGE_CONNECTION_STRING` を読み取ります。 これらの環境変数が設定されていない場合は、 **createBlobService**を呼び出すときにアカウント情報を指定する必要があります。

Azure Web アプリの [Azure Portal](https://portal.azure.com) で環境変数を設定する例については、「[Azure Table サービスを使用する Node.js Web アプリ]」をご覧ください。

## <a name="create-a-container"></a>コンテナーを作成する
**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。 次のコードでは、 **BlobService** オブジェクトを作成します。 **server.js**ファイルの先頭付近に次の内容を追加します。

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> **createBlobServiceAnonymous** を使用してホスト アドレスを指定すると、BLOB に匿名でアクセスできます。 たとえば、 `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`を使用します。
>
>

[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

新しいコンテナーを作成するには、 **createContainerIfNotExists**を使用します。 次のコード例では、'mycontainer' という名前の新しいコンテナーを作成します。

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

コンテナーが新規に作成された場合、 `result.created` は true です。 コンテナーが既に存在する場合は、 `result.created` は false になります。 `response` には、コンテナーの ETag 情報を含む、操作に関する情報が含まれます。

### <a name="container-security"></a>コンテナーのセキュリティ
既定では、新しいコンテナーはプライベートであり、匿名でアクセスすることはできません。 コンテナーを公開して匿名でアクセスできるようにするには、コンテナーのアクセス レベルを **blob** または **container** に設定します。

* **blob** - BLOB の内容およびこのコンテナー内のメタデータへの匿名の読み取りは許可されますが、1 つのコンテナー内の全 BLOB のリストなど、コンテナーのメタデータに対する匿名読み取りは許可されません。
* **container** - コンテナーのメタデータに加えて、BLOB の内容とメタデータに対する匿名読み取りが許可されます。

次のコード例では、アクセス レベルを **blob**に設定する方法を示します。

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

代わりに、 **setContainerAcl** を使用してアクセス レベルを指定することによって、コンテナーのアクセス レベルを変更できます。 次のコード例では、アクセス レベルを container に変更します。

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

結果には、コンテナーの現在の **ETag** 情報を含む、操作に関する情報が含まれます。

### <a name="filters"></a>フィルター
オプションのフィルター操作は、**BlobService** を使って行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

```nodejs
function handle (requestOptions, next)
```

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

```nodejs
function (returnObject, finalCallback, next)
```

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する&2; つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **BlobService** オブジェクトを作成します。

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
BLOB には、ブロック BLOB、ページ BLOB、追加 BLOB の&3; 種類があります。 ブロック BLOB を使用すると、大規模なデータをより効率的にアップロードできます。 追加 BLOB は、追加操作用に最適化されています。 ページ BLOB は、読み取りと書き込み操作用に最適化されています。 詳細については、「 [ブロック BLOB、追加 BLOB、ページ BLOB について](http://msdn.microsoft.com/library/azure/ee691964.aspx)」を参照してください。

### <a name="block-blobs"></a>ブロック blob
データをブロック blob にアップロードするには、以下のメソッドを使用します。

* **createBlockBlobFromLocalFile** - 新しいブロック blob を作成してファイルの内容をアップロードします。
* **createBlockBlobFromStream** - 新しいブロック blob を作成してストリームの内容をアップロードします。
* **createBlockBlobFromText** - 新しいブロック blob を作成して文字列の内容をアップロードします。
* **createWriteStreamToBlockBlob** - ブロック blob への書き込みストリームを提供します。

次のコード例では、**test.txt** ファイルの内容を **myblob** にアップロードします。

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

これらのメソッドによって返される `result` には、BLOB の **ETag** など、操作に関する情報が含まれます。

### <a name="append-blobs"></a>追加 BLOB
データを新しい追加 BLOB にアップロードするには、以下のメソッドを使用します。

* **createAppendBlobFromLocalFile** - 新しい追加 BLOB を作成し、ファイルの内容をアップロードします。
* **createAppendBlobFromStream** - 新しい追加 BLOB を作成し、ストリームの内容をアップロードします。
* **createAppendBlobFromText** - 新しい追加 BLOB を作成し、文字列の内容をアップロードします。
* **createWriteStreamToNewAppendBlob** - 新しい追加 BLOB を作成してから、その BLOB に書き込むストリームを提供します。

次のコード例では、**test.txt** ファイルの内容を **myappendblob** にアップロードします。

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

既存の追加 BLOB にブロックを追加するには、以下のメソッドを使用します。

* **appendFromLocalFile** - 既存の追加 BLOB にファイルの内容を追加します。
* **appendFromStream** - 既存の追加 BLOB にストリームの内容を追加します。
* **appendFromText** - 既存の追加 BLOB に文字列の内容を追加します。
* **appendBlockFromStream** - 既存の追加 BLOB にストリームの内容を追加します。
* **appendBlockFromText** - 既存の追加 BLOB に文字列の内容を追加します。

> [!NOTE]
> appendFromXXX API では、不要なサーバー呼び出しを回避するために、Fail Fast に対するクライアント側検証が実行されますが、 appendBlockFromXXX では実行されません。
>
>

次のコード例では、**test.txt** ファイルの内容を **myappendblob** にアップロードします。

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>ページ blob
データをページ blob にアップロードするには、以下のメソッドを使用します。

* **createPageBlob** - 指定の長さの新しいページ blob を作成します。
* **createPageBlobFromLocalFile** - 新しいページ blob を作成してファイルの内容をアップロードします。
* **createPageBlobFromStream** - 新しいページ blob を作成してストリームの内容をアップロードします。
* **createWriteStreamToExistingPageBlob** - 既存のページ blob への書き込みストリームを提供します。
* **createWriteStreamToNewPageBlob** - 新しいページ BLOB を作成してから、その BLOB に書き込むストリームを提供します。

次のコード例では、**test.txt** ファイルの内容を **mypageblob** にアップロードします。

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> ページ blob は、512 バイトの "ページ" で構成されています。 512 の倍数でないサイズのデータをアップロードするとエラーが発生します。
>
>

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、 **listBlobsSegmented** メソッドを使用します。 特定のプレフィックスと共に BLOB を返す必要がある場合は、 **listBlobsSegmentedWithPrefix**を使用します。

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

`result` には `entries` コレクションが含まれます。これは、各 BLOB を記述するオブジェクトの配列です。 すべての BLOB を返すことができない場合は、`result` は、`continuationToken` も提供します。これは、追加のエントリを取得するための&2; つ目のパラメーターとして使用できます。

## <a name="download-blobs"></a>BLOB をダウンロードする
BLOB からデータをダウンロードするには、以下のメソッドを使用します。

* **getBlobToLocalFile** - BLOB の内容をファイルに書き込みます。
* **getBlobToStream** - BLOB の内容をストリームに書き込みます。
* **getBlobToText** - BLOB の内容を文字列に書き込みます。
* **createReadStream** - BLOB から読み取るためのストリームを提供します。

次のコード例は、**getBlobToStream** を使って **myblob** BLOB の内容をダウンロードし、ストリームを使って **output.txt** ファイルに格納する方法を示しています。

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

`result` には、 **ETag** 情報など、BLOB に関する情報が含まれます。

## <a name="delete-a-blob"></a>BLOB を削除する
最後に、BLOB を削除するには、 **deleteBlob**を呼び出します。 次のコード例では、 **myblob**という名前の BLOB を削除します。

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>同時アクセス
複数のクライアントまたは複数のプロセス インスタンスからの BLOB への同時アクセスをサポートするには、**ETag** または**占有**を使います。

* **Etag** - BLOB またはコンテナーが別のプロセスによって変更されていることを検出する手段を提供します。
* **占有** - 一定期間、BLOB に対する排他的で更新可能な書き込みアクセスまたは削除アクセスを取得する手段を提供します。

### <a name="etag"></a>ETag
ETag は、複数のクライアントまたはインスタンスからのブロック BLOB またはページ BLOB への同時書き込みを許可する必要がある場合に使用してください。 ETag を使用すると、最初の読み取りまたは作成以降にコンテナーまたは BLOB が変更されているかどうかを確認できることから、別のクライアントまたはプロセスによってコミットされた変更の上書きを回避できます。

ETag の条件は、オプションの `options.accessConditions` パラメーターを使用して設定できます。 次のコード例では、BLOB が既に存在し、`etagToMatch` によって ETag 値が含まれる場合に、**test.txt** ファイルのみをアップロードします。

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

ETag を使用している場合、一般的なパターンは次のとおりです。

1. 作成、一覧表示、取得の操作の結果として ETag を取得する。
2. ETag 値が変更されていないことを確認するアクションを実行する。

値が変更されていた場合は、ETag 値を取得して以降、別のクライアントまたはインスタンスがこの BLOB またはコンテナーを変更したことを示しています。

### <a name="lease"></a>占有
占有を取得する BLOB またはコンテナーを指定して **acquireLease** メソッドを使用すると、新しい占有を取得できます。 たとえば、以下のコードでは **myblob**での占有を取得します。

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

**myblob** に対するその後の操作では、`options.leaseId` パラメーターを指定する必要があります。 占有 ID は、**acquireLease** から `result.id` として返されます。

> [!NOTE]
> 既定では、占有期間は無限です。 `options.leaseDuration` パラメーターを指定することで、有限の期間 (15 ～ 60 秒) を指定できます。
>
>

占有を削除するには、 **releaseLease**を使用します。 占有を中断するものの、元の期間が期限切れになるまでは新しい占有が取得されないようにするには、 **breakLease**を使用します。

## <a name="work-with-shared-access-signatures"></a>共有アクセス署名を操作する
共有アクセス署名 (SAS) は、ストレージ アカウント名やキーを指定せずに BLOB やコンテナーへのきめ細やかで安全なアクセスを提供する方法です。 SAS は、モバイル アプリからの BLOB へのアクセスを許可する場合など、データへの制限されたアクセスを提供する場合によく使用されます。

> [!NOTE]
> BLOB への匿名のアクセスも許可できるものの、共有アクセス署名ではより制御されたアクセスを提供することができます。SAS は生成の必要があるためです。
>
>

クラウドベースのサービスなどの信頼されたアプリケーションは、**BlobService** の **generateSharedAccessSignature** を使って共有アクセス署名を生成し、信頼されていないか、モバイル アプリなどの部分的に信頼されたアプリケーションにこれを提供します。 共有アクセス署名は、ポリシーを使用して作成されます。共有アクセス署名には、有効期間の開始日と終了日、共有アクセス署名フォルダーに付与されているアクセス レベルが記述されています。

次のコード例では、共有アクセス署名の保有者による **myblob** BLOB に対する読み取り操作を許可する新しい共有アクセス ポリシーを生成します。このポリシーは作成後 100 分が経過すると期限切れになります。

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

共有アクセス署名の保有者がコンテナーにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

その後、クライアント アプリケーションは、この共有アクセス署名と **BlobServiceWithSAS** を使用して BLOB に対する操作を実行します。 次の例では、 **myblob**に関する情報を取得します。

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

共有アクセス署名は読み取り専用アクセスで生成されるので、BLOB を変更しようとすると、エラーが返されます。

### <a name="access-control-lists"></a>アクセス制御リスト
SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。 複数のクライアントにコンテナーへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。 次のコード例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう&1; つは "user2" 用です。

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

次のコード例では、**mycontainer** の現在の ACL を取得してから、**setBlobAcl** を使って新しいポリシーを追加しています。 この手法で以下を実行できます。

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL を設定したら、ポリシーの ID に基づいて共有アクセス署名を作成できます。 次のコード例では、'user2' の新しい共有アクセス署名を作成します。

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Storage SDK for Node の API リファレンス][Azure Storage SDK for Node API Reference]
* [Azure Storage チーム ブログ][Azure Storage Team Blog]
* GitHub の [Azure Storage SDK for Node] リポジトリ[Azure Storage SDK for Node]。
* [Node.js デベロッパー センター](/develop/nodejs/)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[Azure App Service での Node.js Web アプリの作成]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Azure Table サービスを使用する Node.js Web アプリ]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[WebMatrix を使用した Node.js Web アプリの構築と Azure へのデプロイ]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure portal]: https://portal.azure.com
[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Storage SDK for Node API Reference]: http://dl.windowsazure.com/nodestoragedocs/index.html



<!--HONumber=Jan17_HO4-->


