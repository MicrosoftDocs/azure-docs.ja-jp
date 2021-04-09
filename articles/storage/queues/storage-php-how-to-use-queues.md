---
title: PHP から Queue Storage を使用する方法 - Azure Storage
description: Azure Queue Storage を使用して、キューの作成と削除のほか、メッセージの挿入、取得、削除を行う方法を説明します。 サンプルは PHP で記述されています。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592026"
---
# <a name="how-to-use-queue-storage-from-php"></a>PHP から Queue Storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

このガイドでは、Azure Queue Storage サービスを使用して、一般的なシナリオを実行する方法について説明します。 サンプルは、[PHP 用の Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-php)のクラスを使用して記述されます。 キュー メッセージの挿入、ピーク、取得、削除のシナリオ、キューの作成と削除のシナリオについて説明します。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP アプリケーションの作成

Azure Queue Storage にアクセスする PHP アプリケーションを作成するための要件は、コード内から [PHP 用の Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-php)のクラスを参照することのみです。 アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用する Queue Storage サービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web アプリケーション内で実行されるコードで呼び出すこともできます。

## <a name="get-the-azure-client-libraries"></a>Azure クライアント ライブラリの入手

### <a name="install-via-composer"></a>Composer 経由でインストールする

1. プロジェクトのルートに `composer.json` という名前のファイルを作成し、次のコードをそれに追加します。

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. プロジェクトのルートに [`composer.phar`](https://getcomposer.org/composer.phar) をダウンロードします。

3. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

    ```console
    php composer.phar install
    ```

または、GitHub で [Azure Storage PHP クライアント ライブラリ](https://github.com/Azure/azure-storage-php)に移動して、ソース コードを複製します。

## <a name="configure-your-application-to-access-queue-storage"></a>Queue ストレージにアクセスするようにアプリケーションを構成する

Azure Queue Storage で API を使用するには、次のことが必要になります。

1. [`require_once`](https://www.php.net/manual/en/function.require-once.php) ステートメントを使用してオートローダー ファイルを参照する。
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、`QueueRestProxy` クラスを参照する方法を示しています。

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

次の例では、常に `require_once` ステートメントが表示されますが、参照されるのは、例を実行するのに必要なクラスのみとなります。

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage の接続文字列の設定

Azure Queue Storage クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 Queue Storage の接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

エミュレーター ストレージにアクセスする場合:

```php
UseDevelopmentStorage=true
```

Azure Queue Storage クライアントを作成するには、`QueueRestProxy` クラスを使用する必要があります。 次の手法のうちどちらかを使用できます。

- 接続文字列を直接渡す
- Web アプリで環境変数を使用して、接続文字列を格納する。 接続文字列の構成については、[Azure Web アプリ構成の設定](../../app-service/configure-common.md)に関するドキュメントを参照してください。

ここで概説している例では、接続文字列が直接渡されます。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>キューを作成する

`QueueRestProxy` オブジェクトを使用すると、`CreateQueue` メソッドを使用してキューを作成できます。 キューの作成時にキューのオプションを設定できますが、この設定は必須ではありません 次の例では、キューのメタデータを設定する方法を示しています。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> メタデータ キーでは大文字と小文字は区別されません。 すべてのキーはサービスから小文字で返されます。

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する

メッセージをキューに追加するには、`QueueRestProxy->createMessage` を使用します。 このメソッドにはキュー名、メッセージ テキスト、メッセージ オプション (省略可能) を渡します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>次のメッセージをピークする

`QueueRestProxy->peekMessages` を呼び出すと、キューの先頭にある 1 つまたは複数のメッセージをキューから削除せずにクイック表示することができます。 既定では、`peekMessage` メソッドからは 1 つのメッセージが返されます。しかし、この数は `PeekMessagesOptions->setNumberOfMessages` メソッドを使用して変更できます。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージを削除します。 まず、`QueueRestProxy->listMessages` を呼び出します。これにより、このメッセージは、このキューから読み取る他のコードからは参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 (メッセージがこの時間内に削除されない場合、このキューで再び参照できるようになります)。キューからのメッセージの削除を完了するには、`QueueRestProxy->deleteMessage` を呼び出す必要があります。 2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 ご自分のコードで、メッセージが処理された直後に `deleteMessage` を呼び出します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更するには、`QueueRestProxy->updateMessage` を呼び出します。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。 これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。 この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。 通常は、さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合はメッセージを削除するようにします。 こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>メッセージのデキュー用の追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、`getMessages` メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>キューの長さを取得する

キュー内のメッセージの概数を取得できます。 `QueueRestProxy->getQueueMetadata` メソッドを使用して、キューに関するメタデータを取得します。 返されたオブジェクトに対して `getApproximateMessageCount` メソッドを呼び出すと、キュー内のメッセージの数を取得できます。 要求に対して Queue Storage からの応答があった後にメッセージが追加または削除される可能性があるため、これらの値は概数にすぎません。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>キューを削除する

キューとキュー内のすべてのメッセージとを削除するには、`QueueRestProxy->deleteQueue` メソッドを呼び出します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>次のステップ

これで、Azure Queue Storage の基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

- [Azure Storage PHP クライアント ライブラリの API リファレンス](https://azure.github.io/azure-storage-php/)を参照してください
- [詳細な Queue の例](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)を参照してください。

詳細については、[PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)を参照してください。
