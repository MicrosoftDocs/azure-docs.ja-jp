---
title: クイック スタート:Python で Azure Service Bus のトピックとサブスクリプションを使用する
description: この記事では、Azure Service Bus トピックとサブスクリプションを作成する方法、メッセージをトピックに送信する方法、およびサブスクリプションからメッセージを受信する方法を示します。
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064385"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>クイック スタート:Python で Service Bus のトピックとサブスクリプションを使用する

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Python を Azure Service Bus のトピックおよびサブスクリプションと共に使用する方法について説明します。 サンプルでは、[Azure Python SDK][Azure Python package] パッケージを使用して次のことを行います。 

- トピックとトピックに対するサブスクリプションを作成する
- サブスクリプションのフィルターとルールを作成する
- メッセージをトピックに送信する 
- サブスクリプションからメッセージを受信する
- トピックとサブスクリプションを削除する

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[クイック スタート:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従って作成された Service Bus 名前空間。 このクイックスタートで後ほど使用するために、 **[共有アクセス ポリシー]** 画面から、名前空間名、共有アクセス キー名、および主キーの値をコピーします。 
- [Azure Python SDK][Azure Python package] パッケージがインストールされた Python 3.4 x 以上。 詳しくは、[Python インストール ガイド](/azure/developer/python/azure-sdk-install)に関する記事をご覧ください。

## <a name="create-a-servicebusservice-object"></a>ServiceBusService オブジェクトを作成する

**ServiceBusService** オブジェクトを使用して、トピックとトピックに対するサブスクリプションを操作できます。 プログラムを使用して Service Bus にアクセスするには、Python ファイルの先頭付近に、次の行を追加します。

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

次のコードを追加して、**ServiceBusService** オブジェクトを作成します。 `<namespace>`、`<sharedaccesskeyname>`、`<sharedaccesskeyvalue>` の部分は、Service Bus の名前空間名、Shared Access Signature (SAS) キー名、主キー値に置き換えます。 これらの値は、[Azure portal][Azure portal] 内の Service Bus 名前空間の **[共有アクセス ポリシー]** にあります。

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>トピックを作成する

次のコードでは、`create_topic` メソッドを使用して、`mytopic` という Service Bus トピックを既定の設定で作成します。

```python
bus_service.create_topic('mytopic')
```

トピック オプションを使用すると、メッセージの Time to Live (TTL) や最大トピック サイズなどの既定のトピック設定をオーバーライドできます。 次の例では、トピックの最大サイズが 5 GB で、既定のメッセージ TTL が 1 分である `mytopic` という名前のトピックを作成します。

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>サブスクリプションを作成する

トピックに対するサブスクリプションも **ServiceBusService** オブジェクトを使用して作成します。 サブスクリプションには、仮想キューに配信されるメッセージ セットを制限するフィルターを含めることができます。 フィルターを指定しない場合、新しいサブスクリプションでは既定の **MatchAll** フィルターが使用されます。これにより、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに格納されます。 次の例では、**MatchAll** フィルターを使用する、`mytopic` に対する `AllMessages` という名前のサブスクリプションを作成します。

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>サブスクリプションにフィルターを使用する

サブスクリプションに表示されるメッセージをフィルター処理するには、**ServiceBusService** オブジェクトの `create_rule` メソッドを使用します。 サブスクリプションを作成するときにルールを指定することも、既存のサブスクリプションにルールを追加することもできます。

最も柔軟なフィルターの種類は、SQL-92 のサブセットを使用する **SqlFilter** です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに基づいて動作します。 SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文に関するページを参照してください。

既定の **MatchAll** フィルターはすべての新しいサブスクリプションに自動的に適用されるため、フィルターを適用するサブスクリプションからそれを削除する必要があります。削除しないと、指定する他のすべてのフィルターは **MatchAll** によってオーバーライドされます。 既定のルールを削除するには、**ServiceBusService** オブジェクトの `delete_rule` メソッドを使用します。

次の例では、`HighMessageFilter` という名前の **SqlFilter** ルールを使用して、`mytopic` に対する `HighMessages` という名前のサブスクリプションを作成します。 `HighMessageFilter` ルールでは、カスタム `messageposition` プロパティが 3 を超えるメッセージのみが選択されます。

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

次の例では、`LowMessageFilter` という名前の **SqlFilter** ルールを使用して、`mytopic` に対する `LowMessages` という名前のサブスクリプションを作成します。 `LowMessageFilter` ルールでは、`messageposition` プロパティが 3 以下であるメッセージのみが選択されます。

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`、`HighMessages`、`LowMessages` のすべてが有効になっているため、`mytopic` に送信されるメッセージは常に `AllMessages` サブスクリプションの受信者に配信されます。 また、メッセージの `messageposition` プロパティ値に応じて、メッセージは `HighMessages` または `LowMessages` のサブスクリプションに選択的に配信されます。 

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する

メッセージを Service Bus トピックに送信するために、アプリケーションでは **ServiceBusService** オブジェクトの `send_topic_message` メソッドが使用されます。

次の例では、`mytopic` トピックに 5 つのテスト メッセージを送信します。 カスタム `messageposition` プロパティの値はループの反復回数に依存し、これによってメッセージを受信するサブスクリプションが決定されます。 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>メッセージ サイズの制限とクォータ

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持できるメッセージ数には上限がありませんが、1 つのトピックで保持されるメッセージの合計サイズには上限があります。 トピックのサイズは作成時に定義できます。上限は 5 GB です。 

クォータの詳細については、「[Service Bus のクォータ][Service Bus quotas]」を参照してください。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する

サブスクリプションからメッセージを受信するには、**ServiceBusService** オブジェクトの `receive_subscription_message` メソッドを使用します。 次の例では、`LowMessages` サブスクリプションからメッセージを受信し、読み取り時に削除します。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`receive_subscription_message` のオプションの `peek_lock` パラメーターでは、メッセージが読み取られたときに Service Bus によってそれらのメッセージがサブスクリプションから削除されるかどうかを決定します。 メッセージを受信するための既定のモードは *PeekLock* です。つまり、`peek_lock` が **True** に設定されており、メッセージをサブスクリプションから削除せずに読み取って (ピーク)、ロックします。 その後、各メッセージを明示的に完了して、サブスクリプションから削除する必要があります。

読み取り時にサブスクリプションからメッセージを削除するには、前の例のように、`peek_lock` パラメーターを **False** に設定します。 受信操作の一部としてメッセージを削除するのが最もシンプルなモデルであり、障害が発生した場合にアプリケーションがメッセージの欠落を許容できる場合は問題なく動作します。 この動作を理解するために、アプリケーションが受信要求を発行した後、それを処理する前にクラッシュしたというシナリオを考えてみましょう。 メッセージが受信中に削除された場合、アプリケーションが再起動してメッセージの読み取りを再開したときに、クラッシュ前に受信したメッセージは見落とされます。

アプリケーションがメッセージの紛失を許容できない場合、受信は 2 段階の操作になります。 PeekLock では、次に読み取られるメッセージを検索し、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 メッセージの処理または格納後、アプリケーションは、**Message** オブジェクトの `complete` メソッドを呼び出して受信処理の第 2 段階を完了します。  `complete` メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

ピーク ロックのシナリオを以下の例で説明します。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの `unlock` メソッドを呼び出すことができます。 Service Bus によりサブスクリプション内のメッセージのロックが解除され、メッセージが同じまたは別のコンシューマー側アプリケーションによって再度受信できる状態に変わります。

サブスクリプション内でロックされているメッセージにもタイムアウトがあります。 アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態になります。

メッセージが処理された後、`complete` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般に、この動作は "*1 回以上の処理*" と呼ばれます。 各メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理を許容できないシナリオでは、配信の試行をまたがって一定であるメッセージの **MessageId** プロパティを使用して、重複するメッセージ配信を処理できます。 

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する

トピックとサブスクリプションを削除するには、[Azure portal][Azure portal] または `delete_topic` メソッドを使用します。 次のコードでは、`mytopic` という名前のトピックが削除されます。

```python
bus_service.delete_topic('mytopic')
```

トピックを削除すると、そのトピックに対するすべてのサブスクリプションが削除されます。 サブスクリプションを個別に削除することもできます。 次のコードでは、`HighMessages` という名前のサブスクリプションが `mytopic` トピックから削除されます。

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

既定では、トピックとサブスクリプションは永続的であり、削除するまで存在します。 一定期間が経過した後にサブスクリプションを自動的に削除するには、サブスクリプションに対して [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) パラメーターを設定します。 

> [!TIP]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、Service Bus 名前空間に接続し、メッセージング エンティティを簡単に管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次のステップ

これで、Service Bus トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [キュー、トピック、サブスクリプション][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] のリファレンス

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md