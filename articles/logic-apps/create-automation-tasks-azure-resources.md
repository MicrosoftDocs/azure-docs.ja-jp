---
title: Azure リソースを管理および監視するための自動化タスクを作成する
description: Azure Logic Apps で実行されるワークフローを作成して、Azure リソースを管理し、コストを監視するための自動化タスクを設定します。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719051"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>自動化タスクを作成して Azure リソースを管理し、コストを監視する (プレビュー)

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure リソース](../azure-resource-manager/management/overview.md#terminology)をより簡単に管理できるように、自動化タスク テンプレートを使用して、特定のリソースまたはリソース グループの自動管理タスクを作成することができます。これは、リソースの種類によって可用性が異なります。 たとえば、[Azure ストレージ アカウント](../storage/common/storage-account-overview.md)の場合は、そのストレージ アカウントの月額料金を送信する自動化タスクを設定できます。 [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)の場合は、事前に定義したスケジュールでその仮想マシンをオンまたはオフにする自動化タスクを作成できます。

バックグラウンドでは、自動化タスクは実際には [Azure Logic Apps](../logic-apps/logic-apps-overview.md) サービス上で動作するワークフローで、同じ[価格レート](https://azure.microsoft.com/pricing/details/logic-apps/)や[価格モデル](../logic-apps/logic-apps-pricing.md)を使って課金されています。 タスクを作成したら、ロジック アプリ デザイナーでタスクを開いて、基になるワークフローを表示および編集できます。 タスクの実行が少なくとも 1 つ完了した後であれば、各実行の状態、履歴、入力、および出力を確認できます。

現在、このプレビューで使用可能なタスク テンプレートは次のとおりです。

| リソースの種類 | 自動化タスク テンプレート |
|---------------|---------------------------|
| Azure リソース グループ | **リソースが削除された時** |
| すべての Azure リソース | **リソースの月額料金を送信** |
| Azure の仮想マシン | 補足: <p>- **仮想マシンを電源オフ** <br>- **仮想マシンを起動** |
| Azure Storage アカウント | 補足: <p>- **古い BLOB の削除** |
| Azure Cosmos DB | さらに、 <p>- **クエリ結果を電子メールで送信** |
|||

この記事では、次のタスクを完了する方法について説明します。

* 特定の Azure リソースの[自動化タスクを作成](#create-automation-task)します。

* [タスクの履歴を確認](#review-task-history)します。これには、実行状態、入力、出力、およびその他の履歴情報が含まれます。

* タスクを更新したり、ロジック アプリ デザイナーでタスクの基になるワークフローをカスタマイズしたりできるように、[タスクを編集](#edit-task)します。

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>自動化タスクと Azure Automation の違い

現時点では、リソース レベルでのみ自動化タスクを作成し、タスクの実行履歴を表示して、タスクの基になるロジック アプリ ワークフローを編集することができます。これは、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) サービスを利用しています。 自動化タスクは、[Azure Automation](../automation/automation-intro.md) よりも基本的で軽量です。

これに対し、Azure Automation は、Azure および Azure 以外の環境にわたって一貫性のある管理をサポートする、クラウドベースのオートメーションおよび構成のサービスです。 このサービスは、[Runbook](../automation/automation-runbook-execution.md) を使用して[プロセスをオーケストレーションするためのプロセスの自動化](../automation/automation-intro.md#process-automation)、[変更追跡とインベントリ](../automation/change-tracking/overview.md)を含む構成管理、更新管理、共有機能、および異種環境機能で構成されています。 Automation は、ワークロードとリソースのデプロイ、運用、使用停止を完全に制御します。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* 管理する Azure リソース。 この記事では、例として Azure ストレージ アカウントを使用します。

* Office 365 アカウント (この例に従って Office 365 Outlook を使用して電子メールを送信する場合)。

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>自動化タスクの作成

1. [Azure portal](https://portal.azure.com) で、管理するリソースを検索します。

1. リソース メニューで、 **[Automation]** セクションまでスクロールし、 **[タスク]** を選択します

   ![[Automation] セクションで [タスク] メニュー項目が選択されている、Azure portal およびストレージ アカウントのリソース メニューを示すスクリーンショット。](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. **[タスク]** ウィンドウで、 **[追加]** を選択して、タスク テンプレートを選択できるようにします。

   ![ツール バーの [追加] が選択されているストレージ アカウントの [タスク] ウィンドウを示すスクリーンショット](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. **[タスクの追加]** ペインの **[テンプレートの選択]** で、作成するタスクのテンプレートを選択します。 次のページが表示されない場合は、 **[次へ: 認証]** を選択します。

   この例では、 **[リソースの月額料金を送信]** タスク テンプレートを選択することで続行します。

   !["リソースの月額料金を送信" と "Next:Authentication" (次へ: 認証) を示すスクリーンショット](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. **[認証]** の **[接続]** セクションで、タスクに表示されるすべての接続について **[作成]** を選択して、すべての接続の認証資格情報を指定できるようにします。 各タスクの接続の種類は、タスクによって異なります。

   この例では、このタスクに必要な接続のうちの 1 つだけを示します。

   ![Azure Resource Manager 接続で選択された [作成] オプションを示すスクリーンショット](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. メッセージが表示されたら、Azure アカウントの資格情報でサインインします。

   !["サインイン" を選択していることを示すスクリーンショット](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   接続が正常に認証されると、次の例のようになります。

   ![正常に作成された接続を示すスクリーンショット](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. すべての接続を認証した後、次のページが表示されない場合は **[次へ: 構成]** を選択します。

1. **[構成]** で、タスクの名前と、タスクに必要なその他の情報を指定します。 完了したら **[作成]** を選択します。

   > [!NOTE]
   > 作成後にタスク名を変更することはできません。そのため、[基になるワークフローを編集](#edit-task-workflow)したとしても、引き続き適用できる名前を検討してください。 基になるワークフローに加えた変更は、タスク テンプレートではなく、作成したタスクにのみ適用されます。
   >
   > たとえば、タスクに `Send monthly cost` という名前を付けた後で、基になるワークフローを毎週実行するように編集した場合、タスクの名前を `Send weekly cost` に変更することはできません。

   電子メール通知を送信するタスクには、電子メール アドレスが必要です。

   ![選択したタスクに必要な情報を示すスクリーンショット](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   自動的にライブで実行されている作成したタスクが、 **[Automation タスク]** 一覧に表示されるようになりました。

   ![Automation タスクの一覧を示すスクリーンショット](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > タスクがすぐに表示されない場合は、タスク一覧を最新の状態に更新するか、少し待ってから更新してください。 ツール バーの **[更新]** を選択します。

   選択したタスクが実行されると、次の例のような電子メールが届きます。

   ![タスクによって送信された電子メール通知を示すスクリーンショット](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>タスク履歴の確認

タスクの実行履歴とその状態、入力、出力、およびその他の情報を表示するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、確認するタスク履歴があるリソースを見つけます。

1. リソースのメニューで、 **[設定]** の下にある **[Automation タスク]** を選択します。

1. [タスク] 一覧で、確認するタスクを見つけます。 このタスクの **[実行]** 列で、 **[表示]** を選択します。

   ![タスクと選択された [表示] オプションを示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   **[実行履歴]** ウィンドウには、タスクのすべての実行とその状態、開始時刻、識別子、および実行継続時間が表示されます。

   ![タスクの実行、状態、およびその他の情報を示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   実行には次のような状態があります。

   | Status | 説明 |
   |--------|-------------|
   | **取り消し済み** | タスクは実行中に取り消されました。 |
   | **Failed** | タスクには少なくとも 1 つの失敗したアクションがありますが、失敗を処理するための後続のアクションが存在しませんでした。 |
   | **実行中** | タスクは現在実行中です。 |
   | **Succeeded** | アクションはすべて成功しています。 アクションが失敗した場合でもタスクは正常に完了しますが、失敗を処理するための後続のアクションが存在していました。 |
   | **待機中** | 実行はまだ開始されていません。タスクの以前のインスタンスがまだ実行中であるため、一時停止しています。 |
   |||

   詳細については、「[実行履歴を確認する](../logic-apps/monitor-logic-apps.md#review-runs-history)」を参照してください。

1. 実行の各ステップの状態などの情報を表示するには、その実行を選択します。

   **[ロジック アプリの実行]** ウィンドウが開き、基になる実行されたワークフローが表示されます。

   * ワークフローは、常に [*トリガー*](../connectors/apis-list.md#triggers-actions)で開始されます。 このタスクでは、ワークフローは [**繰り返し** トリガー](../connectors/connectors-native-recurrence.md)で開始されます。

   * 各ステップには、状態と実行継続時間が表示されます。 継続時間が 0 秒のステップは、実行が 1 秒未満であったものです。

   ![実行、状態、および実行継続時間の各ステップを示すスクリーンショット](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 各ステップの入力と出力を確認するには、ステップを選択して展開します。

   この例では、繰り返しトリガーの入力を示しています。このトリガーは、ワークフローの実行時のみを指定し、後続の処理のための出力を提供しないため、出力がありません。

   ![展開されたトリガーと入力を示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   これに対して、 **[電子メールの送信]** アクションには、ワークフローの前のアクションからの入力と、出力があります。

   ![展開されたアクション、入力、および出力を示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

アプリ、データ、サービス、およびシステムを、Azure リソースの自動化タスクのコンテキストとは別に統合できるように、独自の自動化されたワークフローを構築する方法については、[クイックスタート:Azure Logic Apps を使用して初めての統合ワークフローを作成する - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) に関するページを参照してください。

<a name="edit-task"></a>

## <a name="edit-the-task"></a>タスクを編集する

タスクを変更するには、次のオプションがあります。

* 接続情報や構成情報などのプロパティ (メール アドレス など) を変更できるように、[タスクを "インライン" で編集](#edit-task-inline)します。

* ロジック アプリ デザイナーで、[タスクの基になるワークフローを編集](#edit-task-workflow)します。

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>タスクをインラインで編集する

1. [Azure portal](https://portal.azure.com) で、更新するタスクが含まれているリソースを見つけます。

1. リソースのメニューで、 **[Automation]** の下にある **[タスク]** を選択します。

1. [タスク] 一覧で、更新するタスクを見つけます。 タスクの省略記号 ( **...** ) メニューを開き、 **[インラインで編集]** を選択します。

   ![開かれた省略記号メニューと、選択されている [インラインで編集] オプションを示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   既定では、 **[認証]** タブが表示され、既存の接続が表示されます。

1. 新しい認証資格情報を追加するか、接続に使用する既存の認証資格情報を選択するには、接続の省略記号 ( **...** ) メニューを開き、 **[新しい接続の追加]** または使用可能な場合は別の認証資格情報を選択します。

   ![[認証] タブ、既存の接続、および選択した省略記号メニューを表示するスクリーンショット](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. その他のタスクのプロパティを更新するには **[次へ:構成]** を選択します。

   この例のタスクでは、編集できる唯一のプロパティはメール アドレスです。

   ![[構成] タブを示すスクリーンショット](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 終了したら、 **[保存]** を選択します。

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>タスクの基になるワークフローの編集

自動化タスクの基になるワークフローを変更すると、変更内容は、タスクを作成するテンプレートではなく、作成したタスク インスタンスにのみ影響を与えます。 変更を行って保存した後、元のタスクに指定した名前ではタスクを正確に説明できなくなる可能性があるため、別の名前でタスクを作り直す必要があるかもしれません。

> [!TIP]
> ベスト プラクティスとして、基になるワークフローを複製し、コピーしたバージョンを代わりに編集できるようにします。 こうすることで、既存の機能を中断したり壊したりするリスクを冒すことなく、元の自動化タスクが動作し続けている間にコピー上で変更を行ってテストすることができます。 変更が完了し、新しいバージョンが正常に実行されることを確認したら、元の自動化タスクを無効にするか削除し、自動化タスクで複製されたバージョンを使用することができます。 次の手順には、ワークフローを複製する方法に関する情報が含まれています。

1. [Azure portal](https://portal.azure.com) で、更新するタスクが含まれているリソースを見つけます。

1. リソースのメニューで、 **[Automation]** の下にある **[タスク]** を選択します。

1. [タスク] 一覧で、更新するタスクを見つけます。 タスクの省略記号 ( **...** ) メニューを開き、 **[Open in Logic Apps]\(Logic Apps で開く\)** を選択します。

   ![開かれた省略記号メニューと、選択されている [Open in Logic Apps]\(Logic Apps で開く\) オプションを示すスクリーンショット](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   タスクの基になるワークフローが Azure Logic Apps サービスで開き、 **[概要]** ウィンドウが表示されます。ここでは、タスクで使用できるのと同じ実行履歴を表示できます。

   ![[概要] ウィンドウが選択された Azure Logic Apps ビューのタスクを示すスクリーンショット](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. ロジック アプリ デザイナーで基になるワークフローを開くには、ロジック アプリのメニューで **[ロジック アプリ デザイナー]** を選択します。

   !["ロジック アプリ デザイナー" メニュー オプションが選択され、基になるワークフローが表示されたデザイナー画面を示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   ワークフローのトリガーとアクションのプロパティを編集したり、ワークフロー自体を定義するトリガーやアクションを編集したりできるようになりました。 ただし、ベスト プラクティスとして、ワークフローを複製する手順に従って、元のワークフローが引き続き動作して実行されている間にコピーに変更を加えることができるようにします。

1. ワークフローを複製し、コピーされたバージョンを編集するには、次の手順を実行します。

   1. ロジック アプリ ワークフロー メニューで、 **[概要]** を選択します。

   1. [概要] ウィンドウのツール バーで、 **[複製]** を選択します。

   1. ロジック アプリの作成ウィンドウで、 **[名前]** の下に、コピーしたロジック アプリ ワークフローの新しい名前を入力します。

      **[ロジック アプリの状態]** を除き、他のプロパティを編集することはできません。 
      
   1. **[ロジック アプリの状態]** で、 **[無効]** を選択して、変更を行っている間に複製されたワークフローが実行されないようにします。 変更をテストする準備ができたら、ワークフローを有効にできます。

   1. Azure での複製されたワークフローのプロビジョニングが完了したら、ロジック アプリ デザイナーでそのワークフローを見つけて開きます。

1. トリガーまたはアクションのプロパティを表示するには、そのトリガーまたはアクションを展開します。

   たとえば、繰り返しトリガーを、毎月ではなく毎週実行するように変更できます。

   ![使用可能な頻度のオプションを表示するために、頻度の一覧が開かれている、展開された繰り返しトリガーを示すスクリーンショット](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   繰り返しトリガーについて詳しくは、[繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する](../connectors/connectors-native-recurrence.md)方法に関する記事を参照してください。 使用できるその他のトリガーとアクションの詳細については、「[Azure Logic Apps のコネクタ](../connectors/apis-list.md)」を参照してください。

1. 変更を保存するには、デザイナーのツール バーで、 **[保存]** を選択します。

   ![デザイナーのツール バーと選択された [保存] コマンドを示すスクリーンショット](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 更新されたワークフローをテストして実行するには、デザイナーのツール バーで **[実行]** を選択します。

   実行が完了すると、デザイナーにワークフローの実行の詳細が表示されます。

   ![デザイナーで表示されているワークフローの実行の詳細を示すスクリーンショット](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. タスクが実行を続行しないようにワークフローを無効にするには、「[Azure portal でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-azure-portal.md)」を参照してください。

## <a name="provide-feedback"></a>ご意見とご感想

皆様からのご意見をお待ちしております。 バグを報告したり、フィードバックを提供したり、このプレビュー機能について質問したりするには、[Azure Logic Apps チームにお問い合わせ](mailto:logicappspm@microsoft.com)ください。

## <a name="next-steps"></a>次のステップ

* [Azure portal でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-azure-portal.md)
