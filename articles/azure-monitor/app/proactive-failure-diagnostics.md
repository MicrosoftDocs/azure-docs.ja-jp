---
title: スマート検出 - Application Insights での失敗の異常 |Microsoft Docs
description: Web アプリに対する要求の失敗率の異常な変化を通知し、診断分析を行います。 構成は必要ありません。
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587538"
---
# <a name="smart-detection---failure-anomalies"></a>スマート検出 - 失敗の異常
[Application Insights](./app-insights-overview.md) では、Web アプリで要求の失敗率が異常に増加すると、ほぼリアルタイムで自動的にユーザーにアラートを送信します。 具体的には、失敗として報告された HTTP 要求または依存関係の呼び出しの割合が異常に上昇すると、それが検出されます。 要求の場合、失敗した要求の応答コードは、通常、400 以上です。 アラートの詳細には、問題のトリアージと診断に役立つよう、失敗の特性および関連するアプリケーション データの分析が表示されます。 また、より詳しい診断を行うために、Application Insights ポータルへのリンクも含まれています。 この機能は、機械学習アルゴリズムを使用して通常のエラー率を予測するため、セットアップや構成は不要です。

この機能は、クラウドまたは独自サーバーでホストされ、アプリケーションの要求または依存データを生成するあらゆる Web アプリで利用できます。 たとえば、[TrackRequest()](./api-custom-events-metrics.md#trackrequest) または [TrackDependency()](./api-custom-events-metrics.md#trackdependency) を呼び出す worker ロールがある場合などです。

[プロジェクト用に Application Insights](./app-insights-overview.md) を設定し、アプリケーションで特定の最少限のデータを生成する場合、エラーの異常のスマート検出は 24 時間かけてアプリケーションの通常の動作を学習してから、オンに切り替わり、アラートを送信できるようになります。

アラートの例を次に示します。

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="失敗箇所周辺のクラスター分析を示すスマート検出のアラートのサンプル。" lightbox="./media/proactive-failure-diagnostics/013.png":::

アラートの詳細には次の内容が表示されます。

* 通常のアプリケーションの動作と比較したエラー率。
* 影響を受けるユーザーの数 - アラートの重要度がわかります。
* エラーに関連付けられている特徴的パターン。 この例では、特定の応答コード、要求名 (操作)、およびアプリケーションのバージョンがあります。 これにより、コードのどこから探すべきかすぐにわかります。 他には特定のブラウザーやクライアント オペレーティング システムなどが想定されます。
* 特徴付けられた失敗に関連するように見える例外、ログ トレース、依存関係エラー (データベースやその他の外部コンポーネント)。
* Application Insights のデータの関連検索に直接リンクします。

## <a name="benefits-of-smart-detection"></a>スマート検出の利点
通常の [メトリック アラート](../alerts/alerts-log.md) から、問題がある可能性がわかります。 ただし、スマート検出では自動的に診断作業が開始され、ユーザーに代わってさまざまな分析が実行されます。 結果はわかりやすくまとめられるので、問題の原因をすぐに把握できます。

## <a name="how-it-works"></a>しくみ
スマート検出は、アプリから受け取ったデータ (特に失敗率) を監視します。 このルールは、`Successful request` プロパティが false である要求の数と、`Successful call` プロパティが false である依存関係の呼び出しの数をカウントします。 要求の場合、既定では、`Successful request == (resultCode < 400)` です ([フィルター](./api-filtering-sampling.md#filtering)にカスタム コードを記述した場合、または独自の [TrackRequest](./api-custom-events-metrics.md#trackrequest) 呼び出しを生成する場合を除きます)。 

アプリのパフォーマンスには、一般的な動作パターンがあります。 他よりもエラーが発生しやすい要求または依存関係があります。負荷が増えると、全体的なエラー率が上がります。 スマート検出は機械学習を使用し、これらの異常を検出します。

Web アプリから Application Insights にデータが送られると、スマート検出は現在の動作と過去数日間に見られたパターンを比較します。 以前のパフォーマンスと比べて失敗率の異常な上昇が検出された場合に、分析がトリガーされます。

分析がトリガーされると、サービスは失敗した要求のクラスター分析を実行して、失敗を特徴づける値のパターンの特定を試みます。 

上の例では、分析により、ほとんどの失敗が特定の結果コード、要求名、サーバー URL ホスト、およびロール インスタンスに関係していることが検出されました。 

サービスがこれらの呼び出しでインストルメント化される場合、アナライザーは特定されたクラスター内の要求に関連する例外と依存関係エラーに加え、それらの要求に関連するすべてのトレース ログの例も検出します。

分析結果は、アラートとして電子メールで送信されます (送信しないように構成している場合を除きます)。

[手動で設定したアラート](../alerts/alerts-log.md)と同様に、発生したアラートを検査でき、問題を修正すると解決できます。 Application Insights リソースの [アラート] ページでアラート ルールを構成します。 ただし、その他のアラートとは異なり、スマート検出を設定したり、構成したりする必要はありません。 必要に応じて、無効にすることや、送信先の電子メール アドレスを変更することもできます。

### <a name="alert-logic-details"></a>アラート ロジックの詳細

アラートは Microsoft 独自の機械学習アルゴリズムによってトリガーされるため、正確な実装の詳細を共有することはできません。 そうは言っても、ときにはユーザーが基になるロジックのしくみを詳しく把握する必要があることも理解しています。 アラートをトリガーする必要があるかどうかを判断するために評価される主な要素を次に示します。 

* 20 分のローリング時間枠における要求/依存関係のエラー率の分析。
* 過去 20 分間のエラー率を過去 40 分間および過去 7 日間の率と比較して、標準偏差の X 倍を超える有意な偏差を見つけます。
* 最小エラー率の適応限界の使用。これは、アプリの要求/依存関係の量によって異なります。
* 問題が 8 から 24 時間検出されなくなった場合に、発生したアラートのモニターの状態を自動的に解決できるロジックがあります。
  注: 現在の設計では、 スマート検出アラートが解決されると、通知もアクションも送信されません。 スマート検出アラートが解決されたかどうかを Azure portal で確認できます。

## <a name="configure-alerts"></a>アラートを構成する

スマート検出アラート ルールは、ポータルから、または Azure Resource Manager ([テンプレートの例を参照](./proactive-arm-config.md)) を使用して、無効にできます。

このアラート ルールは、電子メールと Webhook のアクションが含まれる "Application Insights スマート検出" という名前の関連する[アクション グループ](../alerts/action-groups.md)とともに作成され、アラート発生時に追加のアクションをトリガーするように拡張できます。

> [!NOTE]
> このアラート ルールから送信される電子メール通知が、サブスクリプションの監視閲覧者ロールおよび監視共同作成者ロールに関連付けられているユーザーに既定で送信されるようになりました。 この詳細については、[こちら](./proactive-email-notification.md)を参照してください。
> このアラート ルールから送信される通知は、[共通アラート スキーマ](../alerts/alerts-common-schema.md)に準拠します。
>

[アラート] ページを開きます。 エラーの異常アラート ルールが、手動で設定したすべてのアラートと共に含まれており、現在アラート状態にあるかどうかを確認できます。

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Application Insights リソース ページで、[アラート] タイル、[アラート ルールの管理] の順にクリックします。" lightbox="./media/proactive-failure-diagnostics/021.png":::

アラートをクリックして構成します。

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="ルールの構成画面。" lightbox="./media/proactive-failure-diagnostics/032.png":::

エラーの異常アラート ルールを無効にしたり削除したりすることはできますが、同じ Application Insights リソースに別のルールを作成することはできません。

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>エラーの異常アラート Webhook ペイロードの例

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>アラートのトリアージと診断

アラートは、要求失敗率の異常な上昇が検出されたことを示します。 アプリやその環境に何らかの問題があることが考えられます。

さらに詳しく調査する場合は、このページの [View full details in Application Insights]\(Application Insights で完全な詳細を表示\) リンクをクリックすると、関連する要求、例外、依存関係、トレースでフィルター処理された[検索ページ](./diagnostic-search.md)に直接移動できます。 

また、[Azure portal](https://portal.azure.com) を開き、アプリの Application Insights リソースに移動して、エラー ページを開くこともできます。

[エラーの診断] をクリックすると、問題の詳細を確認して解決するのに役立ちます。

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="診断検索。" lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

要求の割合と影響を受けるユーザーの数から、問題の緊急度を判断できます。 上の例では、78.5% という失敗率が通常の失敗率である 2.2% と比較され、異常事態が発生していることを示しています。 一方、影響を受けたユーザー数は 46 人のみです。 それが自分のアプリであれば、その深刻度を評価できます。

多くの場合は、提供された要求名、例外、依存関係のエラー、トレース データからすばやく問題を診断できます。

この例では、要求の制限を超えたため、SQL Database から例外が発生しました。

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="失敗した要求の詳細。" lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>最新のアラートを確認する

Application Insights リソース ページで **[アラート]** をクリックすると、直近の発生アラートが表示されます。

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="アラートの概要。" lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>違い...
失敗の異常のスマート検出は、Application Insights の類似しているが異なる他の機能を補完します。

* [メトリック アラート](../alerts/alerts-log.md)は、ユーザーによって設定され、CPU 占有率、要求率、ページの読み込み時間など、広範なメトリックの監視を行うことができます。 このアラートを使用すると、リソースを追加する必要がある場合などに自分に警告することができます。 これに対し、エラーの異常のスマート検出は、重要な少数のメトリック (現在は要求失敗率のみ) を対象としており、Web アプリの要求失敗率が Web アプリの通常の動作と比較して増加すると、ほぼリアルタイムでユーザーに通知するよう設計されています。 メトリック アラートとは異なり、スマート検出は、動作の変化に応じてしきい値を自動的に設定および更新します。 また、スマート検出は、ユーザーに代わって診断作業を開始するため、問題の解決に要する時間が短縮されます。

* [パフォーマンスの異常のスマート検出](proactive-performance-diagnostics.md)では、マシン インテリジェンスを使用してメトリックの異常なパターンを検出します。ユーザーが構成する必要はありません。 ただし、失敗の異常のスマート検出とは異なり、パフォーマンスの異常のスマート検出の目的は、(たとえば、特定の種類のブラウザーの特定のページで) 適切に処理されない可能性のあるさまざまな使用量のセグメントを検出することです。 分析は毎日実行され、見つかった結果はアラートよりも緊急度が大幅に低い可能性があります。 これに対し、エラーの異常の分析は、受け取ったアプリケーション データに対して継続的に実行され、サーバーのエラー率が予想を超えると数分以内にユーザーに通知します。

## <a name="if-you-receive-a-smart-detection-alert"></a>スマート検出アラートを受け取った場合
*このアラートを受け取った理由*

* 先行する期間の正常な基準値と比較し、要求失敗率の異常な上昇が検出されました。 エラーと関連するアプリケーション データを分析したところ、調査すべき問題があると思われます。

*この通知は、明らかに問題があることを意味していますか。*

* アプリの中断や劣化に対してアラートを出すように試みますが、セマンティックスとアプリまたはユーザーに与える影響を完全に理解できるのは当人だけです。

*ユーザーのアプリケーション データは Microsoft からも見られるのですか。*

* いいえ。 サービスは完全に自動化されています。 通知を受け取るだけです。 ユーザーのデータは [プライベート](./data-retention-privacy.md)です。

*このアラートをサブスクライブする必要はありますか。*

* いいえ。 要求データを送信するすべてのアプリケーションには、スマート検出アラート ルールがあります。

*登録を解除できますか。または、代わりに同僚に通知が送信されるように設定できますか。*

* はい。[アラート ルール] で、スマート検出ルールをクリックし、設定します。 アラートを無効にしたり、アラートの受信者を変更したりできます。

*メールが消えました。どうしたらポータルで通知を見つけられますか。*

* アクティビティ ログにあります。 Azure で、アプリの Application Insights リソースを開き、[アクティビティ ログ] を選択します。

*一部のアラートは既知の問題に関するものであるため、受信を停止したいのですが。*

* [アラート アクション ルール](../alerts/alerts-action-rules.md)の抑制機能を使用できます。

## <a name="next-steps"></a>次のステップ
これらの診断ツールを使用すると、アプリからのデータを調査できます。

* [メトリックス エクスプローラー](../essentials/metrics-charts.md)
* [Search エクスプローラー](./diagnostic-search.md)
* [Analytics - 強力なクエリ言語](../logs/log-analytics-tutorial.md)

スマート検出は自動化されています。 ただし、アラートを追加で設定する機能が用意されています。

* [手動で構成するメトリックのアラート](../alerts/alerts-log.md)
* [可用性 Web テスト](./monitor-web-app-availability.md)