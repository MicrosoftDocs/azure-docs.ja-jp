---
title: "Azure Service Bus のトピックとサブスクリプションを Node.js で使用する方法 | Microsoft Docs"
description: "Node.js アプリから Azure の Service Bus トピックとサブスクリプションを使用する方法を学習します。"
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 56094202673416320e5a8801ee2275881ccfc8fb
ms.openlocfilehash: 4e28b47a1ce1a3bf69a57382a5ec6c2a8a161efe
ms.lasthandoff: 01/13/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Service Bus のトピックとサブスクリプションの使用方法
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

このガイドでは、Node.js アプリケーションから Service Bus のトピックとサブスクリプションを使用する方法について説明します。 ここでは、**トピックとサブスクリプションの作成**、**サブスクリプション フィルターの作成**、**トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。 トピックとサブスクリプションの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成
空の Node.js アプリケーションを作成します。 Node.js アプリケーションを作成する手順については、「[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]」、「[Node.js クラウド サービス][Node.js Cloud Service]」 (Windows PowerShell の使用)、または「WebMatrix を使用した Web サイト」をご覧ください。

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Service Bus を使用するには、Node.js Azure パッケージをダウンロードします。 このパッケージには、Service Bus REST サービスと通信するためのライブラリのセットが含まれています。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する
1. **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。
2. コマンド ウィンドウに「**npm install azure**」と入力します。次のような出力が生成されます。

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. 手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。 そのフォルダーで **azure** パッケージを探します。その中に Service Bus トピックにアクセスするために必要なライブラリが含まれています。

### <a name="import-the-module"></a>モジュールのインポート
メモ帳などのテキスト エディターを使用して、アプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Service Bus 接続の設定
Azure モジュールは、Service Bus に接続するために必要な情報を得るために、環境変数 AZURE\_SERVICEBUS\_NAMESPACE と AZURE\_SERVICEBUS\_ACCESS\_KEY を読み取ります。 これらの環境変数が設定されていない場合、**createServiceBusService** を呼び出すときにアカウント情報を指定する必要があります。

Azure Cloud Service の構成ファイルで環境変数を設定する例については、[Storage を使用する Node.js Cloud Service に関するトピック][Node.js Cloud Service with Storage]をご覧ください。

Azure Web サイトの [Azure クラシック ポータル][Azure classic portal]で環境変数を設定する例については、「[ストレージを使用する Node.js Web アプリケーション][Node.js Web Application with Storage]」をご覧ください。

## <a name="create-a-topic"></a>トピックを作成する
**ServiceBusService** オブジェクトを使用すると、トピックを操作できます。 次のコードでは、**ServiceBusService** オブジェクトを作成します。 **server.js** ファイルの先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

```javascript
var serviceBusService = azure.createServiceBusService();
```

**ServiceBusService** オブジェクトで **createTopicIfNotExists** を呼び出すことによって、指定されたトピックが返されるか (存在する場合)、指定された名前で新しいトピックが作成されます。 次のコードでは、**createTopicIfNotExists** を使用して、"MyTopic" という名前のトピックを作成、またはトピックに接続します。

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** は追加のオプションもサポートしています。これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの設定をオーバーライドできます。 次の例では、最大トピック サイズを 5 GB に、有効期間を 1 分に設定します。

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>フィルター
オプションのフィルター操作は、**ServiceBusService** を使用して行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

```javascript
function handle (requestOptions, next)
```

要求オプションに対するプリプロセスを実行した後で、このメソッドは `next` を呼び出して、次のシグネチャのコールバックを渡します。

```javascript
function (returnObject, finalCallback, next)
```

このコールバックで、**returnObject** (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に **finalCallback** を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する&2; つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **ServiceBusService** オブジェクトを作成します。

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>サブスクリプションを作成する
トピック サブスクリプションも **ServiceBusService** オブジェクトで作成します。 サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

> [!NOTE]
> サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。 アプリケーションにサブスクリプションを作成するロジックが含まれている場合は、最初に **getSubscription** メソッドを使用して、サブスクリプションが既に存在しているかどうかを確認する必要があります。
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成
**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。 **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、"AllMessages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成
トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージに絞り込めるフィルターを作成することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **SqlFilter** です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SQL フィルターで使用できる式について詳しくは、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明をご覧ください。

フィルターをサブスクリプションに追加するには、**ServiceBusService** オブジェクトの **createRule** メソッドを使用します。 このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

> [!NOTE]
> 既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるため、最初に既定のフィルターを削除する必要があります。削除しなければ、指定された他のすべてのフィルターは **MatchAll** によってオーバーライドされます。 既定のルールを削除するには、**ServiceBusService** オブジェクトの **deleteRule** メソッドを使用します。
>
>

次の例では、`HighMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム **messagenumber** プロパティが 3 を超えるメッセージのみが選択されます。

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

同様に、次の例では `LowMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、**messagenumber** プロパティが 3 以下のメッセージのみが選択されます。

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

メッセージが `MyTopic` に送信されると、そのメッセージは `AllMessages` トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、`HighMessages` と `LowMessages` トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

## <a name="how-to-send-messages-to-a-topic"></a>メッセージをトピックに送信する方法
メッセージを Service Bus トピックに送信するには、アプリケーションで **ServiceBusService** オブジェクトの **sendTopicMessage** メソッドを使用する必要があります。
Service Bus トピックに送信されたメッセージは **BrokeredMessage** オブジェクトです。
**BrokeredMessage** オブジェクトには、(**Label** や **TimeToLive** などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および文字列データの本体が備わっています。 アプリケーションでは、文字列値を **sendTopicMessage** に渡すことによってメッセージの本文を設定できます。必須の標準プロパティは既定値に設定されます。

次の例では、"MyTopic" にテスト メッセージを&5; 通送信する方法を示しています。 各メッセージの **messagenumber** プロパティの値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
サブスクリプションからメッセージを受信するには、**ServiceBusService** オブジェクトの ** receiveSubscriptionMessage** メソッドを使用します。 既定では、メッセージは読み取られるときにサブスクリプションから削除されますが、省略可能な **isPeekLock** パラメーターを **true** に設定することによって、サブスクリプションからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**isPeekLock** パラメーターが **true** に設定されている場合、受信処理が&2; 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。
アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**deleteMessage** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第&2; 段階を完了します。 **deleteMessage** メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

次の例では、**receiveSubscriptionMessage** を使用したメッセージの受信および処理の方法を示しています。 この例では、最初に "LowMessages" サブスクリプションからメッセージを受信して削除し、次に true に設定した **isPeekLock** を使用して "HighMessages" サブスクリプションからメッセージを受信します。 次に、**deleteMessage** を使用してメッセージを削除します。

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**ServiceBusService** オブジェクトの **unlockMessage** メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は **1 回以上の処理** と呼ばれます。つまり、すべてのメッセージが&1; 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する
トピックおよびサブスクリプションは永続的であり、[Azure クラシック ポータル][Azure classic portal]またはプログラムによって明示的に削除する必要があります。
次の例では、`MyTopic` という名前のトピックを削除する方法を示します。

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。 サブスクリプションは、個別に削除することもできます。 次の例では、`HighMessages` という名前のサブスクリプションを `MyTopic` トピックから削除する方法を示します。

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>次のステップ
これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション][Queues, topics, and subscriptions]。
* [SqlFilter][SqlFilter] の API のリファレンス
* GitHub の [Azure SDK for Node][Azure SDK for Node] リポジトリ。

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure classic portal]: https://manage.windowsazure.com
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js Web Application with Storage]: ../storage/storage-nodejs-use-table-storage-cloud-service-app.md

