---
title: Azure Monitor でメトリックを表示する
titleSuffix: Azure Digital Twins
description: Azure Monitor で Azure Digital Twins メトリックを表示する方法について確認します。
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434068"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Azure Digital Twins のトラブルシューティング: メトリック

この記事で取り上げるメトリックにより、Azure サブスクリプション内の Azure Digital Twins リソースの状態に関する情報が提供されます。 Azure Digital Twins メトリックは、Azure Digital Twins サービスとそれに接続されているリソースの全体的な正常性を評価するのに役立ちます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

メトリックは既定で有効になっています。 Azure Digital Twins メトリックは [Azure portal](https://portal.azure.com) で表示できます。

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure Digital Twins メトリックを表示する方法

1. Azure Digital Twins インスタンスを作成します。 Azure Digital Twins インスタンスを設定する方法については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-portal.md)に関するページを参照してください。

2. [Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスを見つけます (ポータルの検索バーに名前を入力して、そのページを開くことができます)。 

    インスタンスのメニューから、 **[メトリック]** を選択します。
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

    このページには、Azure Digital Twins インスタンスのメトリックが表示されます。 また、リストから表示したいメトリックを選択して、メトリックのカスタム ビューを作成することもできます。
    
3. メトリック データを Event Hubs エンドポイントまたは Azure Storage のアカウントに送信するように選択するには、メニューから **[診断設定]** 、 **[診断設定の追加]** の順に選択します。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが示されているスクリーンショット":::

    このプロセスの詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。

4. メニューから **[アラート]** を選択し、 **[+ 新しいアラート ルール]** を選択することで、メトリック データのアラートを設定できます。
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="[アラート] ページと追加するボタンを示すスクリーンショット":::

    このプロセスの詳細については、[*トラブルシューティング: アラートの設定*](troubleshoot-alerts.md)に関するページを参照してください。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins メトリックとその使用方法

Azure Digital Twins には、インスタンスとその関連リソースの正常性の概要を提供するいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、インスタンスの状態をより大局的に把握することもできます。 

次の表は、各 Azure Digital Twins インスタンスによって追跡されるメトリックと、各メトリックがインスタンスの全体的な状態にどのように関連しているかを示しています。

#### <a name="metrics-for-tracking-service-limits"></a>サービスの制限を追跡するためのメトリック

これらのメトリックを構成すると、ソリューションの一部の特性が[公開されているサービスの制限](reference-service-limits.md#functional-limits)に近づいているかどうかを追跡できます。 

これを設定するには Azure Monitor の[アラート](troubleshoot-alerts.md)機能を使用します。 これらのメトリックのしきい値を定義すると、メトリックが公開されている制限の特定の割合に達したときにアラートを受け取ることができます。

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| TwinCount | ツイン数 (プレビュー) | Count | 合計 | Azure Digital Twins インスタンス内のツインの合計数。 インスタンスごとに許可されるツインの最大数に関する[サービスの制限](reference-service-limits.md#functional-limits)に近づいているかどうかを判断するには、このメトリックを使用します。 |  なし |
| ModelCount | モデル数 (プレビュー) | Count | 合計 | Azure Digital Twins インスタンス内のモデルの合計数。 インスタンスごとに許可されるモデルの最大数に関する[サービスの制限](reference-service-limits.md#functional-limits)に近づいているかどうかを判断するには、このメトリックを使用します。 | なし |

#### <a name="api-request-metrics"></a>API 要求のメトリック

API 要求に関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 要求 | Count | 合計 | Digital Twins の読み取り、書き込み、削除、およびクエリ操作に対して行われた API 要求の数。 |  認証、 <br>操作、 <br>プロトコル、 <br>状態コード、 <br>状態コード クラス、 <br>状態テキスト |
| ApiRequestsFailureRate | API Requests Failure Rate (API 要求の失敗率) | Percent | Average | インスタンスに対してサービスが受信する API 要求のうち、Digital Twins の読み取り、書き込み、削除、クエリ操作に対して内部エラー (500) 応答コードを返す API 要求の割合。 | 認証、 <br>操作、 <br>プロトコル、 <br>状態コード、 <br>状態コード クラス、 <br>状態テキスト
| ApiRequestsLatency | API Requests Latency (API 要求の待機時間) | ミリ秒 | Average | API 要求の応答時間。 これは、Azure Digital Twins が要求を受け取ってから、サービスが Digital Twins の読み取り、書き込み、削除、クエリ操作の成功/失敗の結果を送信するまでの時間を指します。 | 認証、 <br>操作、 <br>Protocol |

#### <a name="billing-metrics"></a>課金メトリック

課金に関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Billing API Operations (課金 API 操作) | Count | 合計 | Azure Digital Twins サービスに対して行われたすべての API 要求の数の課金メトリック。 | Meter ID |
| BillingMessagesProcessed | Billing Messages Processed (処理されたメッセージの課金) | Count | 合計 | Azure Digital Twins から外部エンドポイントに送信されたメッセージ数の課金メトリック。<br><br>課金に対する 1 つのメッセージと見なされるには、ペイロードが 1 KB を超えないようにする必要があります。 これを超えるペイロードは、1 KB ごとに追加のメッセージとしてカウントされます (そのため、1 KB から 2 KB のメッセージは 2 つのメッセージ、2 KB から 3 KB のメッセージは 3 つのメッセージとしてカウントされます)。<br>この制限は、応答にも適用されます。たとえば、応答本文内で 1.5 KB を返す呼び出しは、2 件の操作として課金されます。 | Meter ID |
| BillingQueryUnits | Billing Query Units (課金クエリ単位) | Count | 合計 | クエリを実行するために消費された、内部で計算されたサービス リソース使用量のメジャーであるクエリ単位の数。 クエリ単位の測定に使用できるヘルパー API もあります。[QueryChargeHelper クラス](/dotnet/api/azure.digitaltwins.core.querychargehelper) | Meter ID |

Azure Digital Twins の課金方法の詳細については、「[*Azure Digital Twins の価格*](https://azure.microsoft.com/pricing/details/digital-twins/)」を参照してください。

#### <a name="ingress-metrics"></a>イングレスのメトリック

データのイングレスに関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Ingress Events (イングレス イベント) | Count | 合計 | Azure Digital Twins の受信テレメトリ イベントの数。 | 結果 |
| IngressEventsFailureRate | Ingress Events Failure Rate (イングレス イベント エラー率) | Percent | Average | サービスが内部エラー (500) 応答コードを返す受信テレメトリ イベントの割合。 | 結果 |
| IngressEventsLatency | Ingress Events Latency (イングレス イベント待ち時間) | ミリ秒 | Average | イベントが到着してから、Azure Digital Twins がそのイベントを送出できる状態になるまでの時間。その時点でサービスから成功または失敗の結果が送信されます。 | 結果 |

#### <a name="routing-metrics"></a>ルーティング メトリック

ルーティングに関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Messages Routed (ルーティングされたメッセージ) | Count | 合計 | Event Hub、Service Bus、Event Grid など、エンドポイントの Azure サービスにルーティングされたメッセージの数。 | エンドポイントの種類、 <br>結果 |
| RoutingFailureRate | Routing Failure Rate (ルーティングの失敗率) | Percent | Average | イベントが Azure Digital Twins からエンドポイントの Azure サービス (Event Hub、Service Bus、Event Grid など) にルーティングされるときにエラーが発生するイベントの割合。 | エンドポイントの種類、 <br>結果 |
| RoutingLatency | Routing Latency (ルーティングの待機時間) | ミリ秒 | Average | イベントが Azure Digital Twins からルーティングされてから、Event Hub、Service Bus、Event Grid などのエンドポイントの Azure サービスにポストされるまでの経過時間。 | エンドポイントの種類、 <br>結果 |

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
