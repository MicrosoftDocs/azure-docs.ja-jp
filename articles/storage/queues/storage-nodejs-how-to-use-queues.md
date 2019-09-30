---
title: Node.js から Azure Queue storage を使用する - Azure Storage
description: Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。 サンプルは Node.js で記述されています。
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 83aecc9c05fec5869a82930f2d687ac907981c00
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858670"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Node.js から Queue ストレージを使用する方法
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>概要
このガイドでは、Microsoft Azure Queue サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Node.js API を使用して記述されています。 キュー メッセージの**挿入**、**ピーク**、**取得**、**削除**と、**キューの作成と削除**の各シナリオについて説明します。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成
空の Node.js アプリケーションを作成します。 Node.js アプリケーションの作成手順については、「[Azure App Service での Node.js Web アプリの作成](../../app-service/app-service-web-get-started-nodejs.md)」、「[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)」 (Windows PowerShell の使用)、または [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) に関するページをご覧ください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する
1. **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。
2. コマンド ウィンドウに「 **npm install azure-storage** 」と入力します。 このコマンドの出力は次の例のようになります。
 
    ```bash
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
    ```

3. 手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。 このフォルダーに **azure-storage** パッケージがあります。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### <a name="import-the-package"></a>パッケージをインポートする
メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
Azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT と AZURE\_STORAGE\_ACCESS\_KEY、または AZURE\_STORAGE\_CONNECTION\_STRING を読み取ります。 これらの環境変数が設定されていない場合、 **createQueueService**を呼び出すときにアカウント情報を指定する必要があります。

## <a name="how-to-create-a-queue"></a>方法:キューを作成する
次のコードは、 **QueueService** オブジェクトを作成し、これによってキューを操作できるようにします。

```javascript
var queueSvc = azure.createQueueService();
```

**createQueueIfNotExists** メソッドを使います。このメソッドは、指定されたキューが存在する場合は、そのキューを返します。指定されたキューが存在しない場合は、指定された名前で新しいキューを作成します。

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

キューが作成されると、 `result.created` は true になります。 キューが存在する場合は、 `result.created` は false です。

### <a name="filters"></a>フィルター
オプションのフィルター操作は、**QueueService** を使って行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

```javascript
function handle (requestOptions, next)
```

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

```javascript
function (returnObject, finalCallback, next)
```

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **QueueService** オブジェクトを作成します。

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>方法:メッセージをキューに挿入する
キューにメッセージを挿入するには、 **createMessage** メソッドを使用し、新しいメッセージを作成してキューに追加します。

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする
**peekMessages** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 既定では、 **peekMessages** は 1 つのメッセージを対象としてピークします。

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` にはメッセージが含まれます。

> [!NOTE]
> キューにメッセージがないときに **peekMessages** を使用した場合、エラーは返されませんが、メッセージも返されません。
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする
メッセージは、次の 2 段階のプロセスで処理されます。

1. メッセージをデキューする。
2. メッセージを削除する。

メッセージをデキューするには、 **getMessage**を使用します。 これによりメッセージはキューで参照できなくなるため、他のクライアントがこれを処理することもできません。 アプリケーションでメッセージが処理されたら、 **deleteMessage** を呼び出してキューからこのメッセージを削除します。 次に、メッセージを取得してから削除する例を示します。

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> 既定では、メッセージが非表示になるのは 30 秒間のみで、それ以降は他のクライアントから参照できます。 **getMessages** で `options.visibilityTimeout` を使うことにより、別の値を指定できます。
> 
> [!NOTE]
> キューにメッセージがないときに **getMessages** を使用した場合、エラーは返されませんが、メッセージも返されません。
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する
**updateMessage**を使用すると、キュー内のメッセージの内容をインプレースで変更できます。 次に、メッセージのテキストを更新する例を示します。

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>方法:メッセージをデキューするためのその他のオプション
キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

* `options.numOfMessages` - メッセージをバッチで取得する (最大 32 個)。
* `options.visibilityTimeout` - 非表示タイムアウトを長くするか、短くする。

次のコード例では、 **getMessages** メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。 その後、for ループを使用して、各メッセージを処理します。 また、このメソッドで返されるすべてのメッセージの非表示タイムアウトを 5 分に設定します。

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する
**getQueueMetadata** は、キューで待機中のおおよそのメッセージ数など、キューに関するメタデータを返します。

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>方法:キューを一覧表示する
キューの一覧表示を取得するには、 **listQueuesSegmented**を使用します。 特定のプレフィックスでフィルター処理した一覧を取得するには、 **listQueuesSegmentedWithPrefix**を使用します。

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

すべてのキューを返すことができない場合は、`result.continuationToken` を **listQueuesSegmented** の最初のパラメーターとして使うか、**listQueuesSegmentedWithPrefix** の 2 つ目のパラメーターとして使って、さらに多くの結果を取得することができます。

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する
キューおよびキューに含まれているすべてのメッセージを削除するには、キュー オブジェクトに対して **deleteQueue** メソッドを呼び出します。

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

すべてのメッセージを削除せずにキューからクリアするには、 **clearMessages**を使用します。

## <a name="how-to-work-with-shared-access-signatures"></a>方法:Shared Access Signature を操作する
共有アクセス署名 (SAS) は、ストレージ アカウントの名前またはキーを指定せずにキューへの細密なアクセスを提供する安全な方法です。 SAS は、モバイル アプリによるメッセージの送信を許可する場合など、キューへの制限されたアクセスを提供する場合によく使用されます。

クラウドベースのサービスなどの信頼されたアプリケーションは、**QueueService** の **generateSharedAccessSignature** を使って SAS を生成し、信頼されていないか、部分的に信頼されたアプリケーションにこれを提供します。 たとえば、モバイル アプリなどです。 SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

次の例では、SAS の保有者によるキューへのメッセージの追加を許可する新しい共有アクセス ポリシーを生成します。このポリシーは作成後 100 分が経過すると期限切れになります。

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

SAS の保有者がキューにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

その後、クライアント アプリケーションは、この SAS と **QueueServiceWithSAS** を使用してキューに対する操作を実行します。 次に、キューに接続してメッセージを作成する例を示します。

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

生成された SAS が持つ権限は追加アクセスであるため、メッセージの読み取り、更新、または削除を試行した場合は、エラーが返されます。

### <a name="access-control-lists"></a>アクセス制御リスト
SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。 複数のクライアントにキューへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。 次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

次の例では、**myqueue** の現在の ACL を取得てから、**setQueueAcl** を使って新しいポリシーを追加します。 この手法で以下を実行できます。

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。 次の例では、"user2" 用に新しい SAS を作成しています。

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>次の手順
これで、Queue ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

* [Azure Storage チームのブログ][Azure Storage Team Blog]
* GitHub の [Azure Storage SDK for Node][Azure Storage SDK for Node] リポジトリ



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Azure App Service での Node.js Web アプリの作成](../../app-service/app-service-web-get-started-nodejs.md)

[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
