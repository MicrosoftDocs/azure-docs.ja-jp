---
title: Azure Diagnostics と Azure Application Insights の統合を使用して Azure Cloud Services での問題に関するアラートを設定する | Microsoft Docs
description: Azure Application Insights を使用して Azure Cloud Services での起動エラー、クラッシュ、ロールのリサイクル ループなどの問題を監視します
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309292"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Azure Diagnostics と Azure Application Insights の統合を使用して Azure Cloud Services での問題に関するアラートを設定する

この記事では、Azure Cloud Services (Web ロールとワーカー ロール) での起動エラー、クラッシュ、ロールのリサイクル ループなどの問題を監視するアラート ルールを設定する方法について説明します。

この記事で説明されている方法は、「[Azure Diagnostics integration with Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)」(Azure 診断と Application Insights の統合) と、最近リリースされた[Application Insights のログ アラート](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)機能に基づいています。

## <a name="define-a-base-query"></a>基本クエリを定義する

最初に、Windows Azure チャネルから Windows Event Log イベントを取得する基本クエリを定義します。これは、トレース レコードとして Application Insights 内にキャプチャされます。
これらのレコードを使用して、起動エラー、ランタイム エラー、リサイクル ループなどの Azure Cloud Services でのさまざまな問題を検出できます。

> [!NOTE]
> 次の基本クエリは、問題を 30 分にわたってチェックし、テレメトリ レコードの取り込みの待機時間を 10 分と想定しています。 これらの既定値は、適切と思う値に構成できます。

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>特定のイベント ID をチェックする

Windows Event Log イベント を取得した後、それぞれのイベント ID とメッセージ プロパティをチェックすることで、特定の問題を検出できます (以下の例を参照してください)。
上の基本クエリを以下のクエリのいずれかと組み合わせ、ログ アラート ルールを定義するときに組み合わせたクエリを使用するだけです。

> [!NOTE]
> 以下の例では、分析される時間枠中に 4 個以上のイベントが検出された場合に問題が検出されます。 この既定の設定は、アラート ルールの感度を変更するように構成できます。

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>アラートを作成する

Application Insights リソース内のナビゲーション メニューで **[アラート]** に移動し、**[新しいアラート ルール]** を選択します。

![[ルールの作成] のスクリーン ショット](./media/proactive-cloud-services/001.png)

**[ルールの作成]** ウィンドウで、**[アラートの条件を定義します]** セクションの **[条件の追加]** をクリックし、**[カスタム ログ検索]** をクリックします。

![アラートの条件定義のスクリーン ショット](./media/proactive-cloud-services/002.png)

**[検索クエリ]** ボックスに、前の手順で準備したクエリの組み合わせを貼り付けます。

次に、**[しきい値]** ボックスに進み、その値を [0] に設定します。 必要に応じて、**[期間]** フィールドと **[頻度]** フィールドを調整できます。
**[Done]** をクリックします。

![シグナル ロジック クエリの構成のスクリーン ショット](./media/proactive-cloud-services/003.png)

**[アラートの詳細を定義します]** セクションで、アラート ルールの **[名前]** と **[説明]** を指定し、**[重大度]** を設定します。
さらに、**[ルールの作成時に有効にする]** ボタンが **[はい]** に設定されていることを確認します。

![アラートの詳細のスクリーン ショット](./media/proactive-cloud-services/004.png)

**[アクション グループを定義します]** セクションで、既存の **[アクション グループ]** を選択するか、新しく作成します。
さまざまな種類の複数のアクションが含まれるアクション グループを作成できます。

![アクション グループのスクリーン ショット](./media/proactive-cloud-services/005.png)

アクション グループを定義したら、変更内容を確認し、**[アラート ルールの作成]** をクリックします。

## <a name="next-steps"></a>次の手順

自動検出の詳細を確認します。

[失敗の異常](./proactive-failure-diagnostics.md)
[メモリ リーク](./proactive-potential-memory-leak.md)
[パフォーマンスの異常](./proactive-performance-diagnostics.md)

