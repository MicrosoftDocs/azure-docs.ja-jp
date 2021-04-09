---
title: Managed Instance のアラートと通知を設定する (Azure portal)
description: Azure portal を使用して SQL Managed Instance アラートを作成します。このアラートにより、指定した条件が満たされたときに通知やオートメーションをトリガーできます。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592709"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure portal を使用して Azure SQL Managed Instance のアラートを作成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure portal を使用して、Azure SQL Managed Instance Database のデータベースのアラートを設定する方法について説明します。 アラートでは、電子メールを送信したり、Web hook を呼び出したり、Azure 関数を実行したり、外部 ITSM と互換性のあるチケット システムを呼び出したり、いくつかのメトリック (インスタンスのストレージ サイズや CPU 使用率など) が事前に定義されたしきい値に達したときに電話をかけたり、テキスト メッセージを送信したりすることができます。 この記事では、アラート期間を設定するベスト プラクティスも紹介します。


## <a name="overview"></a>概要

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者および共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* 音声ガイドに従って電話をかける
* 電話番号にテキスト メッセージを送信する
* Webhook を呼び出す
* Azure 関数を呼び出す
* Azure Runbook を呼び出す
* 外部の ITSM と互換性のあるチケット システムを呼び出す

アラート ルールの構成とアラート ルールに関する情報の取得には、[Azure portal、PowerShell、Azure CLI](../../azure-monitor/alerts/alerts-classic-portal.md) のほか、[Azure Monitor REST API](/rest/api/monitor/alertrules) を使用できます。 

## <a name="alerting-metrics-available-for-managed-instance"></a>マネージド インスタンスで使用可能なアラート メトリック

> [!IMPORTANT]
> アラート メトリックは、マネージド インスタンスでのみ使用できます。 マネージド インスタンス内の個々のデータベースのアラート メトリックは使用できません。 一方、データベース診断テレメトリは、[診断ログ](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)の形式で使用できます。 診断ログのアラートは、マネージド インスタンスの[ログ アラート スクリプト](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance)を使用して [SQL Analytics](../../azure-monitor/insights/azure-sql.md) 製品内から設定できます。

アラート構成には、次のマネージド インスタンス メトリックを使用できます。

| メトリック | 説明 | 計測単位と指定できる値 |
| :--------- | --------------------- | ----------- |
| 平均 CPU の割合 | 選択された期間の平均 CPU 使用率。 | 0 から 100 (パーセント) |
| 読み取り IO バイト | 選択された期間に読み取られた IO バイト。 | バイト |
| 書き込み IO バイト | 選択された期間に書き込まれた IO バイト。 | バイト |
| IO 要求数 | 選択された期間の IO 要求の数。 | 数値 |
| 予約済みストレージ スペース | マネージド インスタンス用に予約されている現在の最大ストレージ スペース。 リソースのスケーリング操作による変更。 | MB (メガバイト) |
| 使用済みストレージ スペース | 選択された期間に使用されたストレージ スペース。 データベースとインスタンスでのストレージ消費による変更。 | MB (メガバイト) |
| 仮想コア数 | マネージド インスタンス用にプロビジョニングされた仮想コア。 リソースのスケーリング操作による変更。 | 4 から 80 (仮想コア) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure ポータルでメトリックにアラート ルールを作成する

1. Azure [portal](https://portal.azure.com/) で、監視対象のマネージド インスタンスを見つけて選択します。

2. [監視] セクションで **[メトリック]** メニュー項目を選択します。

   ![監視](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. ドロップダウン メニューで、アラートを設定するメトリックの 1 つを選択します (この例では、使用済みのストレージ スペースが示されています)。

4. 集計期間 (指定された期間に達する平均、最小、または最大 (Avg、Min、または Max)) を選択します。 

5. **[新しいアラート ルール]** を選択する

6. [アラート ルールの作成] ペインで **[条件名]** をクリックします (この例では、使用済みのストレージ スペースが示されています)

   ![条件を定義する](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. [シグナル ロジックの構成] ペインで、演算子、集計の種類、およびしきい値を定義します

   * 演算子の種類のオプションは、より大きい、等しい、より小さい (しきい値) です
   * 集計の種類のオプションは、最小、最大または平均 (集計粒度期間内) です
   * しきい値は、演算子と集計の条件に基づいて評価される、アラート値です
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   スクリーンショットに示されている例では、1840876 MB の値が使用されており、しきい値が 1.8 TB であることを表しています。 この例の演算子はより大きいに設定されているため、マネージド インスタンスのストレージ スペース消費量が 1.8 TB を超える場合は、アラートが作成されます。 ストレージ スペース メトリックのしきい値は MB で表す必要があることに注意してください。

8. 分単位の評価期間 (集約粒度) と、評価の頻度を設定します。 評価の頻度は、しきい値条件が満たされているかどうかを、アラート システムで定期的に確認する時間を表します。

9. アクション グループを選択します。 [アクション グループ] ペインが表示されます。これを使用して、既存のものを選択したり、新しいアクションを作成したりすることができます。 このアクションでは、アラートをトリガーしたときに発生すること (電子メールを送信する、電話をかける、Webhook、Azure 関数、Runbook を実行するなど) を定義します。

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * 新しいアクション グループを作成するには、 **[+ アクション グループの作成]** を選択します

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * アラートを受ける方法を定義します。アクション グループ名、短い名前、アクション名を入力し、[アクションの種類] を選択します。 [アクションの種類] では、電子メール、テキスト メッセージ、音声通話で通知されるようにするか、場合によっては Webhook、Azure 関数、Runbook が実行されるようにするか、互換性のあるシステムで ITSM チケットが作成されるようにするかを定義します。

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. レコードのアラート ルールの詳細を入力し、重要度の種類を選択します。

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * **[アラート ルールの作成]** ボタンをクリックして、アラート ルールの作成を完了します。

新しいアラート ルールは数分以内にアクティブになり、設定に基づいてトリガーされるようになります。

## <a name="verifying-alerts"></a>アラートの確認

> [!NOTE]
> わずらわしいアラートを表示しないようにする場合は、[アクション ルールを使用したアラートの非表示](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts)に関する記述を参照してください。

アラート ルールを設定するときに、アラート トリガーとその頻度に問題がないことを確認します。 使用済みのストレージ スペースに関するアラートを設定するためにこのページに示されている例では、アラート オプションが電子メールの場合、次のような電子メールを受信する可能性があります。

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

電子メールには、アラートの名前、しきい値の詳細、およびアラートがトリガーされた理由が表示されます。これらは、アラートの確認とトラブルシューティングを行うのに役立ちます。 **[See in Azure portal]\(Azure portal で表示\)** ボタンを使用して、Azure portal で電子メール経由で受信したアラートを表示することができます。 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>既存のアラート ルールを表示、中断、アクティブ化、変更および削除する

> [!NOTE]
> 既存のアラートは Azure portal ダッシュボードの [アラート] メニューから管理する必要があります。 Managed Instance リソース ブレードから既存のアラートを変更することはできません。

既存のアラートを表示、中断、アクティブ化、変更および削除するには、次のようにします。

1. Azure portal の検索を使用して、アラートを検索します。 [アラート] をクリックします。

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Azure のナビゲーション バーで [アラート] をクリックすることもできます (構成してある場合)。

2. [アラート] ペインで、[アラート ルールの管理] を選択します。

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   既存のアラートの一覧が表示されます。 管理する既存のアラート ルールを個別に選択します。 既存のアクティブなルールを、好みに合わせて変更および調整することができます。 アクティブなルールは、削除せずに中断することもできます。 

## <a name="next-steps"></a>次のステップ

* Azure Monitor アラート システムの詳細については、「[Microsoft Azure のアラートの概要](../../azure-monitor/alerts/alerts-overview.md)」を参照してください
* メトリック アラートの詳細については、「[Azure Monitor でのメトリック アラートのしくみを理解する](../../azure-monitor/alerts/alerts-metric-overview.md)」を参照してください
* アラートでの Webhook の詳細については、[クラシック メトリック アラートを使用する Webhook の呼び出し](../../azure-monitor/alerts/alerts-webhooks.md)に関するページを参照してください
* PowerShell を使用したアラートの構成と管理の詳細については、[アクション ルール](/powershell/module/az.monitor/add-azmetricalertrulev2)に関するページを参照してください
* API を使用したアラートの構成と管理の詳細については、「[Azure Monitor REST API リファレンス](/rest/api/monitor/)」を参照してください