---
title: "Log Analytics でのデータ使用状況の分析 | Microsoft Docs"
description: "Log Analytics の利用状況ダッシュボードでは、Log Analytics サービスに送信されているデータの量を確認することや、大量のデータが送信されている理由のトラブルシューティングを行うことができます。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 9a4709f298131722e9c473a19f7eee0aebf7e1e6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="analyze-data-usage-in-log-analytics"></a>Log Analytics でのデータ使用状況の分析
Log Analytics には、収集されたデータの量、データを送信したコンピューター、送信されたさまざまな種類のデータに関する情報が含まれます。  **[ログ分析の利用状況]** ダッシュボードを使用すると、Log Analytics サービスに送信されたデータの量を確認できます。 ダッシュボードには、各ソリューションによって収集されたデータの量と、お使いのコンピューターが送信しているデータの量が表示されます。

## <a name="understand-the-usage-dashboard"></a>利用状況ダッシュボードについて
**[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードには、次の情報が表示されます。

- データ ボリューム
    - (現在の時間範囲に基づく) 経時のデータ ボリューム
    - ソリューション別のデータ ボリューム
    - コンピューターに関連付けられていないデータ
- [Computers (コンピューター)]
    - データを送信しているコンピューター
    - 過去 24 時間のデータがないコンピューター
- プラン
    - Insight と Analytics のノード
    - Automation と Control のノード
    - セキュリティ ノード
- パフォーマンス
    - データの収集とインデックス作成に要した時間
- クエリのリスト

![[使用量] ダッシュボード](./media/log-analytics-usage/usage-dashboard01.png)

### <a name="to-work-with-usage-data"></a>使用量データを扱うには
1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure Portal](https://portal.azure.com) にサインインします。
2. **ハブ** メニューで **[その他のサービス]** をクリックし、リソースの一覧で「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** をクリックします。  
    ![Azure のハブ](./media/log-analytics-usage/hub.png)
3. **[Log Analytics]** ダッシュボードにワークスペースの一覧が表示されます。 ワークスペースを選択します。
4. *[ワークスペース]* ダッシュボードで、**[Log Analytics Usage (Log Analytics の使用量)]** をクリックします。
5. **[Log Analytics Usage (Log Analytics の使用量)]** ダッシュボードで、**[時間: 過去 24 時間]** をクリックして期間を変更します。  
    ![期間](./media/log-analytics-usage/time.png)
6. 確認したい領域が示されている使用量カテゴリ ブレードを確認します。 ブレードを選択し、その項目をクリックして、[[ログ検索]](log-analytics-log-searches.md) に詳細を表示します。  
    ![データ使用量ブレードの例](./media/log-analytics-usage/blade.png)
7. [ログ検索] ダッシュボードで、検索によって返された結果を確認します。  
    ![使用量のログの検索の例](./media/log-analytics-usage/usage-log-search.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>収集したデータの量が予測よりも多い場合のアラートを作成する
このセクションでは、次の場合のアラートを作成する方法について説明します。
- データ量が指定された量を超えた。
- データ量が指定された量を超えると予測された。

Log Analytics の[アラート](log-analytics-alerts-creating.md)では、検索クエリを使用します。 次のクエリでは、過去 24 時間で 100 GB を超えるデータが収集された場合に結果が返されます。

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`

次のクエリでは、簡単な数式を使用して、1 日に 100 GB を超えるデータが送信される場合を予測します。 

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`

別のデータ量でアラートを生成するには、クエリの 100 をアラートを生成する GB 数に変更します。

「[アラート ルールを作成する](log-analytics-alerts-creating.md#create-an-alert-rule)」で説明されている手順を使用して、収集したデータの量が予測を超えた場合に通知するようにします。

最初のクエリ (24 時間でデータが 100 GB を超えた場合) のアラートを作成するには、次のように設定します。
- **[名前]**: "*24 時間でデータ量が 100 GB を超えた場合*"
- **[重大度]**: *[警告]*
- **[検索クエリ]**: `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`
- **[時間枠]**: "*24 時間*"
- **[アラートの頻度]**: 使用量データは 1 時間に 1 回しか更新されないため、1 時間に設定します。
- **[アラートを生成する基準]**: *[結果の数]*
- **[結果の数]**: "*0 より大きい*"

[アラート ルールへのアクションの追加](log-analytics-alerts-actions.md)に関する記事に記載されている手順で、アラート ルールの電子メール、webhook、または Runbook アクションを設定します。

2 つ目のクエリ (24 時間でデータが 100 GB を超えると予測される場合) のアラートを作成するには、次のように設定します。
- **[名前]**: "*24 時間でデータ量が 100 GB を超えると予想される場合*"
- **[重大度]**: *[警告]*
- **[検索クエリ]**: `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`
- **[時間枠]**: "*3 時間*"
- **[アラートの頻度]**: 使用量データは 1 時間に 1 回しか更新されないため、1 時間に設定します。
- **[アラートを生成する基準]**: *[結果の数]*
- **[結果の数]**: "*0 より大きい*"

アラートを受け取ったら、次のセクションの手順を使用して、使用量が予想よりも多い理由のトラブルシューティングを行います。

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>使用量が予想よりも多い理由のトラブルシューティング
利用状況ダッシュボードは、使用量 (そのため、コスト) が予想を超えている理由を特定するのに役立ちます。

使用量が多くなる原因は、次のいずれかまたは両方です。
- 予想よりも多くのデータが Log Analytics に送信されている
- 予想よりも多くのノードが Log Analytics にデータを送信している

### <a name="check-if-there-is-more-data-than-expected"></a>データ量が予想を超えているかどうかの確認 
利用状況ページには、大量のデータが収集されている原因を特定するのに役立つ 2 つの重要なセクションがあります。

*[データ ボリュームの経時変化]* グラフには、送信されたデータの総量と最も多くのデータを送信しているコンピューターが示されます。 上部のグラフでは、全体的なデータ使用量が増加しているか、一定しているか、減少しているかを確認できます。 コンピューターの一覧には、大量のデータを送信している上位 10 台のコンピューターが表示されます。

*[ソリューションごとのデータ ボリューム]* グラフには、各ソリューションによって送信されたデータの量と、最も多くのデータを送信しているソリューションが示されます。 上部のグラフには、各ソリューションによって送信されたデータの総量の経時変化が示されています。 この情報により、特定のソリューションが送信しているデータの量が時間の経過に伴って増加しているのか、だいたい同じか、減少しているかを特定できます。 ソリューションの一覧には、大量のデータを送信している上位 10 個のソリューションが表示されます。 

これらの 2 つのグラフには、すべてのデータが表示されます。 データには、課金対象と無料のものがあります。 課金対象のデータのみに絞り込むには、検索ページのクエリを `IsBillable=true` を含めるように変更します。  

![データ量のグラフ](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

*[データ ボリュームの経時変化]* グラフを確認します。 特定のコンピューターでデータを最も多く送信しているソリューションとデータの種類を確認するには、コンピューターの名前をクリックします。 一覧内の最初のコンピューターの名前をクリックします。

次のスクリーンショットでは、*Log Management / Perf* データの種類がそのコンピューターでデータを最も多く送信しています。 

![コンピューターのデータ量](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)

次に、"*利用状況*" ダッシュボードに戻り、*[ソリューションごとのデータ ボリューム]* グラフを確認します。 特定のソリューションでデータを最も多く送信しているコンピューターを確認するには、一覧内のソリューションの名前をクリックします。 一覧内の最初のソリューションの名前をクリックします。 

次のスクリーンショットでは、*acmetomcat* コンピューターが Log Management ソリューションで最も多くのデータを送信していることがわかります。

![ソリューションのデータ量](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)

必要に応じて、追加の分析を実行して、ソリューションまたはデータの種類内でデータ量が多いものを特定します。 クエリの例を次に示します。

+ **Security** ソリューション
  - `Type=SecurityEvent | measure count() by EventID`
+ **Log Management** ソリューション
  - `Type=Usage Solution=LogManagement IsBillable=true | measure count() by DataType`
+ **Perf** データの種類
  - `Type=Perf | measure count() by CounterPath`
  - `Type=Perf | measure count() by CounterName`
+ **Event** データの種類
  - `Type=Event | measure count() by EventID`
  - `Type=Event | measure count() by EventLog, EventLevelName`
+ **Syslog** データの種類
  - `Type=Syslog | measure count() by Facility, SeverityLevel`
  - `Type=Syslog | measure count() by ProcessName`
+ **AzureDiagnostics** データ型
  - `Type=AzureDiagnostics | measure count() by ResourceProvider, ResourceId`

収集されるログの量を削減するには、次の手順を実行します。

| データ量の多いソース | データ量を削減する方法 |
| -------------------------- | ------------------------- |
| セキュリティ イベント            | [一般的または最小限のセキュリティ イベント](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/)を選択します。 <br> 必要なイベントのみを収集するようにセキュリティ監査ポリシーを変更します。 特に、次のイベントを収集する必要性を検討します。 <br> - [フィルタリング プラットフォームの監査](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [レジストリの監査](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [ファイル システムの監査](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [カーネル オブジェクトの監査](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [ハンドル操作の監査](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [リムーバブル記憶域の監査](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| パフォーマンス カウンター       | [パフォーマンス カウンターの構成](log-analytics-data-sources-performance-counters.md)を次のように変更します。 <br> - 収集の頻度を減らす <br> - パフォーマンス カウンターの数を減らす |
| イベント ログ                 | [イベント ログの構成](log-analytics-data-sources-windows-events.md)を次のように変更します。 <br> - 収集対象のイベント ログの数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" レベルのイベントを収集しないようにします。 |
| syslog                     | [syslog の構成](log-analytics-data-sources-syslog.md)を次のように変更します。 <br> - 収集対象の施設の数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" と "*デバッグ*" レベルのイベントを収集しないようにします。 |
| AzureDiagnostics           | リソース ログの収集を次のように変更します。 <br> - Log Analytics へのリソース送信ログの数を減らす <br> - 必要なログのみを収集する |
| ソリューションを必要としないコンピューターからのソリューション データ | [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。 |

### <a name="check-if-there-are-more-nodes-than-expected"></a>ノード数が予想を超えているかどうかの確認
"*ノード単位 (OMS)*" の価格レベルを使用している場合は、使用するノードとソリューションの数に基づいて料金が発生します。 使用されている各プランのノード数は、使用量ダッシュボードの "*オファリング*" セクションで確認できます。

![[使用量] ダッシュボード](./media/log-analytics-usage/log-analytics-usage-offerings.png)

**[すべて表示]** をクリックすると、選択したプランでデータを送信しているコンピューターの完全な一覧が表示されます。

[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。


## <a name="next-steps"></a>次のステップ
* 検索言語の使用方法については、[Log Analytics のログ検索](log-analytics-log-searches.md)に関する記事を参照してください。 検索クエリを使用して、使用量データをさらに分析できます。
* 「[アラート ルールを作成する](log-analytics-alerts-creating.md#create-an-alert-rule)」で説明されている手順を使用して、検索条件が満たされた場合に通知するようにします。
* [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。
* [一般的または最小限のセキュリティ イベント](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/)を選択します。
* [パフォーマンス カウンターの構成](log-analytics-data-sources-performance-counters.md)を変更します。
* [イベント ログの構成](log-analytics-data-sources-windows-events.md)を変更します。
* [syslog の構成](log-analytics-data-sources-syslog.md)を変更します。

