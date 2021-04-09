---
title: Azure Stream Analytics からの Event Hubs 出力
description: この記事では、Azure Stream Analytics から Azure Event Hubs にデータを出力する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 02abdd752528ce28642b6228648062ed961d5ae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452392"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Event Hubs 出力

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) サービスは、スケーラブルな発行-サブスクライブ型イベント インジェスターです。 1 秒あたり数百万のイベントを収集できます。 イベント ハブを出力として使用するのは、たとえば、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力になるときです。 最大メッセージ サイズとバッチ サイズの最適化の詳細については、「[出力バッチ サイズ](#output-batch-size)」セクションを参照してください。

## <a name="output-configuration"></a>出力の構成

次の表に、イベント ハブのデータ ストリームを出力として構成するために必要なパラメーターを示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | クエリの出力をこのイベント ハブに出力するためにクエリ内で使用されるフレンドリ名です。 |
| イベント ハブの名前空間 | 一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成すると、イベント ハブの名前空間も作成されます。 |
| イベント ハブ名 | ご自分のイベント ハブ出力の名前です。 |
| イベント ハブ ポリシー名 | 共有アクセス ポリシーです。イベント ハブの **[構成]** タブで作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| イベント ハブ ポリシー キー | イベント ハブの名前空間へのアクセスを認証するために使用される共有アクセス キーです。 |
| パーティション キー列 | 省略可能。 イベント ハブ出力のパーティション キーが含まれる列です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。 |
| エンコード | CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| Format | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[改行区切り]** を選択した場合、JSON は一度に 1 オブジェクトずつ読み取られます。 コンテンツ全体は、それ自体では有効な JSON になりません。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。  |
| プロパティ列 | 省略可能。 ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列です。 この機能の詳細は、「[出力用のカスタム メタデータ プロパティ](#custom-metadata-properties-for-output)」セクションにあります。 |

## <a name="partitioning"></a>パーティション分割

パーティション分割はパーティションの配置によって異なります。 イベント ハブ出力のパーティション キーが上流の (以前の) クエリ ステップと等間隔で配置されている場合、ライターの数はイベント ハブ出力のパーティションの数と同じになります。 各ライターは、[EventHubSender クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubsender)を使用して、特定のパーティションにイベントを送信します。 イベント ハブ出力のパーティション キーが上流の (以前の) クエリ ステップと一致していない場合、ライターの数はその前のステップのパーティションの数と同じになります。 各ライターは、**EventHubClient** の [SendBatchAsync クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync)を使用して、すべての出力パーティションにイベントを送信します。 

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズは、メッセージあたり 256 KB または 1 MB です。 詳細については、[Event Hubs の制限](../event-hubs/event-hubs-quotas.md)に関するページを参照してください。 入出力のパーティション分割が揃っていない場合、各イベントは個別に `EventData` に格納され、最大メッセージ サイズまでのバッチで送信されます。 これは、[カスタム メタデータ プロパティ](#custom-metadata-properties-for-output)が使用されている場合にも発生します。 入出力のパーティション分割が揃っている場合、複数のイベントが最大メッセージ サイズまで 1 つの `EventData` インスタンスに格納され、送信されます。

## <a name="custom-metadata-properties-for-output"></a>出力用のカスタム メタデータ プロパティ

ご自分の送信メッセージにクエリ列をユーザー プロパティとして添付できます。 これらの列はペイロードに入りません。 これらのプロパティは、出力メッセージにディクショナリの形式で表示されます。 "*キー*" は列名で、"*値*" はプロパティ ディクショナリの列値です。 Record と Array を除き、すべての Stream Analytics データ型がサポートされています。

次の例では、`DeviceId` フィールドと `DeviceStatus` フィールドがメタデータに追加されています。

1. 次のクエリを使用します。

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. 出力のプロパティ列として `DeviceId,DeviceStatus` を構成します。

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="プロパティ列":::

次に示すのは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を利用して EventHub で検査される出力メッセージ プロパティの図です。

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="イベント カスタム プロパティ":::

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics ジョブからマネージド ID を使用してイベント ハブにアクセスする (プレビュー)](event-hubs-managed-identity.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
