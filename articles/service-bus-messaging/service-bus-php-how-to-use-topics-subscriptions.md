---
title: Service Bus トピックの使用方法 (PHP) | Microsoft Docs
description: Azure 上の PHP で Service Bus トピックを使用する方法について説明します。
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 18aeaccef724ba94a9c18240fb77ea33897e8d26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063867"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>PHP で Service Bus のトピックとサブスクリプションを使用する方法

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Service Bus のトピックとサブスクリプションの使用方法について説明します。 サンプルは PHP で記述され、[Azure SDK for PHP](../php-download-sdk.md) を利用しています。 紹介するシナリオは次のとおりです。

- キュー、トピック、およびサブスクリプションを作成する 
- サブスクリプション フィルターを作成する 
- メッセージをトピックに送信する 
- サブスクリプションからメッセージを受信する
- トピックとサブスクリプションを削除する

## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 「[Quickstart:Use the Azure portal to create a Service Bus topic and subscriptions to the topic](service-bus-quickstart-topics-subscriptions-portal.md)」(クイック スタート: Azure portal を使用して Service Bus トピックとその中に含まれるサブスクリプションを作成する) の手順に従って、Service Bus の**名前空間**を作成し、**接続文字列**を取得します。

    > [!NOTE]
    > このクイック スタートでは、**PHP** を使用して **トピック**とその中に含まれる**サブスクリプション**を作成します。 

## <a name="create-a-php-application"></a>PHP アプリケーションの作成
Microsoft Azure Blob service にアクセスする PHP アプリケーションを作成するための要件は、コード内から [Azure SDK for PHP](../php-download-sdk.md) のクラスを参照することのみです。 アプリケーションの作成には任意の開発ツールまたはメモ帳を使用できます。

> [!NOTE]
> PHP のインストールでは、[OpenSSL 拡張機能](https://php.net/openssl)をインストールして有効にしておく必要もあります。
> 
> 

この記事では、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできるサービス機能の使用方法について説明します。

## <a name="get-the-azure-client-libraries"></a>Azure クライアント ライブラリの入手

### <a name="install-via-composer"></a>Composer 経由でインストールする
1. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。
3. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Service Bus API を使用するには、以下の手順を実行します。

1. [require_once][require-once] ステートメントを使用してオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServiceBusService** クラスを参照する方法を示しています。

> [!NOTE]
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。 ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、**WindowsAzure.php** オートローダー ファイルを参照する必要があります。
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

次の例では、`require_once` ステートメントは常に表示されていますが、サンプルの実行に必要なクラスのみが参照されています。

## <a name="set-up-a-service-bus-connection"></a>Service Bus 接続の設定
Service Bus クライアントをインスタンス化するには、まず次の形式の有効な接続文字列が必要です。

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

ここで、`Endpoint` の一般的な形式は `https://[yourNamespace].servicebus.windows.net` です。

いずれかの Azure サービス クライアントを作成するには、`ServicesBuilder` クラスを使用する必要があります。 次のようにすることができます。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
  * 既定では 1 つの外部ソース (環境変数) のみサポートされています。
  * `ConnectionStringSource` クラスを拡張して新しいソースを追加できます。

ここで概説している例では、接続文字列が直接渡されます。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>トピックを作成する
`ServiceBusRestProxy` クラスを介して Service Bus トピックに対する管理操作を実行できます。 `ServicesBuilder::createServiceBusService` ファクトリ メソッドを介して、`ServiceBusRestProxy` オブジェクトがトークン アクセス許可をカプセル化してそれを管理する適切な接続文字列とともに構築されます。

次の例では、`ServiceBusRestProxy` をインスタンス化し、`ServiceBusRestProxy->createTopic` を呼び出して、`MySBNamespace` 名前空間内で `mytopic` という名前のトピックを作成する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> `ServiceBusRestProxy` オブジェクトで `listTopics` メソッドを使用することで、指定した名前のトピックがサービス名前空間に既に存在するかどうかを確認できます。
> 
> 

## <a name="create-a-subscription"></a>サブスクリプションの作成
トピック サブスクリプションも `ServiceBusRestProxy->createSubscription` メソッドで作成します。 サブスクリプションを指定し、サブスクリプションの仮想キューに渡すメッセージを制限するフィルターを設定できます。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成
新しいサブスクリプションの作成時にフィルターが指定されていない場合は､**MatchAll** フィルター (既定) が使用されます｡ **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、`mysubscription` という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成
トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージを指定できるフィルターを設定することもできます。 サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SqlFilter について詳しくは、「[SqlFilter.SqlExpression プロパティ][sqlfilter]」をご覧ください。

> [!NOTE]
> サブスクリプションのルールごとに受信メッセージが個別に処理され、処理後のメッセージがサブスクリプションに追加されます。 さらに、新しいサブスクリプションにはそれぞれ、既定の**ルール** オブジェクトがあり、トピックからのすべてのメッセージをサブスクリプションに追加するフィルターが設定されています。 独自のフィルターに一致するメッセージのみ受信するには、この既定のルールを削除する必要があります。 `ServiceBusRestProxy->deleteRule` メソッドを使用して既定のルールを削除できます。
> 
> 

次の例では、`HighMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム `MessageNumber` プロパティが 3 を超えるメッセージのみが選択されます。 メッセージにカスタム プロパティを追加する方法について詳しくは、「[メッセージをトピックに送信する](#send-messages-to-a-topic)」をご覧ください。

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

このコードには追加の名前空間 `WindowsAzure\ServiceBus\Models\SubscriptionInfo` を使用する必要があります。

同様に、次の例では `LowMessages` という名前のサブスクリプションを作成し、`SqlFilter` を適用します。このフィルターでは、`MessageNumber` プロパティが 3 以下のメッセージのみが選択されます。

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

これでメッセージが `mytopic` に送信されると、そのメッセージは `mysubscription` サブスクリプションをサブスクライブした受信者に必ず配信され、さらにメッセージの内容に応じて、`HighMessages` および `LowMessages` トピック サブスクリプションをサブスクライブしている受信者に対して選択的に配信されます。

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
メッセージを Service Bus トピックに送信するには、アプリケーションで `ServiceBusRestProxy->sendTopicMessage` メソッドを呼び出します。 次のコードでは、上のコードで `MySBNamespace` サービス名前空間内で作成した `mytopic` トピックにメッセージを送信する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

Service Bus トピックに送信されるメッセージは、[BrokeredMessage][BrokeredMessage] クラスのインスタンスです。 [BrokeredMessage][BrokeredMessage] オブジェクトには、一連の標準的なプロパティとメソッドだけでなく、アプリケーションに固有のカスタム プロパティの保持に使用できるプロパティも用意されています。 次の例は以前に作成した `mytopic` トピックに 5 つのテスト メッセージを送信する方法を示しています。 `setProperty` メソッドを使用して、カスタム プロパティ (`MessageNumber`) を各メッセージに追加します。 `MessageNumber` プロパティの値がメッセージごとに異なります (「[サブスクリプションを作成する](#create-a-subscription)」のセクションで説明したように、この値を使用して、メッセージを受信するサブスクリプションを決定できます)。

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピック サイズの上限は 5 GB です。 クォータについて詳しくは、「[Service Bus のクォータ][Service Bus quotas]」をご覧ください。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
サブスクリプションからメッセージを受信する最適な方法は、`ServiceBusRestProxy->receiveSubscriptionMessage` メソッドを使用する方法です。 メッセージは 2 つの異なるモードで受信できます。[*ReceiveAndDelete* と *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) です。 **PeekLock** が既定値です。

[ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) モードを使用する場合、受信は 1 回ずつの動作になります。つまり、Service Bus は、サブスクリプション内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。 [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)* モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークしているため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされます。

既定の [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージを `ServiceBusRestProxy->deleteMessage` に渡すことで受信処理の第 2 段階を完了します。 Service Bus が `deleteMessage` の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例は、[PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) モード (既定のモード) を使用したメッセージの受信および処理の方法を示しています。 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージについて (`deleteMessage` メソッドの代わりに) `unlockMessage` メソッドを呼び出すことができます。 これにより､Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`deleteMessage` 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 このプロセスは､しばしば "*1 回以上の処理*" と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、状況によっては、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの `getMessageId` メソッドを使用して対処します。配信の試行のたびにメッセージが変わることはありません｡

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する
トピックやサブスクリプションを削除するには、それぞれ `ServiceBusRestProxy->deleteTopic` メソッドまたは `ServiceBusRestProxy->deleteSubscripton` を使用します。 トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。

次のコードは、`mytopic` という名前のトピックとその登録されたサブスクリプションを削除する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

`deleteSubscription` メソッドを使用すると、サブスクリプションを個別に削除できます。

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順
詳細は、[キュー、トピック、およびサブスクリプション][Queues, topics, and subscriptions]を参照してください｡

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
