---
title: メトリック アラートを構成する - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure portal から Azure Database for MySQL フレキシブル サーバーのメトリック アラートを構成およびアクセスする方法について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 632aae766e6fd1328dc6e0135a88a942d7ad0910
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595786"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Azure portal を使用して Azure Database for MySQL - フレキシブル サーバーのメトリックのアラートを設定する 

> [!IMPORTANT] 
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure Portal を使用して Azure Database for MySQL のアラートを設定する方法について説明します。 お使いの Azure のサービスの監視メトリックに基づいてアラートを受け取ることができます。

このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 アラートは条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。 メトリック アラートはステートフルです。つまり、状態が変更されたときにのみ通知を送信します。

アラートがトリガーされたときに実行されるように構成できるアクションは次のとおりです。
* サービス管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。
* [Azure Portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure 監視 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal でメトリックのアラート ルールを作成する
1. [Azure portal](https://portal.azure.com/) で、監視する Azure Database for MySQL フレキシブル サーバーを選択します。
2. サイドバーの **[監視]** セクションで、 **[アラート]** を選択します。
3. **[+ 新しいアラート ルール]** を選択します。
4. **[ルールの作成]** ページが開きます。 必要な情報を入力します。
5. **[条件]** セクションで、 **[条件の選択]** を選択します。
6. サポートされているシグナルの一覧が表示されるので、アラートを作成するメトリックを選択します。 たとえば、[ストレージの割合] を選択します。
7. 過去 6 時間のメトリックのグラフが表示されます。 **[グラフの期間]** ドロップダウンを使用して、メトリックのより長期間の履歴を確認することを選択します。
8. **[しきい値]** の種類 (例: [静的] または [動的])、**演算子** (例: [次の値より大きい])、および **[集計の種類]** (例: 平均) を選択します。 これにより、メトリック アラート ルールによって評価されるロジックが決まります。
    - **静的** しきい値を使用している場合は、引き続き **[しきい値]** (例: 85%) を定義します。 メトリック グラフを使用すると、想定される妥当なしきい値を決定できます。
    - **動的な** しきい値を使用している場合は、引き続き **[しきい値の感度]** を定義します。 メトリック グラフに、最新のデータに基づいて計算されたしきい値が表示されます。 [動的しきい値の条件の種類と秘密度のオプションの詳細については、こちらをご覧ください](../../azure-monitor/alerts/alerts-dynamic-thresholds.md)。
9. データ ポイントをグループ化する **集計の粒度 (期間)** の間隔を調整し、集計の種類の関数 (例: 30 分) と **頻度** (例: 15 分ごと) を使用して、条件を絞り込みます。
10. **[Done]** をクリックします。
11. アクション グループを追加します。 アクション グループは、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。 **[アクション グループ]** セクション内で **[アクション グループの選択]** を選択して、既に存在するアクション グループを選択し、アラート ルールにアタッチします。
12. アラートに関する通知を受け取るための新しいアクション グループを作成することもできます。 詳細については、[アクション グループの作成と管理](../../azure-monitor/alerts/action-groups.md)に関するページを参照してください。
13. 新しいアクション グループを作成するには、 **[+ アクション グループの作成]** を選択します。 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[アクション グループ名]** 、 **[表示名]** を使用して、[アクション グループの作成] フォームに入力します。
14. アクショングループの **[通知]** を構成します。
    
    **[通知の種類]** で、[電子メールの Azure Resource Manager のロール] を選択して、通知を受信するサブスクリプションの所有者、共同作成者、および閲覧者を選択します。 電子メールを送信するための **Azure Resource Manager ロール** を選択します。
    また、**電子メール/SMS メッセージ/プッシュ/音声** を選択して、特定の受信者に通知を送信することもできます。

    通知の種類に **[名前]** を指定し、完了したら **[確認および作成]** を選択します。

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. **[アラート ルール名]** 、 **[説明]** 、 **[リソース グループにアラート ルールを保存します]** 、および **[重大度]** などの **[アラート ルールの詳細]** を入力します。

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. **[アラート ルールの作成]** を選択して、アラートを作成します。
    数分後にアラートがアクティブになり、前述のようにトリガーされます。
## <a name="manage-your-alerts"></a>アラートの管理
アラートを作成したら、それを選択して次のアクションを実行できます。

* このアラートに関連するメトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* アラート ルールを **編集** または **削除** する。
* アラートを **無効** にしてアラートを一時的に停止する、または **有効** にして通知の受け取りを再開する。


## <a name="next-steps"></a>次のステップ
- [メトリックへのアラートの設定](../../azure-monitor/alerts/alerts-metric.md)に関する詳細を確認する。
- 使用可能な [Azure Database for MySQL フレキシブル サーバーのメトリック](./concepts-monitoring.md)に関する詳細を確認する。
- [Azure Monitor でのメトリック アラートのしくみを理解する](../../azure-monitor/alerts/alerts-metric-overview.md)