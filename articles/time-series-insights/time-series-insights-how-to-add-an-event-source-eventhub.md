---
title: Azure Time Series Insights に Event Hub イベント ソースを追加する | Microsoft Docs
description: この記事では、Azure Event Hubs に接続されたイベント ソースを Time Series Insights 環境に追加する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: d4a80358535429f53b582abe8560757028159070
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557786"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Time Series Insights 環境にイベント ハブ イベント ソースを追加する

この記事では、Azure portal を使用し、Azure Event Hubs からデータを読み取るイベント ソースを Azure Time Series Insights 環境に追加する方法について説明します。

> [!NOTE]
> この記事で説明する手順は、Time Series Insights GA と Time Series Insights Preview 環境の両方に適用されます。

## <a name="prerequisites"></a>前提条件

- Time Series Insights 環境を作成します。 詳細については、[Azure Time Series Insights 環境の作成](./time-series-insights-update-create-environment.md)に関するページをご覧ください。
- イベント ハブを作成します。 Event Hubs の詳細については、[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)方法に関するページを参照してください。
- イベント ハブには、アクティブなメッセージ イベントが送信される必要があります。 詳細については、「[.NET Framework を使用して Azure Event Hubs にイベントを送信する](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)」をご覧ください。
- Time Series Insights 環境で使用する専用コンシューマー グループをイベント ハブで作成します。 各 Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 イベント ハブごとに 20 個のコンシューマー グループという制限があります。 詳細については、[Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)に関するページをご覧ください。

### <a name="add-a-consumer-group-to-your-event-hub"></a>イベント ハブにコンシューマー グループを追加する

アプリケーションではコンシューマー グループを使用して Azure Event Hubs からデータを引き出します。 イベント ハブから確実にデータを読み取るため、この Time Series Insights 環境でのみ使用する専用のコンシューマー グループを用意します。

イベント ハブに新しいコンシューマー グループを追加するには:

1. Azure portal で、イベント ハブを見つけて開きます。

1. **[エンティティ]** で **[コンシューマー グループ]** を選択し、**[コンシューマー グループ]** を選択します。

   ![イベント ハブ - コンシューマー グループの追加](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

1. **[コンシューマー グループ]** ページで、**[名前]** に新しい一意の値を入力します。  Time Series Insights 環境で新しいイベント ソースを作成するとき、この同じ名前を使用します。

1. **作成**を選択します。

## <a name="add-a-new-event-source"></a>新しいイベント ソースの追加

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 既存の Time Series Insights 環境を見つけます。 左のメニューで **[すべてのリソース]** を選択し、自分の Time Series Insights 環境を選択します。

1. **[環境トポロジ]** で **[イベント ソース]** を選択し、**[追加]** を選択します。

   ![[イベント ソース] で [追加] ボタンを選択します](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

1. **[イベント ソース名]** に値を入力します。この値はこの Time Series Insights 環境に一意であり、たとえば、**event-stream** とします。

1. **[ソース]** で **[イベント ハブ]** を選択します。

1. **[インポート オプション]** に適切な値を選択します。
   - サブスクリプションのいずれかに既にイベント ハブが存在する場合、**[利用可能なサブスクリプションからのイベント ハブを使用する]** を選択します。 このオプションが最も簡単な方法となります。
   - イベント ハブがサブスクリプションの外部であるか、高度なオプションを選択する場合、**[イベント ハブ設定を手動で行う]** を選択します。

   ![[新しいイベント ソース] ウィンドウで、最初の 3 つのパラメーターの値を入力します](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

1. **[利用可能なサブスクリプションからのイベント ハブを使用する]** オプションに必要なプロパティについて次の表で説明します。

   ![サブスクリプションとイベント ハブの詳細](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | このイベント ハブが作成されたサブスクリプションを選択します。
   | Service Bus 名前空間 | イベント ハブが含まれている Azure Service Bus 名前空間を選択します。
   | イベント ハブ名 | イベント ハブの名前を選択します。
   | イベント ハブ ポリシー名 | 共有アクセス ポリシーを選択します。 イベント ハブの **[構成]** タブで共有アクセス ポリシーを作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
   | イベント ハブ ポリシー キー | キーの値は事前入力されることがあります。
   | イベント ハブ コンシューマー グループ | イベント ハブからイベントを読み取るコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。 |
   | イベントのシリアル化の形式 | 現在のところ、JSON が唯一利用できるシリアル化形式です。 イベント メッセージはこの形式にする必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、イベント ハブに送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の**イベント エンキュー時間**がイベント タイムスタンプとして使用されます。 |

1. **[イベント ハブ設定を手動で行う]** オプションに必要なプロパティについて次の表で説明します。

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | このイベント ハブが作成されたサブスクリプション。
   | リソース グループ | このイベント ハブが作成されたリソース グループ。
   | Service Bus 名前空間 | Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成したとき、Service Bus 名前空間も作成されました。
   | イベント ハブ名 | イベント ハブの名前。 イベント ハブを作成したときに、固有の名前を設定しています。
   | イベント ハブ ポリシー名 | 共有アクセス ポリシー。 イベント ハブの **[構成]** タブで共有アクセス ポリシーを作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
   | イベント ハブ ポリシー キー | Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。
   | イベント ハブ コンシューマー グループ | イベント ハブからイベントを読み取るコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。
   | イベントのシリアル化の形式 | 現在のところ、JSON が唯一利用できるシリアル化形式です。 イベント メッセージはこの形式にする必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、イベント ハブに送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の**イベント エンキュー時間**がイベント タイムスタンプとして使用されます。 |

1. イベント ハブに追加した専用 Time Series Insights コンシューマー グループ名を追加します。

1. **作成**を選択します。

   ![[作成] を選択します](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   イベント ソースの作成後、Time Series Insights は自動的に環境へのデータのストリーミングを開始します。

## <a name="next-steps"></a>次の手順

* [データ アクセス ポリシーを定義](time-series-insights-data-access.md)して、データをセキュリティ保護します。
* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
* [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境にアクセスします。
