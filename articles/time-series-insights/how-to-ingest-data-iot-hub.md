---
title: IoT Hub のイベント ソースを追加する方法 ‐ Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 環境に IoT Hub イベント ソースを追加する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 85773ec66dbe567afcfd1afca1d14aa0119fb8f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464022"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Azure Time Series Insights 環境に IoT Hub イベント ソースを追加する

この記事では、Microsoft Azure portal を使用して、Azure IoT Hub からデータを読み取るイベント ソースを Azure Time Series Insights 環境に追加する方法を説明します。

> [!NOTE]
> この記事の手順は、Azure Time Series Insights Gen 1 環境と Azure Time Series Insight Gen 2 環境の両方に適用されます。

## <a name="prerequisites"></a>前提条件

* [Azure Time Series Insights 環境](./tutorial-set-up-environment.md)を作成する。
* [Microsoft Azure portal を使用して IoT Hub](../iot-hub/iot-hub-create-through-portal.md) を作成する。
* IoT Hub には、アクティブなメッセージ イベントが送信される必要があります。
* IoT Hub に Azure Time Series Insights 環境で使用する専用コンシューマー グループを作成します。 各 Azure Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 詳細については、[「Azure IoT Hub 開発者ガイド」](../iot-hub/iot-hub-devguide.md)をご覧ください。

### <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub へのコンシューマー グループの追加

アプリケーションは、コンシューマー グループを使用して、Azure IoT Hub からデータをプルします。 IoT Hub からデータを確実に読み取るには、この Azure Time Series Insights 環境でのみ使用される専用のコンシューマー グループを指定します。

新しいコンシューマー グループを IoT Hub に追加するには:

1. [Azure portal](https://portal.azure.com) で、ご利用の IoT Hub を探して開きます。

1. **[設定]** で **[組み込みのエンドポイント]** を選択し、 **[イベント]** エンドポイントを選択します。

   [![[組み込みのエンドポイント] ページで [イベント] ボタンを選択する](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. **[コンシューマー グループ]** にコンシューマー グループの一意の名前を入力します。 Azure Time Series Insights 環境で新しいイベント ソースを作成する場合に、これと同じ名前を使用します。

1. **[保存]** を選択します。

## <a name="add-a-new-event-source"></a>新しいイベント ソースの追加

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[すべてのリソース]** を選びます。 Azure Time Series Insights 環境を選択します。

1. **[設定]** で、 **[イベント ソース]** 、 **[追加]** の順に選択します。

   [![[イベント ソース] を選択し、[追加] ボタンを選択する](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. **[New event source] (新しいイベント ソース)** ウィンドウで、 **[イベント ソース名]** にこの Azure Time Series Insights 環境の一意の名前を入力します。 たとえば、「**イベント ストリーム**」と入力します。

1. **[ソース]** には **[IoT Hub]** を選択します。

1. **[インポート オプション]** の値を選択します。

   * サブスクリプションのいずれかに IoT Hub が既にある場合は、 **[Use IoT Hub from available subscriptions] (使用可能なサブスクリプションの Iot Hub を使用する)** を選択します。 このオプションが最も簡単な方法となります。

     [![[New event source] (新しいイベント ソース) ウィンドウでオプションを選択する](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * **[Use IoT Hub from available subscriptions] (使用可能なサブスクリプションの Iot Hub を使用する)** オプションに必要なプロパティについて次の表に説明します。

       [![[New event source] (新しいイベント ソース) ウィンドウ - [Use IoT Hub from available subscriptions] (使用可能なサブスクリプションの Iot Hub を使用する) オプションで設定するプロパティ](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | プロパティ | 説明 |
       | --- | --- |
       | サブスクリプション | 目的の IoT Hub が属しているサブスクリプション。 |
       | IoT Hub 名 | 選択した IoT Hub の名前。 |
       | IoT Hub ポリシー名 | 共有アクセス ポリシーを選択します。 共有アクセス ポリシーは [IoT Hub の設定] タブで見つかります。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続** アクセス許可が "*必要*" です。 |
       | IoT Hub ポリシー キー | キーが事前設定されています。 |

   * IoT Hub がサブスクリプションの外部であるか、または高度なオプションを選択する場合は、 **[IoT Hub 設定を手動で行う]** を選択します。

      次の表に、**IoT Hub 設定を手動で行う** ために必要なプロパティを示します。

       | プロパティ | 説明 |
       | --- | --- |
       | サブスクリプション ID | 目的の IoT Hub が属しているサブスクリプション。 |
       | Resource group | この IoT Hub が作成されたリソース グループ名です。 |
       | IoT Hub 名 | IoT Hub の名前です。 IoT Hub を作成したときに、IoT Hub の名前を入力しました。 |
       | IoT Hub ポリシー名 | 共有アクセス ポリシー。 共有アクセス ポリシーは [IoT Hub の設定] タブで作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続** アクセス許可が "*必要*" です。 |
       | IoT Hub ポリシー キー | Azure Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。 |

   * どちらのオプションも以下の構成オプションを共有します。

       | プロパティ | 説明 |
       | --- | --- |
       | IoT Hub コンシューマー グループ | IoT Hub からイベントを読み取るコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。 |
       | イベントのシリアル化の形式 | 現在のところ、JSON が唯一利用できるシリアル化形式です。 イベント メッセージは、この形式である必要があります。そうでないとデータを読み取ることができません。 |
       | タイムスタンプ プロパティ名 | この値を決定するには、IoT Hub に送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの **名前** です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の **イベント エンキュー時間** がイベント タイムスタンプとして使用されます。 |

1. IoT Hub に追加した、専用の Azure Time Series Insights コンシューマー グループ名を追加します。

1. **［作成］** を選択します

1. イベント ソースの作成後、Azure Time Series Insights は自動的に環境へのデータのストリーミングを開始します。

## <a name="next-steps"></a>次のステップ

* [データ アクセス ポリシーを定義](./concepts-access-policies.md)して、データをセキュリティ保護します。

* イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。

* [Azure Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境にアクセスします。
