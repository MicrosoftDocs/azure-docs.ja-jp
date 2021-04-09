---
title: Azure Event Grid - トピックまたはドメインの診断ログを有効にする
description: この記事では、Azure Event Grid トピックの診断ログを有効にする手順について説明します。
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576453"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid のトピックまたはドメインの診断ログを有効にする
この記事では、Event Grid のトピックまたはドメインの診断設定を有効にする手順について説明します。  これらの設定を行うと、**発行エラーと配信エラー** のログをキャプチャして表示することができます。 

## <a name="prerequisites"></a>前提条件

- プロビジョニングされた Event Grid トピック
- 診断ログのキャプチャに使用するプロビジョニングされた宛先。 Event Grid トピックと同じ場所に、次のいずれかの送信先を指定できます。
    - Azure ストレージ アカウント
    - イベント ハブ
    - Log Analytics ワークスペース

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>カスタム トピックの診断ログを有効にする

> [!NOTE]
> 次の手順では、トピックの診断ログを有効にする手順について説明します。 ドメインの診断ログを有効にする手順もよく似ています。 手順 2 で、Azure portal 内の Event Grid **ドメイン** に移動します。  

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 診断ログ設定を有効にするイベント グリッド トピックに移動します。 
    1. 上部の検索バーで、「**Event Grid Topics (Event Grid トピック)** 」を検索します。 
    
        ![カスタム トピックを検索する](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. 診断設定を構成するリストから、 **[トピック]** を選択します。 
1. 左側のメニューの **[監視]** の下にある **[診断設定]** を選択します。
1. **[診断設定]** ページで、 **[Add New Diagnostic Setting]\(新しい診断設定の追加\)** を選択します。 
    
    ![[診断設定を追加する] ボタン](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 診断設定の **[名前]** を指定します。 
6. **[ログ]** セクションの **[DeliveryFailures]** および **[PublishFailures]** オプションを選択します。 
    ![エラーを選択する](./media/enable-diagnostic-logs-topic/log-failures.png)
7. ログの 1 つまたは複数のキャプチャ先を有効にしてから、前に作成したキャプチャ リソースを選択して構成します。 
    - **[ストレージ アカウントへのアーカイブ]** を選択した場合は、 **[ストレージ アカウント - 構成]** を選択してから、Azure サブスクリプションのストレージ アカウントを選択します。 

        ![[Azure ストレージ アカウントにアーカイブする] チェックボックスがオンになっており、ストレージ アカウントが選択されている [診断設定] ページ示すスクリーンショット。](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **[イベント ハブへのストリーム]** を選択した場合は、 **[イベント ハブ - 構成]** を選択してから、Event Hubs 名前空間、イベント ハブ、およびアクセス ポリシーを選択します。 
        ![[イベント ハブへのストリーム] チェックボックスがオンになっている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **[Log Analytics への送信]** を選択した場合は、Log Analytics ワークスペースを選択します。
        ![[Log Analytics への送信] チェックボックスがオンになっている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. **[保存]** を選択します。 次に、右上隅にある **[X]** を選択して、ページを閉じます。 
9. 次に **[診断設定]** ページに戻り、 **[診断設定]** テーブルに新しいエントリが表示されていることを確認します。 
    ![[診断設定] テーブルの新しいエントリが強調表示されている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     また、トピックのすべてのメトリックの収集を有効にすることもできます。 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>システム トピックの診断ログを有効にする

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 診断ログ設定を有効にするイベント グリッド トピックに移動します。 
    1. 上部の検索バーで、「**Event Grid System Topics (Event Grid システム トピック)** 」を検索します。 
    
        ![システム トピックの検索](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 診断設定を構成する **[システム トピック]** を選択します。 
    
        ![システム トピックを選択する](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 左側のペインで **[監視]** の下の **[診断設定]** を選択してから、 **[Add diagnostic setting]\(診断設定の追加\)** を選択します。 

    ![診断設定の追加 - ボタン](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 診断設定の **[名前]** を指定します。 
7. **[ログ]** セクションで、 **[DeliveryFailures]\(配信エラー\)** を選択します。 
    ![配信エラーを選択する](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. ログの 1 つまたは複数のキャプチャ先を有効にしてから、前に作成したキャプチャ リソースを選択して構成します。 
    - **[Log Analytics への送信]** を選択した場合は、Log Analytics ワークスペースを選択します。
        ![Log Analytics への送信](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - **[ストレージ アカウントへのアーカイブ]** を選択した場合は、 **[ストレージ アカウント - 構成]** を選択してから、Azure サブスクリプションのストレージ アカウントを選択します。 

        ![Azure ストレージ アカウントにアーカイブする](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - **[イベント ハブへのストリーム]** を選択した場合は、 **[イベント ハブ - 構成]** を選択してから、Event Hubs 名前空間、イベント ハブ、およびアクセス ポリシーを選択します。 
        ![イベント ハブへのストリーム](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. **[保存]** を選択します。 次に、右上隅にある **[X]** を選択して、ページを閉じます。 
9. 次に **[診断設定]** ページに戻り、 **[診断設定]** テーブルに新しいエントリが表示されていることを確認します。 
    ![一覧の診断設定](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     また、システム トピックのすべての **メトリック** の収集を有効にすることもできます。

    ![システム トピック - すべてのメトリックを有効にする](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Azure Storage アカウントの診断ログの表示 

1. キャプチャ先としてストレージ アカウントを有効にすると、Event Grid が診断ログの出力を開始します。 ストレージ アカウントで、**insights-logs-deliveryfailures** および **insights-logs-publishfailures** という名前の新しいコンテナーが表示されます。 

    ![ストレージ - 診断ログ用のコンテナー](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. いずれかのコンテナー内を移動すると、JSON 形式の BLOB が見つかります。 このファイルには、配信エラーまたは発行エラーのログ エントリが含まれています。 ナビゲーション パスは、Event Grid トピックの **ResourceId** と、ログ エントリが生成されたときのタイムスタンプ (分単位) を表します。 ダウンロード可能な BloB/JSON ファイルは、最終的に次のセクションで説明するスキーマに準拠しています。 

    [ ![ストレージ内の JSON ファイル](./media/enable-diagnostic-logs-topic/select-json.png) ](./media/enable-diagnostic-logs-topic/select-json.png)
3. 次の例のようなコンテンツが JSON ファイルで確認できます。 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>次のステップ
ログ スキーマと、トピックまたはドメインの診断ログに関するその他の概念情報については、「[診断ログ](diagnostic-logs.md)」を参照してください。
