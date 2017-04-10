---
title: "Azure Service Bus トピックの使用方法 (Python) | Microsoft Docs"
description: "Python から Azure Service Bus のトピックとサブスクリプションを使用する方法を説明します。"
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: d0714870a0b01eca5e07c171c5ca8d0c0c1d6df1
ms.openlocfilehash: 69f8807d509c31ae4aadeb16731fc481039a7e20
ms.lasthandoff: 01/13/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Service Bus のトピックとサブスクリプションの使用方法
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Service Bus のトピックとサブスクリプションの使用方法について説明します。 サンプルは Python で記述され、[Python Azure パッケージ][Python Azure package]を使用しています。 ここでは、**トピックとサブスクリプションの作成**、**サブスクリプション フィルターの作成**、**トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。 トピックとサブスクリプションの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Python または [Python Azure パッケージ][Python Azure package]をインストールする場合は、[Python インストール ガイド](../python-how-to-install.md)をご覧ください。

## <a name="create-a-topic"></a>トピックを作成する
**ServiceBusService** オブジェクトを使用すると、トピックを操作できます。 プログラムを使用して Service Bus にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

次のコードでは、**ServiceBusService** オブジェクトを作成します。 `mynamespace`、`sharedaccesskeyname`、`sharedaccesskey` の部分は、実際の名前空間、Shared Access Signature (SAS) キー名、キー値に置き換えます。

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SAS キー名とキー値のそれぞれの値は、[Azure ポータル][Azure portal] から取得できます。

```python
bus_service.create_topic('mytopic')
```

**create\_topic** は追加のオプションもサポートしています。これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの設定をオーバーライドできます。 次の例では、最大トピック サイズを 5 GB に、有効期間 (TTL) を 1 分に設定します。

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>サブスクリプションを作成する
トピックのサブスクリプションも **ServiceBusService** オブジェクトで作成します。 サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

> [!NOTE]
> サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションがサブスクライブされているトピックが削除されるまで存在し続けます。
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成
**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。 **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、"AllMessages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成
トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージを指定するフィルターを定義することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **SqlFilter** です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明を参照してください。

**ServiceBusService** オブジェクトの **create\_rule** メソッドを使用して、サブスクリプションにフィルターを追加できます。 このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

> [!NOTE]
> 既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるため、最初に既定のフィルターを削除する必要があります。削除しなければ、指定された他のすべてのフィルターは **MatchAll** によってオーバーライドされます。 既定のルールを削除するには、**ServiceBusService**オブジェクトの **delete\_rule** メソッドを使用します。
> 
> 

次の例では、`HighMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム **messagenumber** プロパティが 3 を超えるメッセージのみが選択されます。

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

同様に、次の例では `LowMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、**messagenumber** プロパティが 3 以下のメッセージのみが選択されます。

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

これでメッセージが `mytopic` に送信されると、そのメッセージは **AllMessages** トピック サブスクリプションをサブスクライブした受信者に必ず配信され、さらにメッセージの内容に応じて、**HighMessages** と **LowMessages** トピック サブスクリプションをサブスクライブしている受信者に対して選択的に配信されます。

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
メッセージを Service Bus トピックに送信するには、アプリケーションで **ServiceBusService** オブジェクトの **send\_topic\_message** メソッドを使用する必要があります。

次の例では、`mytopic` トピックに&5; 件のテスト メッセージを送信する方法を示しています。 各メッセージの **messagenumber** プロパティの値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。 クォータについて詳しくは、「[Service Bus のクォータ][Service Bus quotas]」をご覧ください。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
サブスクリプションからメッセージを受信するには、**ServiceBusService** オブジェクトの **receive\_subscription\_message** メソッドを使用します。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

**peek\_lock** パラメーターが **False** に設定されていると、メッセージが読み取られるときにサブスクリプションから削除されます。 **peek\_lock** パラメーターを **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**peek\_lock** パラメーターが **True** に設定されている場合、受信処理が&2; 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの **delete** メソッドを呼び出して受信処理の第&2; 段階を完了します。 **delete** メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの **unlock** メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は **1 回以上の処理** と呼ばれます。つまり、すべてのメッセージが&1; 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する
トピックおよびサブスクリプションは永続的であり、[Azure ポータル][Azure portal]またはプログラムによって明示的に削除する必要があります。 次の例では、`mytopic` という名前のトピックを削除する方法を示しています。

```python
bus_service.delete_topic('mytopic')
```

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。 サブスクリプションは、個別に削除することもできます。 次のコードでは、`HighMessages` という名前のサブスクリプションを `mytopic` トピックから削除する方法を示しています。

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>次のステップ
これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション][Queues, topics, and subscriptions]。
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] のリファレンス。

[Azure portal]: https://portal.azure.com
[Python Azure package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus quotas]: service-bus-quotas.md 

