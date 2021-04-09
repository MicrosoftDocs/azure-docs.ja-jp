---
title: Azure Portal でアラートと通知を設定する
description: Azure Portal を使用してアラートを作成します。このアラートにより、指定した条件が満たされたときに通知やオートメーションをトリガーできます。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: c7f72a9b140c68320e221a4cfb20cbf107a5f531
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572265"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Azure portal を使用して、Azure SQL Database と Azure Synapse Analytics のアラートを作成します
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>概要

この記事では、Azure Portal を使用して、Azure SQL Database と Azure Synapse Analytics のデータベースのアラートを設定する方法について説明します。 あるメトリック (データベース サイズや CPU 使用率など) がしきい値に達したら、アラートはユーザーに電子メールを送信するか、または Web フックを呼び出すことができます。

> [!NOTE]
> Azure SQL Managed Instance 固有の手順については、[Azure SQL Managed Instance のアラートを作成する](../managed-instance/alerts-create.md)方法に関するページを参照してください。

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービス管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure ポータル](../../azure-monitor/alerts/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/alerts/alerts-classic-portal.md)
* [コマンド ライン インターフェイス (CLI)](../../azure-monitor/alerts/alerts-classic-portal.md)
* [Azure 監視 REST API](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure Portal でメトリックにアラート ルールを作成する

1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。
2. [監視] セクションで、 **[アラート]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。  

   ![監視](./media/alerts-insights-configure-portal/Alerts.png)
  
3. **[新しいアラート ルール]** ボタンを選択して **[ルールの作成]** ページを開きます。
  ![ルールの作成](./media/alerts-insights-configure-portal/create-rule.png)

4. **[条件]** セクションで、 **[追加]** をクリックします。
  ![条件を定義する](./media/alerts-insights-configure-portal/create-rule.png)
5. **[シグナル ロジックの構成]** ページで、シグナルを選択します。
  ![シグナルを選択する](./media/alerts-insights-configure-portal/select-signal.png)
6. **[CPU の割合]** などのシグナルを選択すると、 **[シグナル ロジックの構成]** ページが表示されます。
  ![シグナル ロジックを構成する](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. このページで、しきい値の種類、演算子、集計の種類、しきい値、集計の粒度、および評価の頻度を構成します。 次に、 **[Done]** をクリックします。
8. **[ルールの作成]** で、既存の **アクション グループ** を選択するか、新しいグループを作成します。 アクション グループを使用して、アラート条件が発生したときに実行するアクションを定義できます。
  ![アクション グループを定義する](./media/alerts-insights-configure-portal/action-group.png)

9. ルールの名前を定義し、必要に応じて説明を入力し、ルールの重大度レベルを選択し、ルールの作成時にルールを有効にするかどうかを選択した後、 **[Create rule alert]\(ルールのアラートの作成\)** をクリックしてメトリック ルールのアラートを作成します。

10 分以内にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="next-steps"></a>次のステップ

* [アラートでの webhook の構成](../../azure-monitor/alerts/alerts-webhooks.md)に関する詳細情報を確認します。