---
title: Azure Event Grid メトリックの表示とアラートの設定
description: この記事では、Azure Portal を使用して Azure Event Grid のトピックとサブスクリプションのメトリックを表示し、それらのアラートを作成する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114885"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid メッセージ配信の監視 
この記事では、ポータルを使用して Event Grid のトピックとサブスクリプションのメトリックを表示し、それらのアラートを作成する方法について説明します。 

## <a name="metrics"></a>メトリック

ポータルでは、イベント メッセージ配信の状態のメトリックを表示します。

トピックについて、メトリックの一部を次に示します。

* **[発行成功]** : イベントは正常にトピックに送信され、2xx 応答で処理されました。
* **[発行失敗]** : イベントはトピックに送信されましたが、エラー コードにより拒否されました。
* **[一致しない]** : イベントはトピックに正常に発行されましたが、イベント サブスクリプションと一致しませんでした。 イベントは破棄されました。

サブスクリプションについて、メトリックの一部を次に示します。

* **[配信成功]** : イベントは正常にサブスクリプションのエンドポイントに配信され、2xx 応答を受信しました。
* **[配信失敗]** : サービスで配信が試行され、イベント ハンドラーから成功の 2xx コードを返されないたびに、 **[配信失敗]** カウンターが増加します。 同じイベントを複数回配信しようとして失敗した場合は、エラーが発生するたびに、 **[配信失敗]** カウンターが増加します。
* **[期限切れのイベント]** : イベントは配信されず、すべての再試行が送信済みになりました。 イベントは破棄されました。
* **[一致するイベント]** : トピックにあるイベントがイベント サブスクリプションと一致しました。

    > [!NOTE]
    > メトリックの完全な一覧については、「[Azure Event Grid でサポートされているメトリック](metrics.md)」を参照してください。

## <a name="view-custom-topic-metrics"></a>カスタム トピック メトリックの表示

カスタム トピックを発行済みの場合は、そのトピックのメトリックを表示できます。 

1. [Azure Portal](https://portal.azure.com/)にサインインします。
2. トピックの検索バーに「**Event Grid トピック**」と入力し、ドロップ ダウン リストから **[Event Grid トピック]** を選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid トピック を検索して選択する":::
3. トピックの一覧から対象のカスタム トピックを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="カスタム トピックの選択":::
4. カスタム イベント トピックのメトリックが **[Event Grid トピック]** ページに表示されます。 次の図では、リソース グループ、サブスクリプションなどを示す **[Essentials]** セクションが最小化されています。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="イベント メトリックの表示":::

サポートされているメトリックを使用してグラフを作成するには、 **[Event Grid トピック]** ページの **[メトリック]** タブを使用します。

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="トピック - メトリック ページ":::

メトリックについて詳しくは、「[Azure Monitor のメトリック](../azure-monitor/platform/data-platform-metrics.md)」をご覧ください。

たとえば、**発行されたイベント** メトリックのメトリック グラフを確認します。

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="発行されたイベント メトリック":::


## <a name="view-subscription-metrics"></a>サブスクリプションのメトリックを表示する
1. 前のセクションの手順に従って、 **[Event Grid トピック]** ページに移動します。 
2. 次の例に示すように、下部のペインからサブスクリプションを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="[イベント サブスクリプション] を選択する":::    

    また、Azure Portal の検索バーで「**Event Grid サブスクリプション**」を検索し、 **[トピックの種類]** 、 **[サブスクリプション]** 、 **[場所]** を選択してイベント サブスクリプションを確認することもできます。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="[Event Grid サブスクリプション] ページからイベント サブスクリプションを選択する":::        

    カスタム トピックの場合は、 **[トピックの種類]** として **[Event Grid トピック]** を選択します。 システム トピックについては、Azure リソースの種類 (**ストレージ アカウント (BLOB、GPv2)** など) を選択します。 
3. サブスクリプションのホームページで、サブスクリプションのメトリックをグラフで確認します。 過去 1 時間、6 時間、12 時間、1 日、7 日間、または 30 日間の **[全般]** 、 **[エラー]** 、 **[待機時間]** 、 **[配信不能]** メトリックを確認できます。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="サブスクリプションのホーム ページのメトリック":::    

## <a name="view-system-topic-metrics"></a>システム トピックのメトリックを表示する

1. [Azure Portal](https://portal.azure.com/)にサインインします。
2. トピックの検索バーに「**Event Grid システム トピック**」と入力し、ドロップ ダウン リストから **[Event Grid システム トピック]** を選択します。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid システム トピックを検索して選択する":::
3. トピックの一覧から対象のシステム トピックを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="システム トピックの選択":::
4. システム トピックのメトリックが **[Event Grid システム トピック]** ページに表示されます。 次の図では、リソース グループ、サブスクリプションなどを示す **[Essentials]** セクションが最小化されています。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="概要ページでシステム トピックのメトリックを表示する":::

サポートされているメトリックを使用してグラフを作成するには、 **[Event Grid トピック]** ページの **[メトリック]** タブを使用します。

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="システム トピック - メトリック ページ":::

メトリックについて詳しくは、「[Azure Monitor のメトリック](../azure-monitor/platform/data-platform-metrics.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- メトリックとアクティビティ ログ操作に関するアラートを作成する方法については、「[アラートの設定](set-alerts.md)」を参照してください。
- イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
