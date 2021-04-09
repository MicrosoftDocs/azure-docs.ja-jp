---
title: アラートの構成 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL の Hyperscale (Citus) でのメトリック アラートを構成してアクセスする方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577082"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure portal を使用して Azure Database for PostgreSQL でのメトリックに対するアラートを設定する - Hyperscale (Citus)

この記事では、Azure Portal を使用して Azure Database for PostgreSQL のアラートを設定する方法について説明します。 お使いの Azure のサービスの[監視メトリック](concepts-hyperscale-monitoring.md)に基づいて、アラートを受け取ることができます。

指定したメトリックの値がしきい値を超えた場合に、アラートがトリガーされるように設定します。 条件が最初に一致したときにアラートがトリガーされ、その後も引き続きトリガーが行われます。

アラートがトリガーされたときに実行されるように構成できるアクションは次のとおりです。
* サービスの管理者および共同管理者に電子メール通知を送信する。
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure 監視 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal でメトリックのアラート ルールを作成する
1. [Azure Portal](https://portal.azure.com/) で、監視する Azure Database for PostgreSQL サーバーを選択します。

2. 次のように、サイドバーの **[監視]** セクションで、 **[アラート]** を選択します。

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="アラート ルールを選択する":::

3. **[新しいアラート ルール]** (+ アイコン) を選択します。

4. 以下のように、 **[ルールの作成]** ページが開きます。 必要な情報を入力します。

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="メトリック アラート フォームを追加する":::

5. **[条件]** セクション内で、 **[追加]** を選択します。

6. アラート通知のシグナルの一覧からメトリックを選択します。 この例では、[ストレージの割合] を選択します。
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="スクリーンショットは、複数のシグナルを表示できる [シグナル ロジックの構成] ページを示しています。":::

7. 次のアラート ロジックを構成します。

    * **演算子** (例: "より大きい")
    * **しきい値** (例: 85 パーセント)
    * **[時間の集計]** 、アラートがトリガーされる前にメトリック ルールが確実に満たされるまでの期間 (例: "直近 30 分")
    * **[評価の頻度]** (例: "1 分")
   
   完了したら、 **[完了]** を選択します。

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="スクリーンショットは、アラート ロジックを構成できるウィンドウを示しています。":::

8. **[アクション グループ]** セクション内で **[新規作成]** を選択して、アラートの通知を受信する新しいグループを作成します。

9. [アクション グループの追加] フォームに、名前、短い名前、サブスクリプション、リソース グループを入力します。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="スクリーンショットは、[アクション グループの追加] フォームを示しています。ここで、説明の値を入力できます。":::

10. アクションの種類で、 **[電子メール/SMS/プッシュ/音声]** を構成します。
    
    [電子メールの Azure Resource Manager のロール] を選択して、サブスクリプション所有者、共同作成者、および閲覧者に通知を送信します。
   
    完了したら、 **[OK]** を選択します。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="スクリーンショットは、[電子メール/SMS/プッシュ/音声] ウィンドウを示しています。":::

11. [アラート ルール名]、[説明]、[重大度] を指定します。

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="スクリーンショットは、[アラートの詳細] ウィンドウを示しています。"::: 

12. **[アラート ルールの作成]** を選択して、アラートを作成します。

    数分後にアラートがアクティブになり、前述のようにトリガーされます。

### <a name="managing-alerts"></a>アラートの管理

アラートを作成したら、それを選択して次のアクションを実行できます。

* このアラートに関連するメトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* アラート ルールを **編集** または **削除** する。
* アラートを **無効** にしてアラートを一時的に停止する、または **有効** にして通知の受け取りを再開する。

## <a name="suggested-alerts"></a>推奨されるアラート

### <a name="disk-space"></a>ディスク領域

監視とアラートは、すべての運用環境の Hyperscale (Citus) サーバー グループにとって重要です。 正常に動作するには、基になる PostgreSQL データベースに空きディスク領域が必要です。 ディスクがいっぱいになると、データベース サーバー ノードはオフラインになり、領域が使用可能になるまで起動できません。 その時点で、この状況を解決するには、Microsoft にサポートを要求する必要があります。

運用環境以外で使用する場合であっても、すべてのサーバー グループのすべてのノードでディスク領域アラートを設定することをお勧めします。 ディスク領域使用量アラートでは、介入してノードを正常な状態に保つために必要な事前警告が提供されます。 最良の結果を得るには、75%、85%、95% の使用率で一連のアラートを試してみてください。 データ インジェストが速いほどディスクがいっぱいになるのも速いため、選択するパーセンテージは、データ インジェストの速度に依存します。

ディスクが容量制限に近づいたら、次の方法で空き領域を増やしてみます。

* データ保持ポリシーを検討します。 可能であれば、古いデータをコールド ストレージに移動します。
* サーバー グループに[ノードを追加](howto-hyperscale-scale-grow.md#add-worker-nodes)し、シャードを再調整することを検討します。 再調整を行うと、より多くのコンピューターにデータが分散されます。
* ワーカー ノードの[容量を増やす](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes)ことを検討します。 各ワーカーに許されるストレージの最大量は 2 TiB です。 ただし、ノードを追加する方が短時間で完了するため、ノードのサイズを変更する前に、ノードを追加する必要があります。

### <a name="cpu-usage"></a>CPU 使用率

CPU 使用率の監視は、パフォーマンスのベースラインを確立するのに役立ちます。 たとえば、CPU 使用率が通常は約 40 - 60% であるとします。 CPU 使用率が急に 95% 程度まで上昇し始めたら、異常があることがわかります。 CPU 使用率は、有機的成長を反映する場合もありますが、不適切なクエリを示していることもあります。 CPU アラートを作成するときは、長期間の増加が検出されて、瞬間的な急増が無視されるように、集計単位を長く設定します。

## <a name="next-steps"></a>次のステップ
* [アラートでの webhook の構成](../azure-monitor/alerts/alerts-webhooks.md)に関する詳細情報を確認します。
* [メトリック収集の概要](../azure-monitor/data-platform.md) 情報を入手して、サービスの可用性と応答性を確認します。
