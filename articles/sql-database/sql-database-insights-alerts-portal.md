---
title: "Azure Portal での SQL Database アラートの作成 | Microsoft Docs"
description: "Azure Portal を使用して SQL Database アラートを作成します。このアラートにより、指定した条件が満たされたときに通知やオートメーションをトリガーできます。"
author: CarlRabeler
manager: jhubbard
editor: 
services: sql-database
documentationcenter: 
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2679681c77dd6a3410bbe6ddbcf562924b13bfe6
ms.openlocfilehash: afa21052281200768db24ce35a94097f23f23efe


---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database"></a>Azure Portal での SQL Database アラートの作成

## <a name="overview"></a>概要
この記事では、Azure Portal を使用して SQL Database アラートを設定する方法について説明します。 この記事では、値およびしきい値のベスト プラクティスも紹介します。    

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。    
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す
* Azure Runbook の実行を開始する (Azure ポータルからのみ)

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure ポータル](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure ポータルでメトリックにアラート ルールを作成する
1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。
2. [監視] セクションで、**[アラート]** または **[アラート ルール]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。  
   
    ![監視](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
3. **[アラートの追加]** コマンドを選択し、フィールドに入力します。
   
    ![[アラートの追加]](../monitoring-and-diagnostics/media/insights-alerts-portal/AddAlertOnlyParamsPage.png)
4. アラート ルールに**名前**を付けて、**説明**を選択します。この説明は通知電子メールにも表示されます。
5. 監視する**メトリック**を選択し、メトリックの**条件**と**しきい値**を選択します。 また、 **[期間]** では、どのくらいの期間メトリック ルールが満たされた後、アラートがトリガーされるかを選択します。 たとえば、期間として [PT5M] を使用すると、アラートは 80% を超える CPU を見つけて、その CPU が 5 分間継続して 80% を超えた時点でトリガーされます。 最初のトリガーが発生したら、次のアラートは、CPU が 5 分間継続して 80% を下回ったときにトリガーされます。 CPU 測定は 1 分ごとに発生します。   
6. アラートが発生したときに管理者と共同管理者に電子メールが送信されるようにするには、 **[メールの所有者...]** をオンにします。
7. アラートが発生したときに、他のアドレスにも電子メールを送信して通知する場合は、 **[追加する管理者の電子メール]** フィールドにそのアドレスを入力します。 複数の電子メール アドレスを入力する場合はセミコロンで区切ります- *email@contoso.com;email2@contoso.com*
8. **[webhook]** フィールドに、アラートが発生したときに呼び出す webhook の有効な URI を入力します。
9. Azure Automation を使用する場合は、アラートが発生したときに実行する Runbook を選択できます。
10. 完了したら **[OK]** を選択して、アラートを作成します。   

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="managing-your-alerts"></a>アラートの管理
アラートを作成して選択したら、次の操作を行うことができます。

* メトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* 編集または削除する。
* そのアラートの受信を一時的に停止または再開する必要がある場合に、そのアラートを**無効**または**有効**にする。


## <a name="sql-database-alert-values-and-thresholds"></a>SQL Database のアラート値およびしきい値

| リソースの種類 | メトリックの名前 | フレンドリ名 | 集計の種類 | 最短アラート時間ウィンドウ|
| --- | --- | --- | --- | --- |
| SQL データベース | cpu_percent | CPU の割合 | 平均 | 5 分 |
| SQL データベース | physical_data_read_percent | データ IO の割合 | 平均 | 5 分 |
| SQL データベース | log_write_percent | ログ IO の割合 | 平均 | 5 分 |
| SQL データベース | dtu_consumption_percent | DTU の割合 | 平均 | 5 分 |
| SQL データベース | storage | 合計データベース サイズ | 最大値 | 30 分 |
| SQL データベース | connection_successful | 成功した接続 | 合計 | 10 分 |
| SQL データベース | connection_failed | 失敗した接続 | 合計 | 10 分 |
| SQL データベース | blocked_by_firewall | ファイアウォールによってブロックされる | 合計 | 10 分 |
| SQL データベース | deadlock | デッドロック | 合計 | 10 分 |
| SQL データベース | storage_percent | データベース サイズの割合 | 最大値 | 30 分 |
| SQL データベース | xtp_storage_percent | インメモリ OLTP ストレージの割合 (プレビュー) | 平均 | 5 分 |
| SQL データベース | workers_percent | ワーカーの割合 | 平均 | 5 分 |
| SQL データベース | sessions_percent | セッションの割合 | 平均 | 5 分 |
| SQL データベース | dtu_limit | DTU の上限 | 平均 | 5 分 |
| SQL データベース | dtu_used | 使用された DTU | 平均 | 5 分 |
||||||           
| SQL Data Warehouse | cpu_percent | CPU の割合 | 平均 | 10 分 |
| SQL Data Warehouse | physical_data_read_percent | データ IO の割合 | 平均 | 10 分 |
| SQL Data Warehouse | storage | 合計データベース サイズ | 最大値 | 10 分 |
| SQL Data Warehouse | connection_successful | 成功した接続 | 合計 | 10 分 |
| SQL Data Warehouse | connection_failed | 失敗した接続 | 合計 | 10 分 |
| SQL Data Warehouse | blocked_by_firewall | ファイアウォールによってブロックされる | 合計 | 10 分 |
| SQL Data Warehouse | service_level_objective | データベースのサービス レベル目標 | 合計 | 10 分 |
| SQL Data Warehouse | dwu_limit | DWU 上限 | 最大値 | 10 分 |
| SQL Data Warehouse | dwu_consumption_percent | DWU の割合 | 平均 | 10 分 |
| SQL Data Warehouse | dwu_used | 使用済み DWU | 平均 | 10 分 |
||||||               
| エラスティック プール | cpu_percent | CPU の割合 | 平均 | 5 分 |
| エラスティック プール | physical_data_read_percent | データ IO の割合 | 平均 | 5 分 |
| エラスティック プール | log_write_percent | ログ IO の割合 | 平均 | 5 分 |
| エラスティック プール | dtu_consumption_percent | DTU の割合 | 平均 | 5 分 |
| エラスティック プール | storage_percent | ストレージの割合 | 平均 | 5 分 |
| エラスティック プール | workers_percent | ワーカーの割合 | 平均 | 5 分 |
| エラスティック プール | eDTU_limit | eDTU 制限 | 平均 | 5 分 |
| エラスティック プール | storage_limit | ストレージの制限 | 平均 | 5 分 |
| エラスティック プール | eDTU_used | 使用済み eDTU | 平均 | 5 分 |
| エラスティック プール | storage_used | 使用済みストレージ | 平均 | 5 分 |
||||||


## <a name="next-steps"></a>次のステップ
* [Azure での監視の概要](../monitoring-and-diagnostics/monitoring-overview.md) 情報を入手します。
* [アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md)の詳細情報を確認します。
* [診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 情報を入手し、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。




<!--HONumber=Nov16_HO3-->


