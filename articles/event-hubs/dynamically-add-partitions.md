---
title: Azure Event Hubs でイベント ハブにパーティションを動的に追加する
description: この記事では、Azure Event Hubs でイベント ハブにパーティションを動的に追加する方法について説明します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625267"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Azure Event Hubs でイベント ハブ (Apache Kafka トピック) にパーティションを動的に追加する
Event Hubs は、パーティション化されたコンシューマー パターンを使用してメッセージ ストリーミングを実現します。このパターンでは、各コンシューマーはメッセージ ストリームの特定のサブセット (またはパーティション) のみを読み取ります。 このパターンでは、イベント処理能力を水平方向に拡張 (スケールアウト) することができ、キューおよびトピックでは利用できない、ストリームに重点を置いたその他の機能が利用できます。 パーティションは、イベント ハブで保持される順序付けされた一連のイベントです。 新しいイベントが到着すると、このシーケンスの末尾に追加されます。 パーティションの概要について詳しくは、「[パーティション](event-hubs-scalability.md#partitions)」を参照してください

パーティションの数は、イベント ハブの作成時に指定できます。 場合によっては、イベント ハブを作成した後で、パーティションの追加が必要になることもあります。 この記事では、既存のイベント ハブにパーティションを動的に追加する方法について説明します。 

> [!IMPORTANT]
> パーティションの動的な追加は、**専用** の Event Hubs クラスターでのみ利用できます。

> [!NOTE]
> Apache Kafka クライアントの場合、**イベント ハブ** は **Kafka トピック** に相当します。 Azure Event Hubs と Apache Kafka の間のマッピングの詳細については、「[Kafka と Event Hubs の概念のマッピング](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)」を参照してください


## <a name="update-the-partition-count"></a>パーティション数の更新
このセクションでは、イベント ハブのパーティション数を更新するための各種の方法 (PowerShell、CLI など) について説明します。

### <a name="powershell"></a>PowerShell
[Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) PowerShell コマンドを使用して、イベント ハブのパーティションを更新します。 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
イベント ハブでパーティションを更新するには、[`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) CLI コマンドを使用します。 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager テンプレート
Resource Manager テンプレートで `partitionCount` プロパティの値を更新し、テンプレートを再デプロイしてリソースを更新します。 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
`AlterTopics` API (たとえば、**kafka-topics** CLI ツール) を使用して、パーティション数を増やします。 詳細については、「[Kafka トピックの変更](http://kafka.apache.org/documentation/#basic_ops_modify_topic)」を参照してください。 

## <a name="event-hubs-clients"></a>Event Hubs クライアント
イベント ハブでパーティション数が更新されたときに、Event Hubs クライアントがどのように動作するかを見てみましょう。 

既存のハブにパーティションを追加すると、イベント ハブ クライアントがサービスから `MessagingException` を受け取ります。これは、エンティティ メタデータが変更されたことをクライアントに通知するものです (エンティティはイベント ハブで、メタデータはパーティション情報)。 クライアントは自動的に AMQP リンクを開き、変更されたメタデータ情報を取得します。 その後、クライアントは正常に動作します。

### <a name="senderproducer-clients"></a>センダー/プロデューサー クライアント
Event Hubs には、次の 3 つのセンダー オプションがあります。

- **パーティション センダー** – このシナリオでは、クライアントが直接パーティションにイベントを送信します。 パーティションは識別可能であり、イベントはそれらに直接送信することができますが、このパターンはお勧めしません。 パーティションを追加しても、このシナリオには影響しません。 新しく追加されたパーティションを検出できるように、アプリケーションを再起動することをお勧めします。 
- **パーティション キー センダー** – このシナリオでは、クライアントがキーを使用してイベントを送信します。これにより、そのキーに属するすべてのイベントが同じパーティションになります。 この場合、サービスはキーをハッシュし、対応するパーティションにルーティングします。 場合によっては、パーティション数の更新により、ハッシュが変更されて順序の問題が発生する可能性があります。 そのため、順序付けを考慮する必要がある場合は、パーティション数を増やす前に、アプリケーションで使用されるのがすべて既存のパーティションのイベントであることを確認してください。
- **ラウンドロビン センダー (既定)** – このシナリオでは、Event Hubs サービスがパーティション間でイベントをラウンドロビンします。負荷分散アルゴリズムも使用します。 Event Hubs サービスはパーティション数の変更を認識し、パーティション数が変更されてから数秒以内に、新しいパーティションに送信するようになります。

### <a name="receiverconsumer-clients"></a>受信者/コンシューマー クライアント
Event Hubs にはダイレクト レシーバーがあり、[イベント プロセッサ ホスト (旧 SDK)](event-hubs-event-processor-host.md) または [イベント プロセッサ (新 SDK)](event-processor-balance-partition-load.md) と呼ばれる簡単なコンシューマー ライブラリが用意されています。

- **ダイレクト レシーバー** – ダイレクト レシーバーは特定のパーティションをリッスンします。 このレシーバーのランタイム動作は、パーティションがイベントハブ用にスケールアウトされても影響を受けません。 ダイレクト レシーバーを使用するアプリケーションでは、新しいパーティションを取得し、それに応じて受信者を割り当てる必要があります。
- **イベント プロセッサ ホスト** - このクライアントでは、エンティティ メタデータは自動的に更新されません。 そのため、パーティション数の増加は感知されません。 イベント プロセッサ インスタンスが再作成されると、エンティティ メタデータのフェッチが発生し、それにより、新たに追加されたパーティションの新しい BLOB が作成されます。 既存の BLOB は影響を受けません。 新しく追加されたパーティションがすべてのインスタンスで認識され、コンシューマー間で負荷分散が正しく処理されるようにするには、すべてのイベント プロセッサ インスタンスを再起動することをお勧めします。

    以前のバージョンの .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) を使用している場合、チェックポイントのパーティション数がサービスからフェッチされたパーティション数と一致しない場合には、既存のチェックポイントがイベント プロセッサ ホストによって再起動時に削除されます。 この動作は、アプリケーションに影響を与える可能性があります。 

## <a name="apache-kafka-clients"></a>Apache Kafka クライアント
このセクションでは、イベント ハブのパーティション数が更新された際に、Azure Event Hubs の Kafka エンドポイントを使用する Apache Kafka クライアントがどのように動作するかについて説明します。 

Apache Kafka プロトコル経由で Event Hubs を使用する Kafka クライアントでは、AMQP プロトコルを使用するイベント ハブ クライアントと動作が異なります。 Kafka クライアントは、`metadata.max.age.ms` ミリ秒ごとにメタデータを更新します。 この値は、クライアント構成で指定します。 `librdkafka` のライブラリでも、同じ構成が使用されます。 メタデータの更新時には、サービスの変更 (パーティション数の増加を含む) がクライアントに通知されます。 構成の一覧については、「[Event Hubs に対応した Apache Kafka の構成](apache-kafka-configurations.md)」を参照してください。

### <a name="senderproducer-clients"></a>センダー/プロデューサー クライアント
プロデューサーは、生成されたレコードのセットごとに、パーティション ターゲットを送信要求に含めることを常に指示します。 そのため、すべてのパーティション分割は、ブローカーのメタデータに対するプロデューサーのビューを使用して、クライアント側で行われます。 新しいパーティションがプロデューサーのメタデータ ビューに追加されると、それらはプロデューサー要求で使用できるようになります。

### <a name="consumerreceiver-clients"></a>コンシューマー/受信者クライアント
コンシューマー グループのメンバーでメタデータ更新が実行され、新しく作成されたパーティションが取得されると、そのメンバーによってグループの再調整が開始されます。 その後、コンシューマー メタデータがすべてのグループ メンバーに対して更新され、割り当てられた再調整リーダーによって新しいパーティションが割り当てられます。

## <a name="recommendations"></a>Recommendations

- プロデューサー アプリケーションでパーティション キーを使用し、キー ハッシュによってパーティション内の順序付けを維持する場合は、パーティションを動的に追加することは推奨されません。 

    > [!IMPORTANT]
    > 既存のデータの順序は維持されますが、パーティションの追加によってパーティション数が変更された後にハッシュされたメッセージについては、パーティション ハッシュは維持されません。
- 次の場合は、既存のトピックまたはイベント ハブ インスタンスにパーティションを追加することをお勧めします。
    - 既定のイベント送信方法を使用する場合
     - Kafka の既定のパーティション分割戦略 (例: Sticky Assignor 戦略)


## <a name="next-steps"></a>次のステップ
パーティションの詳細については、「[パーティション](event-hubs-scalability.md#partitions)」をご覧ください。

