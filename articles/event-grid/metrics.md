---
title: Azure Event Grid でサポートされているメトリック
description: この記事では、Azure Event Grid サービスによってサポートされている Azure Monitor メトリックについて説明します。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 06c43b1990efc977cae33ced3f66f02e2de0b9c4
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225174"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid でサポートされているメトリック
この記事では、名前空間ごとに分類された Event Grid メトリックの一覧を示します。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|Topic、EventSubscriptionName、DomainEventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|Topic、ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|トピック|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|ディメンションなし|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|ディメンションなし|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|ディメンションなし|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|メトリック|診断設定を使用したエクスポートが可能か?|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|---|
|DeadLetteredCount|はい|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|いいえ|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|はい|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|EventSubscriptionName|
|DestinationProcessingDurationInMs|いいえ|宛先処理継続時間|ミリ秒|Average|宛先処理継続時間 (ミリ秒単位)|EventSubscriptionName|
|DroppedEventCount|はい|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason、EventSubscriptionName|
|MatchedEventCount|はい|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|EventSubscriptionName|
|PublishFailCount|はい|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|PublishSuccessCount|はい|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishSuccessLatencyInMs|はい|成功した発行の待機時間|ミリ秒|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|
|UnmatchedEventCount|はい|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|

## <a name="next-steps"></a>次のステップ
次の記事を参照してください: [診断ログ](diagnostic-logs.md)
