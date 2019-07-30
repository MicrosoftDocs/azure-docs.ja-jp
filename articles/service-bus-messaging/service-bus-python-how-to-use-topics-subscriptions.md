---
title: Azure Service Bus トピックの使用方法 (Python) | Microsoft Docs
description: Python から Azure Service Bus のトピックとサブスクリプションを使用する方法を説明します。
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: d294ceaaf77175a3010131b18864b71c7b26b88b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360819"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Python で Service Bus のトピックとサブスクリプションを使用する方法

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Service Bus のトピックとサブスクリプションの使用方法について説明します。 サンプルは Python で記述され、[Azure Python SDK パッケージ][Azure Python package]を使用しています。 紹介するシナリオは次のとおりです。

- キュー、トピック、およびサブスクリプションを作成する 
- サブスクリプション フィルターを作成する 
- メッセージをトピックに送信する 
- サブスクリプションからメッセージを受信する
- トピックとサブスクリプションを削除する

## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 「[Quickstart:Use the Azure portal to create a Service Bus topic and subscriptions to the topic](service-bus-quickstart-topics-subscriptions-portal.md)」(クイック スタート: Azure portal を使用して Service Bus トピックとその中に含まれるサブスクリプションを作成する) の手順に従って、Service Bus の**名前空間**を作成し、**接続文字列**を取得します。

    > [!NOTE]
    > このクイック スタートでは、**Python** を使用して **トピック**とその中に含まれる**サブスクリプション**を作成します。 
3. [Azure Python パッケージ][Azure Python package]をインストールします。 「[Python インストール ガイド](../python-how-to-install.md)」を参照してください。

## <a name="create-a-topic"></a>トピックを作成する

**ServiceBusService** オブジェクトを使用すると、トピックを操作できます。 プログラムを使用して Service Bus にアクセスするすべての Python ファイルの先頭付近に次のコードを追加します。

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
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

`create_topic` メソッドは追加のオプションもサポートしています。これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの設定をオーバーライドできます。 次の例では、最大トピック サイズを 5 GB に、有効期間 (TTL) を 1 分に設定します。

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>サブスクリプションを作成する

トピックのサブスクリプションも **ServiceBusService** オブジェクトで作成します。 サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

> [!NOTE]
> 既定では、サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションがサブスクライブされているトピックが削除されるまで存在し続けます。
> 
> [auto_delete_on_idle プロパティ](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python)を設定することで、サブスクリプションを自動的に削除することができます。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成

新しいサブスクリプションの作成時にフィルターが指定されていない場合は､**MatchAll** フィルター (既定) が使用されます｡ **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、`AllMessages` という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージを指定するフィルターを定義することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **SqlFilter** です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明を参照してください。

**ServiceBusService** オブジェクトの **create\_rule** メソッドを使用して、サブスクリプションにフィルターを追加できます。 このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

> [!NOTE]
> 既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるため、最初に既定のフィルターを削除する必要があります。削除しなければ、指定された他のすべてのフィルターは **MatchAll** によってオーバーライドされます。 既定のルールを削除するには、**ServiceBusService** オブジェクトの `delete_rule` メソッドを使用します。
> 
> 

次の例では、`HighMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム `messagenumber` プロパティが 3 を超えるメッセージのみが選択されます。

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

同様に、次の例では `LowMessages` という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、`messagenumber` プロパティが 3 以下のメッセージのみが選択されます。

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

メッセージを Service Bus トピックに送信するには、アプリケーションで **ServiceBusService** オブジェクトの `send_topic_message` メソッドを使用する必要があります。

次の例では、`mytopic` トピックに 5 件のテスト メッセージを送信する方法を示しています。 各メッセージの `messagenumber` プロパティの値がループの反復回数に応じて変化します (これによってメッセージを受信するサブスクリプションが決定されます)。

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。 クォータの詳細については、「[Service Bus のクォータ][Service Bus quotas]」を参照してください。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する

サブスクリプションからメッセージを受信するには、**ServiceBusService** オブジェクトの `receive_subscription_message` メソッドを使用します。

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`peek_lock` パラメーターが **False** に設定されていると、メッセージが読み取られるときにサブスクリプションから削除されます。 `peek_lock` パラメーターを **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の過程で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 この動作を理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークしているため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされます。

`peek_lock` パラメーターが **True** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの `delete` メソッドを呼び出して受信処理の第 2 段階を完了します。 `delete` メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの `unlock` メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によりサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`delete` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 この動作は､しばしば "1 回以上の処理\*" と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、状況によっては、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 これを行うには、メッセージの **MessageId** プロパティを使用できます。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する

トピックとサブスクリプションは、[auto_delete_on_idle プロパティ](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python)が設定されている場合を除き、永続的です。 これらは [Azure portal][Azure portal] を通じて、またはプログラムで削除できます。 次の例では、`mytopic` という名前のトピックを削除する方法を示しています。

```python
bus_service.delete_topic('mytopic')
```

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。 サブスクリプションは、個別に削除することもできます。 次のコードでは、`HighMessages` という名前のサブスクリプションを `mytopic` トピックから削除する方法を示しています。

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション][Queues, topics, and subscriptions]。
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] のリファレンス

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
