---
title: メトリック アラートを構成する - Azure portal - Azure Database for MariaDB
description: この記事では、Azure portal から Azure Database for MariaDB のメトリック アラートを構成およびアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 689e830d2486420be8d551d1f88dccba8d982428
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593059"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Azure portal を使用して Azure Database for MariaDB のメトリックのアラートを設定する

この記事では、Azure portal を使用して Azure Database for MariaDB のアラートを設定する方法について説明します。 お使いの Azure のサービスの監視メトリックに基づいてアラートを受け取ることができます。

このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 アラートは条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。

アラートがトリガーされたときに実行されるように構成できるアクションは次のとおりです。
* サービス管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure 監視 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>メトリックのアラート ルールを作成する
1. [Azure portal](https://portal.azure.com/) で、監視する Azure Database for MariaDB サーバーを選択します。

2. 次のように、サイドバーの **[監視]** セクションで、 **[アラート]** を選択します。

   ![アラート ルールを選択する](./media/howto-alert-metric/2-alert-rules.png)

3. **[メトリック アラートの追加]** (+ アイコン) を選択します。

4. 以下のように、 **[ルールの作成]** ページが開きます。 必要な情報を入力します。

   ![メトリック アラート フォームを追加する](./media/howto-alert-metric/4-add-rule-form.png)

5. **[条件]** セクションで、 **[条件の追加]** を選択します。

6. アラート通知のシグナルの一覧からメトリックを選択します。 この例では、[ストレージの割合] を選択します。
   
   ![メトリックを選択する](./media/howto-alert-metric/6-configure-signal-logic.png)

7. アラート ロジックを構成します。これには、 **[条件]** (例: 「より大きい」)、 **[しきい値]** (例: 85 パーセント)、 **[時間の集計]** 、どのくらいの期間メトリック ルールが満たされた後にアラートがトリガーされるかを示す **[期間]** (例: 「直近 30 分」)、と **[頻度]** があります。
   
   完了したら、 **[完了]** を選択します。

   ![メトリック 2 を選択する](./media/howto-alert-metric/7-set-threshold-time.png)

8. **[アクション グループ]** セクション内で **[新規作成]** を選択して、アラートの通知を受信する新しいグループを作成します。

9. [アクション グループの追加] フォームに、名前、短い名前、サブスクリプション、リソース グループを入力します。

10. アクションの種類で、 **[電子メール/SMS/プッシュ/音声]** を構成します。
    
    [電子メールの Azure Resource Manager のロール] を選択して、通知を受信するサブスクリプションの所有者、共同作成者、および閲覧者を選択します。
   
    オプションで、アラートが発生したときに呼び出す webhook の有効な URI を **[webhook]** フィールドに入力します。

    完了したら、 **[OK]** を選択します。

    ![アクション グループ](./media/howto-alert-metric/10-action-group-type.png)

11. [アラート ルール名]、[説明]、[重大度] を指定します。

    ![アクション グループ 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. **[アラート ルールの作成]** を選択して、アラートを作成します。

    数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="manage-your-alerts"></a>アラートの管理
アラートを作成したら、それを選択して次のアクションを実行できます。

* このアラートに関連するメトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* アラート ルールを **編集** または **削除** する。
* アラートを **無効** にしてアラートを一時的に停止する、または **有効** にして通知の受け取りを再開する。


## <a name="next-steps"></a>次のステップ
* [アラートでの webhook の構成](../azure-monitor/alerts/alerts-webhooks.md)に関する詳細情報を確認します。
* [メトリック収集の概要](../azure-monitor/data-platform.md) 情報を入手して、サービスの可用性と応答性を確認します。