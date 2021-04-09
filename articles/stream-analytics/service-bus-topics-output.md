---
title: Azure Stream Analytics からの Service Bus トピック出力
description: この記事では、Azure Stream Analytics の出力としての Service Bus トピックについて説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: bee6e5547ad78554467f23968230721ba029aba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451764"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Service Bus トピック出力

Service Bus キューには、送信者から受信者への一対一の通信方法が用意されています。 [Service Bus のトピック](/previous-versions/azure/hh367516(v=azure.100))では、一対多の形式の通信を行うことができます。

次の表に、Service Bus トピックの出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Service Bus トピックに出力するためにクエリ内で使用されるフレンドリ名です。 |
| Service Bus 名前空間 |一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成したとき、Service Bus 名前空間も作成されました。 |
| トピック名 |トピックは、イベント ハブやキューと類似するメッセージ エンティティです。 デバイスやサービスからイベント ストリームを収集するように設計されています。 トピックが作成されるときに、特定の名前も付けられます。 トピックに送信されるメッセージはサブスクリプションが作成されなければ使用できないため、トピックの下に 1 つ以上のサブスクリプションがあることを確認してください。 |
| トピック ポリシー名 |Service Bus トピックを作成するとき、トピックの **[構成]** タブで共有アクセス ポリシーを作成することもできます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| トピック ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV または JSON 形式を使用する場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| プロパティ列 | 省略可能。 ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列です。 この機能の詳細は、「[出力用のカスタム メタデータ プロパティ](#custom-metadata-properties-for-output)」セクションにあります。 |
| システム プロパティ列 | 省略可能。 ペイロードではなく送信メッセージに添付する必要がある、システムプロパティと対応する列名のキーと値のペア。 |

パーティション数は、[Service Bus SKU とサイズに基づいています](../service-bus-messaging/service-bus-partitioning.md)。 パーティション キーは、各パーティションに固有の整数値です。

## <a name="partitioning"></a>パーティション分割

パーティション分割が自動的に選択されます。 パーティション数は、[Service Bus SKU とサイズ](../service-bus-messaging/service-bus-partitioning.md)に基づいています。 パーティション キーは、各パーティションに固有の整数値です。 出力ライターの数は、出力トピック内のパーティションの数と同じです。

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズは、Standard レベルではメッセージあたり 256 KB、Premium レベルでは 1 MB です。 詳細については、[Service Bus の制限](../service-bus-messaging/service-bus-quotas.md)に関する記事を参照してください。 最適化するには、メッセージごとに 1 つのイベントを使用します。

## <a name="custom-metadata-properties-for-output"></a>出力用のカスタム メタデータ プロパティ

ご自分の送信メッセージにクエリ列をユーザー プロパティとして添付できます。 これらの列はペイロードに入りません。 これらのプロパティは、出力メッセージにディクショナリの形式で表示されます。 "*キー*" は列名で、"*値*" はプロパティ ディクショナリの列値です。 Record と Array を除き、すべての Stream Analytics データ型がサポートされています。

次の例では、`DeviceId` フィールドと `DeviceStatus` フィールドがメタデータに追加されています。

1. 次のクエリを使用します。

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. 出力のプロパティ列として `DeviceId,DeviceStatus` を構成します。

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="プロパティ列":::

次に示すのは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を利用して EventHub で検査される出力メッセージ プロパティの図です。

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="イベント カスタム プロパティ":::

## <a name="system-properties"></a>システム プロパティ

クエリ列を、[システム プロパティ](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties)として送信 Service Bus キューまたはトピック メッセージに添付できます。 これらの列はペイロードに含まれず、代わりに対応する BrokeredMessage [システム プロパティ](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) には、クエリ列の値が設定されます。
システムプロパティ (`MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`) はサポートされています。

これらの列の文字列値は、対応するシステム プロパティ値の型として解析され、解析エラーはデータ エラーとして扱われます。
このフィールドは、JSON オブジェクト形式で提供されます。 この形式の詳細は次のとおりです。

* 中かっこ {} で囲まれている。
* キーと値のペアで記述されている。
* キーと値は文字列である必要がある。
* キーはシステム プロパティの名前、値はクエリ列の名前となっている。
* キーと値は、コロンで区切って指定されている。
* キーと値のペアはそれぞれ、コンマで区切られている。

このプロパティの使用方法を次に示します。

* クエリ: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* システム プロパティ列: `{ "MessageId": "column1", "PartitionKey": "column2"}`

これにより、`column1` の値を持つ Service Bus キューメッセージに `MessageId` が設定され、PartitionKey が `column2` の値で設定されます。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)