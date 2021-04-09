---
title: Azure Service Bus Explorer を使用して Service Bus に対してデータ操作を実行する (プレビュー)
description: この記事では、ポータルベースの Azure Service Bus Explorer を使用して Azure Service Bus データにアクセスする方法について説明します。
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738958"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Service Bus Explorer を使用して Service Bus に対してデータ操作を実行する (プレビュー)

## <a name="overview"></a>概要

Azure Service Bus を使用すると、送信側と受信側のクライアント アプリケーションは、使い慣れたポイントツーポイント (キュー) および発行とサブスクライブ (トピックサブスクリプション) セマンティクスを使用してビジネス ロジックを分離できます。

Azure Service Bus 名前空間に対して実行される操作は 2 種類あります 
   * 管理操作 - Service Bus 名前空間、キュー、トピック、およびサブスクリプションの作成、更新、削除。
   * データ操作 - キュー、トピック、サブスクリプションとの間のメッセージの送受信。

Azure Service Bus Explorer では、管理操作以外にまでポータル機能を拡張し、キュー、トピック、サブスクリプション (およびそれらの配信不能サブエンティティ) に対するデータ操作 (送信、受信、ピーク) をサポートしています。これらの操作は Azure portal から行うことができます。

> [!NOTE]
> この記事では、Azure portal 上の Azure Service Bus Explorer の機能について説明します。
>
> Azure Service Bus Explorer ツールは、コミュニティが所有する OSS ツールの [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) "***ではありません***"。
>

## <a name="prerequisites"></a>前提条件

Service Bus Explorer ツールを使用するには、次のタスクを実行する必要があります。 

- Azure Service Bus 名前空間をプロビジョニングします。
- メッセージを送受信するためのキューや、機能をテストするためのトピックとサブスクリプションを作成します。 キュー、トピック、およびサブスクリプションを作成する方法については、次の記事を参照してください。 
    - [クイックスタート - キューを作成する](service-bus-quickstart-portal.md)
    - [クイックスタート - トピックを作成する](service-bus-quickstart-topics-subscriptions-portal.md)
- 名前空間で次のいずれかのロールのメンバーであることを確認します。 
    - [Service Bus データ所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Contributor](../role-based-access-control/built-in-roles.md#contributor) 
    - [所有者](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Service Bus Explorer の使用

Azure Service Bus Explorer を使用するには、送信、ピーク、および受信操作を実行する Service Bus 名前空間に移動する必要があります。

キューに対して操作を実行する場合は、ナビゲーション メニューから **[キュー]** を選択します。 トピック (とそれに関連するサブスクリプション) に対して操作を実行する場合は、 **[トピック]** を選択します。 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="エンティティの選択":::

**[キュー]** または **[トピック]** を選択した後、特定のキューまたはトピックを選択します。

左側のナビゲーション メニューから **[Service Bus Explorer (プレビュー)]** を選択します

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="SB Explorer の左側のナビゲーション メニュー":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>キューまたはトピックに対するメッセージの送信

メッセージを **キュー** または **トピック** に送信するには、Service Bus Explorer の **_[送信]_** タブをクリックします。

ここでメッセージを作成するには、次の手順を実行します。 

1. **[コンテンツの種類]** として、'Text/Plain'、'Application/Xml'、'Application/Json' のいずれかを選択します。
2. メッセージの **[コンテンツ]** を追加します。 設定した **[コンテンツの種類]** と一致していることを確認します。
3. **[詳細プロパティ]** (省略可能) を設定します。[相関 ID]、[メッセージ ID]、[ラベル]、[ReplyTo]、[Time to Live (TTL)]、[スケジュールされたエンキュー時刻] (スケジュールされたメッセージの場合) などがあります。
4. **[カスタム プロパティ]** を設定します。ディクショナリ キーに対して設定された任意のユーザー プロパティを指定できます。

メッセージの作成が完了したら、[送信] をクリックします。

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="メッセージを作成する":::

送信操作が正常に完了したら、次のようにします。 

* キューに送信すると、 **[Active Messages]\(アクティブなメッセージ\)** メトリック カウンターが増えます。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* トピックに送信すると、メッセージがルーティングされた先のサブスクリプションの **[Active Messages]\(アクティブなメッセージ\)** メトリック カウンターが増えます。

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>キューからのメッセージの受信

Service Bus Explorer の受信機能では、一度に 1 つのメッセージを受信できます。 受信操作は、**ReceiveAndDelete** モードを使用して実行されます。

> [!IMPORTANT]
> Service Bus Explorer によって実行される受信操作は、"***破壊的受信***" であることに注意してください。つまり、メッセージが Service Bus Explorer ツールに表示されると、キューから削除されます。
>
> キューからメッセージを削除せずにメッセージを参照するには、"***ピーク***" 機能の使用を検討します。
>

キュー (またはその配信不能サブキュー) からメッセージを受信するには 

1. Service Bus Explorer の ***[受信]*** タブをクリックします。
2. メトリックをチェックして、 **[Active Messages]\(アクティブなメッセージ\)** または **[Dead-lettered Messages]\(配信不能メッセージ\)** を受信しているかどうかを確認します。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. * **[キュー]** _ または _ *_[Deadletter]\(配信不能\)_** サブキューから選択します。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. * **[受信]** _ ボタン、次に _ *_[はい]_** をクリックして、'受信して削除する' 操作を確定します。


受信操作が成功すると、次のようにメッセージの詳細がグリッドに表示されます。 グリッドからメッセージを選択すると、その詳細が表示されます。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Azure Service Bus Explorer の [キュー] ウィンドウのスクリーンショット。キュー内で選択されているアクティブ メッセージのメッセージ詳細が表示されています。":::


### <a name="peeking-a-message-from-a-queue"></a>キューからのメッセージのピーク

ピーク機能を使用すると、Service Bus Explorer を使用してキューまたは配信不能キューの上位 32 個のメッセージを表示できます。

1. キューのメッセージをピークするには、Service Bus Explorer の ***[ピーク]*** タブをクリックします。

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. メトリックをチェックして、 **[Active Messages]\(アクティブなメッセージ\)** または **[Dead-lettered Messages]\(配信不能メッセージ\)** をピークしているかどうかを確認します。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. 次に、* **[キュー]** _ または _ *_[配信不能]_** サブキューのいずれかを選択します。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. ***[ピーク]*** ボタンをクリックします。 

ピーク操作が完了すると、次のように最大 32 個のメッセージがグリッドに表示されます。 特定のメッセージの詳細を表示するには、グリッドから選択します。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> ピークは破壊的な操作ではないため、メッセージはキューから削除 "**されません**"。
>

### <a name="receiving-a-message-from-a-subscription"></a>サブスクリプションからのメッセージの受信

キューの場合と同様に、***[受信]*** 操作はサブスクリプション (またはその配信不能エンティティ) に対して実行できます。 ただし、サブスクリプションはトピックのコンテキスト内に存在するため、受信操作は、Service Bus Explorer に移動して特定のトピックに対して実行されます。

> [!IMPORTANT]
> Service Bus Explorer によって実行される受信操作は、"***破壊的受信***" であることに注意してください。つまり、メッセージが Service Bus Explorer ツールに表示されると、キューから削除されます。
>
> キューからメッセージを削除せずにメッセージを参照するには、"***ピーク***" 機能の使用を検討します。
>

1. * **[受信]** _ タブをクリックし、ドロップダウン セレクターから特定の _ *_[サブスクリプション]_** を選択します。

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. * **[サブスクリプション]** _ または _ *_[DeadLetter]\(配信不能\)_** サブエンティティから選択します。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. * **[受信]** _ ボタン、次に _ *_[はい]_** をクリックして、'受信して削除する' 操作を確定します。

受信操作が成功すると、受信したメッセージが次のようにグリッドに表示されます。 メッセージの詳細を表示するには、メッセージをクリックします。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Azure Service Bus Explorer の [受信] タブのスクリーンショット。受信されているアクティブ メッセージのメッセージ詳細が表示されています。":::

### <a name="peeking-a-message-from-a-subscription"></a>サブスクリプションからのメッセージのピーク

サブスクリプションまたは配信不能サブエンティティのメッセージを簡単に閲覧するには、サブスクリプションに対して ***ピーク*** 機能を利用する方法もあります。

1. * **[ピーク]** _ タブをクリックし、ドロップダウン セレクターから特定の _ *_[サブスクリプション]_** を選択します。

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. * **[サブスクリプション]** _ または _ *_[DeadLetter]\(配信不能\)_** サブエンティティから選択します。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. ***[ピーク]*** ボタンをクリックします。

ピーク操作が完了すると、次のように最大 32 個のメッセージがグリッドに表示されます。 特定のメッセージの詳細を表示するには、グリッドから選択します。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> ピークは破壊的な操作ではないため、メッセージはキューから削除 "**されません**"。
>

## <a name="next-steps"></a>次の手順

   * Service Bus の[キュー](service-bus-queues-topics-subscriptions.md#queues)と[トピック](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)について詳細を確認します
   * [Azure portal を使用した Service Bus キュー](service-bus-quickstart-portal.md)の作成について詳細を確認します
   * [Azure portal を使用した Service Bus のトピックとサブスクリプション](service-bus-quickstart-topics-subscriptions-portal.md)の作成について詳細を確認します