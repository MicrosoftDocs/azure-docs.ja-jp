---
title: 状態を監視し、履歴を表示し、アラートを設定する
description: 実行状態の確認、トリガー履歴の確認、およびアラートの有効化による、 Azure Logic Apps でのロジック アプリのトラブルシューティング
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580666"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Azure Logic Apps での実行状態の監視、トリガー履歴の確認、アラートの設定

[ロジック アプリを作成して実行する](../logic-apps/quickstart-create-first-logic-app-workflow.md)と、ロジック アプリの実行状態、[実行の履歴](#review-runs-history)、[トリガー履歴](#review-trigger-history)、およびパフォーマンスを確認できます。 エラーやその他考えられる問題に関する通知を受け取るには、[アラート](#add-azure-alerts)を設定します。 たとえば、"1 時間に 5 件を超える実行が失敗したとき" を検出するアラートを作成できます。

リアルタイムでのイベントの監視と高度なデバッグを行うには、[Azure Monitor ログ](../azure-monitor/overview.md)を使用してロジック アプリの診断ログを設定します。 この Azure サービスを使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 これにより、トリガー イベント、実行イベント、アクション イベントなどのイベントを検索して表示できます。 この情報を [Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)に格納することで、この情報の検索と分析に役立つ[ログ クエリ](../azure-monitor/logs/log-query-overview.md)を作成できます。 この診断データは、Azure Storage や Azure Event Hubs などの他の Azure サービスで使用することもできます。 詳細については、「[Azure Monitor を使用してロジック アプリを監視する](../logic-apps/monitor-logic-apps-log-analytics.md)」を参照してください。

> [!NOTE]
> [内部アクセス エンドポイント](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)を使用するように作成された [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) でロジック アプリを実行する場合、*仮想ネットワーク内からのみ*、ロジック アプリの実行履歴の入力と出力を表示してアクセスできます。 プライベート エンドポイントと、実行履歴へのアクセス元として使用するコンピューターの間にネットワーク接続があることを確認します。 たとえば、クライアント コンピューターは、ISE の仮想ネットワーク内、または ISE の仮想ネットワークに接続されている仮想ネットワーク内に配置できます (たとえば、ピアリングや仮想プライベート ネットワークを使用します)。 詳細については、「[ISE エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>実行履歴を確認する

項目またはイベントに対してトリガーが発生する度に、Logic Apps エンジンによって項目またはイベントごとに個別のワークフロー インスタンスが作成され、実行されます。 既定では、各ワークフロー インスタンスは並列で実行されるため、ワークフローは待ち時間なしで実行が開始されます。 実行中に何が発生したか、たとえば、ワークフローの各ステップの状態や、各ステップでの入出力を確認することができます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで、ロジック アプリを探して開きます。

   ロジック アプリを検索するには、Azure のメイン検索ボックスに「`logic apps`」と入力し、 **[Logic Apps]** を選択します。

   !["Logic Apps" サービスを検索して選択する](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal には、所有する Azure サブスクリプションに関連付けられているロジック アプリがすべて表示されます。 この一覧は、名前、サブスクリプション、リソース グループ、場所などに基づいてフィルター処理できます。

   ![サブスクリプションに関連付けられているロジック アプリの表示](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. ロジック アプリを選択して、 **[概要]** を選択します。

   [概要] ウィンドウの **[実行の履歴]** の下に、ロジック アプリの過去、現在、および待機中の実行がすべて表示されます。 一覧に多数の実行が表示され、必要なエントリが見つからない場合は、一覧をフィルター処理してみてください。

   > [!TIP]
   > 実行の状態が表示されない場合は、 **[更新]** を選択して [概要] ページを更新してみてください。 条件が満たされなかったか、データが見つからなかったためにスキップされたトリガーに対しては、実行は行われません。

   ![概要、実行の履歴、およびその他のロジック アプリ情報](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   次のような実行の状態があります。

   | 実行の状態 | 説明 |
   |------------|-------------|
   | **Aborted** | 外部に問題が発生したため、実行が停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | 実行がトリガーされ、開始されましたが、キャンセル要求を受け取りました。 |
   | **Failed** | 実行中に少なくとも 1 つのアクションが失敗しました。 ワークフローの後続のアクションは、エラーを処理するように設定されていません。 |
   | **実行中** | 実行がトリガーされ、進行中です。ただし、この状態は、[アクション制限](logic-apps-limits-and-config.md)または[現在の料金プラン](https://azure.microsoft.com/pricing/details/logic-apps/)によって制限されている実行に対しても表示されます。 <p><p>**ヒント**:[診断ログ](monitor-logic-apps-log-analytics.md)を設定すると、発生するスロットル イベントに関する情報を取得することができます。 |
   | **Succeeded** | 実行は成功しました。 いずれかのアクションが失敗した場合、ワークフロー内の後続のアクションによってそのエラーが処理されます。 |
   | **タイムアウト** | 現在の継続時間が実行継続時間の制限を超えたため、実行がタイムアウトしました。これは、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行の継続時間は、実行の開始時刻とその開始時刻での実行継続時間の制限を使用して計算されます。 <p><p>**注**:実行の継続時間が現在の *"実行履歴の保持期間の制限"* も超えている場合は、毎日のクリーンアップ ジョブによって実行履歴から実行が消去されます。これも、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行がタイムアウトしたか完了したかにかかわらず、保持期間は常に、実行の開始時刻と "*現在の*" 保持期間の制限を使用して計算されます。 そのため、処理中の実行の継続時間制限を短くすると、実行はタイムアウトします。ただし、実行が実行履歴に残るか消去されるかは、実行の継続時間が保持期間の制限を超えているかどうかに基づいて決まります。 |
   | **待機中** | たとえば、その前のワークフロー インスタンスがまだ実行中である等の理由で、実行が開始されていないか、または一時停止しています。 |
   |||

1. 特定の実行についてのステップとその他の情報を確認するには、 **[実行の履歴]** でその実行を選択します。

   ![確認する特定の実行を選択する](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **[ロジック アプリの実行]** ウィンドウには、選択した実行の各ステップ、各ステップの実行状態、各ステップの実行にかかった時間が表示されます。次に例を示します。

   ![特定の実行の各アクション](./media/monitor-logic-apps/logic-app-run-pane.png)

   この情報をリスト形式で表示するには、 **[ロジック アプリの実行]** ツール バーで **[実行の詳細]** を選択します。

   ![ツール バーで [実行の詳細] を選択する](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   [実行の詳細] ビューには、各ステップ、その状態、およびその他の情報が表示されます。

   ![実行の各ステップの詳細を確認する](./media/monitor-logic-apps/review-logic-app-run-details.png)

   たとえば、その実行の **関連付け ID** プロパティを取得できます。これは、[Logic Apps 用の REST API](/rest/api/logic) を使用する際に必要になる場合があります。

1. 特定のステップに関する詳細情報を取得するには、次のいずれかのオプションを選択します。

   * **[ロジック アプリの実行]** ウィンドウで、ステップを選択して図形を展開します。 入力、出力、およびそのステップで発生したエラーなどの情報を表示できるようになりました。次に例を示します。

     ![[ロジック アプリの実行] ウィンドウで、失敗したステップを表示する](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * **[ロジック アプリの実行の詳細]** ウィンドウで、目的のステップを選択します。

     ![[実行の詳細] ウィンドウで、失敗したステップを表示する](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     そのステップの入力や出力などの情報を表示できるようになりました。次に例を示します。

   > [!NOTE]
   > Logic Apps サービス内では、実行時の詳細情報とイベントはすべて暗号化されます。 これらの暗号化が解除されるのは、ユーザーがそのデータの表示を要求したときのみです。 [実行履歴の入力と出力を非表示にしたり](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用して、この情報へのユーザー アクセスを制御したりすることができます。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>トリガー履歴を確認する

各ロジック アプリの実行は、トリガーによって開始されます。 このトリガー履歴には、ロジック アプリによるすべてのトリガーの試行と、各トリガーの試行の入出力に関する情報が一覧表示されます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで、ロジック アプリを探して開きます。

   ロジック アプリを検索するには、Azure のメイン検索ボックスに「`logic apps`」と入力し、 **[Logic Apps]** を選択します。

   !["Logic Apps" サービスを検索して選択する](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal には、所有する Azure サブスクリプションに関連付けられているロジック アプリがすべて表示されます。 この一覧は、名前、サブスクリプション、リソース グループ、場所などに基づいてフィルター処理できます。

   ![サブスクリプションに関連付けられているロジック アプリの表示](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. ロジック アプリを選択して、 **[概要]** を選択します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 **[概要]** セクションの **[評価]** で、 **[See trigger history]\(トリガー履歴を表示する\)** を選択します。

   ![ロジック アプリのトリガー履歴を表示する](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   [トリガー履歴] ウィンドウには、ロジック アプリが行ったすべてのトリガーの試行が表示されます。 項目またはイベントに対してトリガーが発生する度に、Logic Apps エンジンによってワークフローを実行するロジック アプリ インスタンスが個別に作成されます。 既定では、各インスタンスが並列で実行されるため、ワークフローは待ち時間なしで実行が開始されます。 したがって、ロジック アプリが複数の項目を同時にトリガーする場合は、同じ日付と時刻をもつトリガー エントリが各項目に対して表示されます。

   ![さまざまな項目に対する複数のトリガーの試行](./media/monitor-logic-apps/logic-app-trigger-history.png)

   トリガー試行には次のような状態があります。

   | トリガー状態 | 説明 |
   |----------------|-------------|
   | **Failed** | エラーが発生しました。 失敗したトリガーに対して生成されたエラー メッセージを確認するには、そのトリガー試行を選択し、 **[出力]** を選択します。 たとえば、無効な入力が見つかる場合があります。 |
   | **Skipped** | トリガーによりエンドポイントがチェックされましたが、指定された条件を満たすデータが見つかりませんでした。 |
   | **Succeeded** | トリガーによりエンドポイントがチェックされ、使用可能なデータが見つかりました。 通常、この状態は、 **[起動済み]** と共に表示されます。 表示されない場合、トリガー定義に、十分でない条件または `SplitOn` コマンドが含まれる可能性があります。 <p><p>この状態は、手動トリガー、繰り返しトリガー、またはポーリング トリガーに適用できます。 トリガーは正常に実行できますが、アクションによって未処理のエラーが生成されると、実行自体が引き続き失敗する可能性があります。 |
   |||

   > [!TIP]
   > トリガーの再チェックは、そのトリガーが次回起動されるのを待たずに実行できます。 [概要] ツール バーで、 **[トリガーの実行]** を選択してトリガーを選択すると、チェックが強制的に実行されます。 または、Logic Apps デザイナー ツール バーで **[実行]** を選択します。

1. 特定のトリガーの試行に関する情報を表示するには、[トリガー] ウィンドウで、そのトリガー イベントを選択します。 一覧に多数のトリガー試行が表示され、必要なエントリが見つからない場合は、一覧にフィルターを適用してみます。 必要なデータが見つからない場合は、ツール バーの **[更新]** を選択してみます。

   ![特定のトリガーの試行を表示する](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   選択したトリガー イベントに関する情報を確認できるようになりました。次に例を示します。

   ![特定のトリガー情報を表示する](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>監視アラートを設定する

ロジック アプリの特定のメトリックまたはしきい値の超過に基づいてアラートを発生させるには、[Azure Monitor のアラート](../azure-monitor/alerts/alerts-overview.md)を設定します。 [Azure のメトリック](../azure-monitor/data-platform.md)について確認してください。 [Azure Monitor](../azure-monitor/logs/log-query-overview.md) を使用せずにアラートを設定するには、次の手順を実行します。

1. ロジック アプリのメニューで、 **[監視]** にある **[アラート]**  >  **[新しいアラート ルール]** を選択します。

   ![ロジック アプリのアラートを追加する](./media/monitor-logic-apps/add-new-alert-rule.png)

1. **[ルールの作成]** ウィンドウの **[リソース]** で、ロジック アプリを選択します (まだ選択されていない場合)。 **[条件]** の **[追加]** を選択して、アラートをトリガーする条件を定義できるようにします。

   ![ルールの条件を追加する](./media/monitor-logic-apps/add-condition-for-rule.png)

1. **[シグナル ロジックの構成]** ウィンドウで、アラートを発生させるシグナルを検索して選択します。 検索ボックスを使用できます。またはシグナルをアルファベット順に並べ替えるには、 **[シグナル名]** 列ヘッダーを選択します。

   たとえば、トリガーが失敗したときにアラートを送信する場合は、次の手順を実行します。

   1. **[シグナル名]** 列で、 **[失敗したトリガー]** シグナルを見つけて選択します。

      ![アラートを作成するためのシグナルを選択する](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 選択したシグナルについて表示される情報ウィンドウの **[アラート ロジック]** で、次のように条件を設定します。

   1. **[演算子]** には、 **[次の値以上]** を選択します。

   1. **[集計の種類]** は、 **[カウント]** を選択します。

   1. **[しきい値]** には「`1`」を入力します。

   1. **[条件のプレビュー]** で、条件が正しく表示されることを確認します。

   1. **[評価基準]** で、アラート ルールを実行する間隔と頻度を設定します。 **[集約粒度 (期間)]** で、データをグループ化する期間を選択します。 **[評価の頻度]** で、条件を確認する頻度を選択します。

   1. 準備ができたら、 **[完了]** を選択します。

   完成した条件は次のとおりです。

   ![アラートの条件を設定する](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   **[ルールの作成]** ページに、作成した条件とそのアラートを実行するためのコストが表示されます。

   ![[ルールの作成] ページの新しいアラート](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. アラートの名前、オプションの説明、重大度のレベルを指定します。 **[ルールの作成時に有効にする]** 設定をオンのままにするか、ルールを有効にする準備が整うまでオフにします。

1. 完了したら、 **[アラート ルールの作成]** を選択します。

> [!TIP]
> アラートからロジック アプリを実行するために、[要求トリガー](../connectors/connectors-native-reqres.md)をワークフローに含めることができます。これにより、次の例のようなタスクを実行できます。
> 
> * [Slack に投稿する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [テキストを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [メッセージをキューに追加する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor を使用してロジック アプリを監視する](../logic-apps/monitor-logic-apps-log-analytics.md)
