---
title: Node.js から Azure Queue Storage を使用する方法 - Azure Storage
description: Azure Queue Storage を使用してキューを作成および削除する方法について説明します。 Node.js を使用してメッセージを挿入、取得、削除する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803682"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Node.js から Azure Queue Storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概要

このガイドでは、Azure Queue Storage を使用して一般的なシナリオを実行する方法について説明します。 サンプルは Node.js API を使用して記述されています。 紹介するシナリオには、キュー メッセージの挿入、ピーク、取得、削除が含まれます。 また、キューを作成および削除する方法についても説明します。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成するには、[Azure App Service での Node.js Web アプリの作成](../../app-service/quickstart-nodejs.md)に関するページ、PowerShell を使用した「[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)」、または [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) に関するページを参照してください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成

[JavaScript 用の Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript)に関するページには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node パッケージ マネージャー (npm) を使用してパッケージを取得する

1. PowerShell (Windows)、Terminal (Mac)、Bash (Unix) などのコマンド ライン インターフェイスを使用し、サンプル アプリケーションを作成したフォルダーに移動します。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. コマンド ウィンドウに「`npm install @azure/storage-queue`」と入力します。

1. `node_modules` フォルダーが作成されたことを確認します。 そのフォルダーには、ストレージにアクセスするために必要なクライアント ライブラリが含まれる `@azure/storage-queue` パッケージがあります。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. コマンド ウィンドウに「`npm install azure-storage`」と入力します。

1. `node_modules` フォルダーが作成されたことを確認します。 そのフォルダー内には、ストレージにアクセスするために必要なライブラリを含む `azure-storage` パッケージが見つかります。

---

### <a name="import-the-package"></a>パッケージをインポートする

コード エディターを使用して、キューを使用しようとしている JavaScript ファイルの先頭に次を追加します。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>キューの作成方法

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

次のコードを実行すると、`AZURE_STORAGE_CONNECTION_STRING` という名前の環境変数の値が取得され、それを使用して [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) オブジェクトが作成されます。 その後、このオブジェクトを使用して、特定のキューを操作できる [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) オブジェクトを作成します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

そのキューが既に存在する場合は、例外がスローされます。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Azure モジュールにより、Azure ストレージ アカウントに接続するために必要な情報として、環境変数 `AZURE_STORAGE_ACCOUNT` と `AZURE_STORAGE_ACCESS_KEY`、または `AZURE_STORAGE_CONNECTION_STRING` が読み取られます。 これらの環境変数が設定されていない場合は、`createQueueService` を呼び出すときにアカウント情報を指定する必要があります。

次のコードは、 `QueueService` オブジェクトを作成し、これによってキューを操作できるようにします。

```javascript
var queueSvc = azure.createQueueService();
```

`createQueueIfNotExists` メソッドを呼び出して、指定された名前の新しいキューを作成するか、または既に存在する場合はそのキューを返します。

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

キューが作成されると、 `result.created` は true になります。 キューが存在する場合は、 `result.created` は false です。

---

## <a name="how-to-insert-a-message-into-a-queue"></a>メッセージをキューに挿入する方法

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

キューにメッセージを追加するには、[`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) メソッドを呼び出します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

キューにメッセージを挿入するには、`createMessage` メソッドを呼び出して新しいメッセージを作成し、それをキューに追加します。

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>次のメッセージをピークする方法

`peekMessages` メソッドを呼び出すことにより、キュー内のメッセージをそのキューから削除することなくピークできます。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

既定では、[`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) によって 1 つのメッセージがピークされます。 次の例では、キュー内の最初の 5 つのメッセージをピークします。 表示されるメッセージが 5 つより少ない場合は、表示されるメッセージだけが返されます。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

`peekMessages` の既定では、1 つのメッセージを対象としてピークします。

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` にはメッセージが含まれます。

---

キュー内にメッセージがないときに `peekMessages` を呼び出しても、エラーは返されません。 ただし、メッセージは返されません。

## <a name="how-to-change-the-contents-of-a-queued-message"></a>キューに配置されたメッセージの内容を変更する方法

次の例では、メッセージのテキストを更新します。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) を呼び出すことにより、キュー内のメッセージの内容をその場で変更します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

`updateMessage` を呼び出すことにより、キュー内のメッセージの内容をインプレースで変更します。

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

---

## <a name="how-to-dequeue-a-message"></a>メッセージをデキューする方法

メッセージのデキューは、次の 2 段階のプロセスです。

1. メッセージを取得する。

1. メッセージを削除する。

次の例では、メッセージを取得してから削除します。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

メッセージを取得するには、[`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) メソッドを呼び出します。 この呼び出しによりそのメッセージはキュー内で非表示になるため、他のクライアントは処理できません。 アプリケーションによるメッセージの処理が済んだら、[`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) を呼び出してキューからそれを削除します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

既定では、メッセージが非表示になるのは 30 秒間だけです。 30 秒後に、他のクライアントに表示されます。 `receiveMessages` を呼び出すときに [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) を設定することで、異なる値を指定できます。

キュー内にメッセージがないときに `receiveMessages` を呼び出しても、エラーは返されません。 ただし、メッセージは返されません。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

メッセージを取得するには、`getMessages` メソッドを呼び出します。 この呼び出しによりそのメッセージはキュー内で非表示になるため、他のクライアントは処理できません。 アプリケーションでメッセージが処理されたら、 `deleteMessage` を呼び出してキューからこのメッセージを削除します。

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

既定では、メッセージが非表示になるのは 30 秒間だけです。 30 秒後に、他のクライアントに表示されます。 `getMessages` で `options.visibilityTimeout` を使うことにより、別の値を指定できます。

キュー内にメッセージがないときに `getMessages` を使用しても、エラーは返されません。 ただし、メッセージは返されません。

---

## <a name="additional-options-for-dequeuing-messages"></a>メッセージのデキュー用の追加オプション

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): メッセージのバッチを取得します (最大 32 個)。
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): 非表示タイムアウトを長くするか、短くします。

次の例では、`receiveMessages` メソッドを使用して、1 回の呼び出しで 5 つのメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、このメソッドで返されるすべてのメッセージの非表示タイムアウトを 5 分に設定します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

- `options.numOfMessages`:メッセージのバッチを取得します (最大 32 個)。
- `options.visibilityTimeout`:非表示タイムアウトを長くするか、短くします。

次のコード例では、 `getMessages` メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、このメソッドで返されるすべてのメッセージの非表示タイムアウトを 5 分に設定します。

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
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

---

## <a name="how-to-get-the-queue-length"></a>キューの長さを取得する方法

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) メソッドからは、キューに関するメタデータ (キュー内で待機しているメッセージの概数を含む) が返されます。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

`getQueueMetadata` メソッドは、キューに関するメタデータ (キュー内で待機しているメッセージの概数を含む) を返します。

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>キューを一覧表示する方法

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

キューの一覧を取得するには、[`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) を呼び出します。 特定のプレフィックスによってフィルター処理された一覧を取得するには、呼び出し内の [options.prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) を `listQueues` に設定します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

キューの一覧表示を取得するには、 `listQueuesSegmented`を使用します。 特定のプレフィックスでフィルター処理した一覧を取得するには、 `listQueuesSegmentedWithPrefix`を使用します。

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

すべてのキューを返せない場合は、さらに多くの結果を取得するために、`listQueuesSegmented` の最初のパラメーター、または `listQueuesSegmentedWithPrefix` の 2 番目のパラメーターとして `result.continuationToken` を渡します。

---

## <a name="how-to-delete-a-queue"></a>キューを削除する方法

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

キューおよびキューに格納されているすべてのメッセージを削除するには、`QueueClient` オブジェクトで [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) メソッドを呼び出します。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

キューを削除することなくすべてのメッセージをクリアするには、[`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) を呼び出します。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトで `deleteQueue` メソッドを呼び出します。

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

すべてのメッセージを削除することなくキューからクリアするには、`clearMessages` を呼び出します。

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>次のステップ

これで、Queue Storage の基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

- 新機能を確認するには、[Azure Storage チーム ブログ](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)を参照してください
- GitHub 上の [JavaScript 用の Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript)のリポジトリにアクセスしてください。
