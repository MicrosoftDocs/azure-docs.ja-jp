---
title: Service Bus キューの使用方法 (PHP) | Microsoft Docs
description: Azure での Service Bus キューの使用方法を学習します。 コード サンプルは PHP で記述されています。
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 55eee839e24db2ad96eb635adc488e9a119c5907
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501197"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>PHP で Service Bus キューを使用する方法
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

このチュートリアルでは、PHP アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信する方法を学習します。 

## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。
    1. Service Bus **キュー**の**概要**をお読みください。 
    2. Service Bus **名前空間**を作成します。 
    3. **接続文字列**を取得します。 

        > [!NOTE]
        > このチュートリアルでは、PHP を使用して Service Bus 名前空間で**キュー**を作成します。 
3. [Azure SDK for PHP](../php-download-sdk.md)

## <a name="create-a-php-application"></a>PHP アプリケーションの作成
Microsoft Azure Blob service にアクセスする PHP アプリケーションを作成するための要件は、コード内から [Microsoft Azure SDK for PHP ](../php-download-sdk.md)のクラスを参照することのみです。 アプリケーションの作成には任意の開発ツールまたはメモ帳を使用できます。

> [!NOTE]
> PHP のインストールでは、[OpenSSL 拡張機能](https://php.net/openssl)をインストールして有効にしておく必要もあります。

このガイドで使用するサービス機能は、PHP アプリケーション内からローカルで呼び出すことも、Azure の Web ロール、worker ロール、Web サイト上で実行されるコード内で呼び出すこともできます。

## <a name="get-the-azure-client-libraries"></a>Azure クライアント ライブラリの入手
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Service Bus キュー API を使用するには、次の操作を行います。

1. [require_once][require_once] ステートメントを使用してオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、`ServicesBuilder` クラスを参照する方法を示しています。

> [!NOTE]
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。 ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、**WindowsAzure.php** オートローダー ファイルを参照する必要があります。
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

下のすべてのサンプルに `require_once` ステートメントが入っていますが、サンプルの実行に必要なクラスのみが参照されます。

## <a name="set-up-a-service-bus-connection"></a>Service Bus 接続の設定
Service Bus クライアントをインスタンス化するには、まず次の形式の有効な接続文字列が必要です。

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

ここで、`Endpoint` の一般的な形式は `[yourNamespace].servicebus.windows.net` です。

いずれかの Azure サービス クライアントを作成するには、`ServicesBuilder` クラスを使用する必要があります。 次のようにすることができます。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
  * 既定では 1 つの外部ソース (環境変数) のみがサポートされています。
  * `ConnectionStringSource` クラスを拡張して新しいソースを追加できます。

ここで概説している例では、接続文字列が直接渡されます。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>キューを作成する
`ServiceBusRestProxy` クラスを使用して Service Bus キューに対する管理操作を実行できます。 `ServicesBuilder::createServiceBusService` ファクトリ メソッドを介して、`ServiceBusRestProxy` オブジェクトがトークン アクセス許可をカプセル化してそれを管理する適切な接続文字列とともに構築されます。

次の例では、`ServiceBusRestProxy` をインスタンス化し、`ServiceBusRestProxy->createQueue` を呼び出して、`MySBNamespace` サービス名前空間内で `myqueue` という名前のキューを作成する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> `ServiceBusRestProxy` オブジェクトの `listQueues` メソッドを使用すると、指定した名前のキューが名前空間に既に存在するかどうかを確認できます。
> 
> 

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
メッセージを Service Bus キューに送信するには、アプリケーションで `ServiceBusRestProxy->sendQueueMessage` メソッドを呼び出します。 次のコードでは、上のコードで `MySBNamespace` サービス名前空間内で作成した `myqueue` キューにメッセージを送信する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Service Bus キューに送信されたメッセージ (および Service Bus キューから受信したメッセージ) は、[BrokeredMessage][BrokeredMessage] クラスのインスタンスになります。 [BrokeredMessage][BrokeredMessage] オブジェクトには、アプリケーションに特有のカスタム プロパティの保持に使用する標準的なプロパティやメソッド、および任意のアプリケーション データの本体が含まれています。

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。 このキュー サイズの上限は 5 GB です。

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

キューからメッセージを受信する最適な方法は、`ServiceBusRestProxy->receiveQueueMessage` メソッドを使用する方法です。 メッセージは 2 つの異なるモードで受信できます。[*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) と [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) です。 **PeekLock** が既定値です。

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードを使用する場合、受信が 1 回ずつの動作になります。つまり、Service Bus はキュー内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

既定の [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージを `ServiceBusRestProxy->deleteMessage` に渡すことで受信処理の第 2 段階を完了します。 Service Bus が `deleteMessage` の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例は、[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) モード (既定のモード) を使用したメッセージの受信および処理の方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージについて (`deleteMessage` メソッドの代わりに) `unlockMessage` メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`deleteMessage` 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は *1 回以上*の処理と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加することをお勧めします。 通常、この問題はメッセージの `getMessageId` メソッドを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="next-steps"></a>次の手順
これで、Service Bus キューの基本を学習できました。詳しくは、[キュー、トピック、およびサブスクリプション][Queues, topics, and subscriptions]に関する記事をご覧ください。

詳しくは、[PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)もご覧ください。

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


