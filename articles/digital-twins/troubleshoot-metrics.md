---
title: Azure Monitor でメトリックを表示する
titleSuffix: Azure Digital Twins
description: Azure Monitor で Azure Digital Twins メトリックを表示する方法について確認します。
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ecd402c30fee63ad594fff5e4fdc3b1610fe7e4e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003883"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Azure Digital Twins のトラブルシューティング: メトリック

この記事で取り上げるメトリックにより、Azure サブスクリプション内の Azure Digital Twins リソースの状態に関する情報が提供されます。 Azure Digital Twins メトリックは、Azure Digital Twins サービスとそれに接続されているリソースの全体的な正常性を評価するのに役立ちます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

メトリックは既定で有効になっています。 Azure Digital Twins メトリックは [Azure portal](https://portal.azure.com) で表示できます。

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure Digital Twins メトリックを表示する方法

1. Azure Digital Twins インスタンスを作成します。 Azure Digital Twins インスタンスを設定する方法については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-scripted.md)に関するページを参照してください。

2. [Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスを見つけます (ポータルの検索バーに名前を入力して、そのページを開くことができます)。 

    インスタンスのメニューから、 **[メトリック]** を選択します。
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

    このページには、Azure Digital Twins インスタンスのメトリックが表示されます。 また、リストから表示したいメトリックを選択して、メトリックのカスタム ビューを作成することもできます。
    
3. メトリック データを Event Hubs エンドポイントまたは Azure Storage のアカウントに送信するように選択するには、メニューから **[診断設定]** 、 **[診断設定の追加]** の順に選択します。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが示されているスクリーンショット":::

    このプロセスの詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。

4. メニューから **[アラート]** を選択し、 **[+ 新しいアラート ルール]** を選択することで、メトリック データのアラートを設定できます。
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="[アラート] ページと追加するボタンを示すスクリーンショット":::

    このプロセスの詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins メトリックとその使用方法

Azure Digital Twins には、インスタンスとその関連リソースの正常性の概要を提供するいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、インスタンスの状態をより大局的に把握することもできます。 

次の表は、各 Azure Digital Twins インスタンスによって追跡されるメトリックと、各メトリックがインスタンスの全体的な状態にどのように関連しているかを示しています。

#### <a name="api-request-metrics"></a>API 要求のメトリック

API 要求に関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 要求 (プレビュー) | Count | 合計 | Digital Twins の読み取り、書き込み、削除、およびクエリ操作に対して行われた API 要求の数。 |  認証、 <br>操作、 <br>プロトコル、 <br>状態コード、 <br>状態コード クラス、 <br>状態テキスト |
| ApiRequestsFailureRate | API Requests Failure Rate (API 要求の失敗率) (プレビュー) | Percent | Average | インスタンスに対してサービスが受信する API 要求のうち、Digital Twins の読み取り、書き込み、削除、クエリ操作に対して内部エラー (500) 応答コードを返す API 要求の割合。 | 認証、 <br>操作、 <br>プロトコル、 <br>状態コード、 <br>状態コード クラス、 <br>状態テキスト
| ApiRequestsLatency | API Requests Latency (API 要求の待機時間) (プレビュー) | ミリ秒 | Average | API 要求の応答時間。 これは、Azure Digital Twins が要求を受け取ってから、サービスが Digital Twins の読み取り、書き込み、削除、クエリ操作の成功/失敗の結果を送信するまでの時間を指します。 | 認証、 <br>操作、 <br>Protocol |

#### <a name="billing-metrics"></a>課金メトリック

課金に関連するメトリック:

>[!NOTE]
> プレビュー期間中は、**課金されません**。 これらのメトリックは選択可能なリストに表示されますが、プレビュー期間中は適用されません。また、サービスがプレビューの範囲を超えない限り、0 のままになります。

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Billing API Operations (課金 API 操作) (プレビュー) | Count | 合計 | Azure Digital Twins サービスに対して行われたすべての API 要求の数の課金メトリック。 | Meter Id |
| BillingMessagesProcessed | Billing Messages Processed (処理されたメッセージの課金) (プレビュー) | Count | 合計 | Azure Digital Twins から外部エンドポイントに送信されたメッセージ数の課金メトリック。 | Meter Id |
| BillingQueryUnits | Billing Query Units (課金クエリ単位) (プレビュー) | Count | 合計 | クエリを実行するために消費された、内部で計算されたサービス リソース使用量のメジャーであるクエリ単位の数。 クエリ単位の測定に使用できるヘルパー API もあります。[QueryChargeHelper クラス](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | Meter Id |

#### <a name="ingress-metrics"></a>イングレスのメトリック

データのイングレスに関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Ingress Events (イングレス イベント) (プレビュー) | Count | 合計 | Azure Digital Twins の受信テレメトリ イベントの数。 | 結果 |
| IngressEventsFailureRate | Ingress Events Failure Rate (イングレス イベント エラー率) (プレビュー) | Percent | Average | サービスが内部エラー (500) 応答コードを返す受信テレメトリ イベントの割合。 | 結果 |
| IngressEventsLatency | Ingress Events Latency (イングレス イベント待ち時間) (プレビュー) | ミリ秒 | Average | イベントが到着してから、Azure Digital Twins がそのイベントを送出できる状態になるまでの時間。その時点でサービスから成功または失敗の結果が送信されます。 | 結果 |

#### <a name="routing-metrics"></a>ルーティング メトリック

ルーティングに関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Messages Routed (ルーティングされたメッセージ) (プレビュー) | Count | 合計 | Event Hub、Service Bus、Event Grid など、エンドポイントの Azure サービスにルーティングされたメッセージの数。 | エンドポイントの種類、 <br>結果 |
| RoutingFailureRate | Routing Failure Rate (ルーティングの失敗率) (プレビュー) | Percent | Average | イベントが Azure Digital Twins からエンドポイントの Azure サービス (Event Hub、Service Bus、Event Grid など) にルーティングされるときにエラーが発生するイベントの割合。 | エンドポイントの種類、 <br>結果 |
| RoutingLatency | Routing Latency (ルーティングの待機時間) (プレビュー) | ミリ秒 | Average | イベントが Azure Digital Twins からルーティングされてから、Event Hub、Service Bus、Event Grid などのエンドポイントの Azure サービスにポストされるまでの経過時間。 | エンドポイントの種類、 <br>結果 |

## <a name="dimensions"></a>Dimensions

ディメンションは、メトリックに関する詳細を特定するのに役立ちます。 一部のルーティング メトリックでは、エンドポイントごとの情報が提供されます。 次の表に、これらのディメンションに可能な値を示します。

| Dimension | 値 |
| --- | --- |
| 認証 | OAuth |
| 操作 (API 要求用) | Microsoft.DigitalTwins/digitaltwins/delete、 <br>Microsoft.DigitalTwins/digitaltwins/write、 <br>Microsoft.DigitalTwins/digitaltwins/read、 <br>Microsoft.DigitalTwins/eventroutes/read、 <br>Microsoft.DigitalTwins/eventroutes/write、 <br>Microsoft.DigitalTwins/eventroutes/delete、 <br>Microsoft.DigitalTwins/models/read、 <br>Microsoft.DigitalTwins/models/write、 <br>Microsoft.DigitalTwins/models/delete、 <br>Microsoft.DigitalTwins/query/action |
| エンドポイントの種類 | Event Grid、 <br>Event Hub、 <br>Service Bus |
| Protocol | HTTPS |
| 結果 | 成功、 <br>障害 |
| 状態コード | 200、404、500 など。 |
| 状態コード クラス | 2xx、4xx、5xx など。 |
| 状態テキスト | 内部サーバー エラー、見つかりませんなど。 |

## <a name="next-steps"></a>次のステップ

Azure Digital Twins の記録されたメトリックの管理の詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。