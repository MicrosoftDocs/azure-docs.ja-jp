---
title: クイック スタート:Azure Queue storage ライブラリ v12 - JavaScript
description: Azure Queue JavaScript v12 ライブラリを使用してキューを作成し、そのキューにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って削除する方法について説明します。 キューを削除する方法についても説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: f2d740184c160faa241e3c0267ec39e1309725a8
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431321"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>クイック スタート:JavaScript 用 Azure Queue storage クライアント ライブラリ v12

JavaScript 用 Azure Queue storage クライアント ライブラリ バージョン 12 を使用してみましょう。 Azure Queue storage は、後で取得して処理するために多数のメッセージを格納するためのサービスです。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

JavaScript 用 Azure Queue storage クライアント ライブラリ v12 を使用すると、以下のことができます。

* キューを作成する
* メッセージをキューに追加する
* キュー内のメッセージを表示する
* キュー内のメッセージを更新する
* キューからメッセージを受信する
* キューからメッセージを削除する
* キューを削除する

その他のリソース:

* [API リファレンス ドキュメント](https://docs.microsoft.com/javascript/api/@azure/storage-queue/)
* [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
* [パッケージ (ノード パッケージ マネージャー)](https://www.npmjs.com/package/@azure/storage-queue)
* [サンプル](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
* Azure Storage アカウント - [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 使用するオペレーティング システム用の最新の [Node.js](https://nodejs.org/en/download/)。

## <a name="setting-up"></a>設定

このセクションでは、JavaScript 用 Azure Queue storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

*queues-quickstart-v12* という名前の Node.js アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、プロジェクト用に新しいディレクトリを作成します。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 新しく作成された *queues-quickstart-v12* ディレクトリに切り替えます。

    ```console
    cd queues-quickstart-v12
    ```

1. *package.json* という名前の新しいテキスト ファイルを作成します。 このファイルには、Node.js プロジェクトが定義されます。 このファイルを *queues-quickstart-v12* ディレクトリに保存します。 ファイルのコンテンツを次に示します。

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    必要に応じて、`author` フィールドにご自身の名前を入力できます。

### <a name="install-the-package"></a>パッケージをインストールする

まだ *queues-quickstart-v12* ディレクトリにいる間に、`npm install` コマンドを使用して、JavaScript パッケージ用 Azure Queue storage クライアント ライブラリをインストールします。

```console
npm install
```

 このコマンドにより、*package.json* ファイルが読み取られ、JavaScript パッケージ用 Azure Queue storage クライアント ライブラリ v12 と、それが依存しているすべてのライブラリがインストールされます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで別の新しいテキスト ファイルを開く
1. `require` の呼び出しを追加して Azure および Node.js モジュールを読み込む
1. 極めて基本的な例外処理を含め、プログラムの構造を作成します

    コードは次のとおりです。

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. この新しいファイルを *queues-quickstart-v12.js* として *queues-quickstart-v12* ディレクトリに保存します。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。 Queue storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のキュー
* キュー内のメッセージ

次の図に、これらのリソースの関係を示します。

![Queue storage のアーキテクチャ図](./media/storage-queues-introduction/queue1.png)

これらのリソースとやり取りするには、以下の JavaScript クラスを使用します。

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` を使用すると、ストレージ アカウント内のすべてのキューを管理できます。
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): `QueueClient` クラスを使用すると、個々のキューとそのメッセージを管理および操作できます。
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` クラスは、キューの [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) を呼び出したときに返される個々のオブジェクトを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、JavaScript 用 Azure Queue storage クライアント ライブラリを使用して以下の操作を実行する方法を示します。

* [接続文字列を取得する](#get-the-connection-string)
* [キューを作成する](#create-a-queue)
* [メッセージをキューに追加する](#add-messages-to-a-queue)
* [キュー内のメッセージを表示する](#peek-at-messages-in-a-queue)
* [キュー内のメッセージを更新する](#update-a-message-in-a-queue)
* [キューからメッセージを受信する](#receive-messages-from-a-queue)
* [キューからメッセージを削除する](#delete-messages-from-a-queue)
* [キューを削除する](#delete-a-queue)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

次のコードを `main` 関数内に追加します。

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>キューを作成する

新しいキューの名前を決定します。 次のコードでは、確実に一意になるように、キュー名に UUID 値を追加します。

> [!IMPORTANT]
> キュー名に使用できるのは小文字、数字、ハイフンのみであり、名前の先頭は文字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。 また、名前は 3 から 63 文字で指定する必要があります。 キューの名前付け規則の詳細については、「[キューとメタデータの名前付け規則](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)」を参照してください。

[QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) クラスのインスタンスを作成します。 次に、[create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) メソッドを呼び出して、ストレージ アカウントにキューを作成します。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>メッセージをキューに追加する

以下のコード スニペットでは、[sendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) メソッドを呼び出してキューにメッセージを追加します。 また、3 番目の `sendMessage` 呼び出しでは、返された [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) を保存します。 返された `sendMessageResponse` は、後でプログラムの中でメッセージの内容を更新する際に使用します。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>キュー内のメッセージを表示する

キュー内のメッセージを表示するには、[peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) メソッドを呼び出します。 `peekMessages` メソッドは、キューの先頭からメッセージを 1 つ以上取得しますが、メッセージの可視性は変更しません。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>キュー内のメッセージを更新する

メッセージの内容を更新するには、[updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) メソッドを呼び出します。 メッセージの表示タイムアウトと内容は、`updateMessage` メソッドで変更できます。 メッセージの内容には UTF-8 でエンコードされた文字列を指定してください。最大サイズは 64 KB です。 先ほどこのコードの中で保存した応答の `messageId` と `popReceipt` を、新しい内容と共に渡します。 `sendMessageResponse` プロパティによって、更新するメッセージが識別されます。

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

[receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) メソッドを呼び出して、先ほど追加したメッセージをダウンロードします。  `numberOfMessages` フィールドには、この呼び出しで受信する最大メッセージ数を渡します。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>キューからメッセージを削除する

メッセージは、受信して処理した後にキューから削除します。 ここでの処理は、単にメッセージをコンソールに表示するだけです。

[deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) メソッドを呼び出してメッセージを削除します。 明示的に削除されなかったメッセージは、最終的にキューに再表示され、別の機会に処理されることになります。

`main` 関数の末尾に次のコードを追加します。

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>キューを削除する

次のコードでは、[delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) メソッドを使用してキューを削除することにより、アプリによって作成されたリソースがクリーンアップされます。

`main` 関数の末尾に次のコードを追加してファイルを保存します。

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>コードの実行

このアプリは、3 つのメッセージを作成して Azure のキューに追加します。 コードでは、キュー内のメッセージを一覧表示した後にそれらを取得して削除してから、最後にキューを削除します。

コンソール ウィンドウで、*queues-quickstart-v12.js* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node queues-quickstart-v12.js
```

アプリの出力は、次の例のようになります。

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

デバッガーでコードをステップ実行し、プロセス全体について [Azure portal](https://portal.azure.com) 上でチェックします。 ストレージ アカウントをチェックして、キュー内のメッセージが作成され、その後削除されることを確認してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、JavaScript コードを使用して、キューを作成し、そこにメッセージを追加する方法について説明しました。 その後、メッセージの表示、取得、削除について説明しました。 最後に、メッセージ キューを削除する方法を説明しました。

チュートリアル、サンプル、クイック スタートなどのドキュメントについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [Azure for JavaScript のドキュメント](https://docs.microsoft.com/azure/developer/javascript/)

* 詳細については、[JavaScript 用 Azure Storage キュー クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)に関するページを参照してください。
* その他の Azure Queue storage サンプル アプリについては、[Azure Queue storage クライアント ライブラリ v12 JavaScript サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples) ページを参照してください。
