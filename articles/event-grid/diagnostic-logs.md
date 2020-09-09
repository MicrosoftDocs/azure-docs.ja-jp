---
title: Azure Event Grid - トピックまたはドメインの診断ログ
description: この記事では、Azure Event Grid のトピックまたはドメインの診断ログの概念に関する情報を提供します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c8f6734d9d43887d0eb0bb90bb08f727732feac3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116745"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Azure Event Grid のトピックまたはドメインの診断ログ
Event Grid ユーザーは、診断設定を使用して、Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースのいずれかで**発行および配信エラー** ログをキャプチャして表示することができます。 この記事では、ログのスキーマとログ エントリの例を示します。


## <a name="schema-for-publishdelivery-failure-logs"></a>発行および配信エラー ログのスキーマ

| プロパティ名 | データ型 | 説明 |
| ------------- | --------- | ----------- | 
| Time | DateTime | ログ エントリが生成された時刻 <p>**値の例:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | イベント サブスクリプションの名前 <p>**値の例:** "EVENTSUB1"</p> <p>このプロパティは、配信エラー ログにのみ存在します。</p>  |
| カテゴリ | String | ログのカテゴリの名前。 <p>**値の例:** "DeliveryFailures" または "PublishFailures" | 
| OperationName | String | エラーが発生したときに実行された操作の名前。<p>**サンプル値:** 配信に失敗した場合は、"Deliver"。 |
| Message | String | エラーの理由とその他の詳細情報を説明する、ユーザーのログ メッセージ。 |
| ResourceId | String | トピックまたはドメイン リソースのリソース ID<p>**サンプル値:** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>例

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>次のステップ
トピックまたはドメインの診断ログを有効にする方法については、[診断ログの有効化](enable-diagnostic-logs-topic.md)に関するページを参照してください。
