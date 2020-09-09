---
title: Azure Automation Update Management のアラートを作成する方法
description: この記事では、更新プログラムの評価または展開の状態について通知するように Azure アラートを構成する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2c39a07ceac4d36bf3ef7394927589b53da7d789
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449722"
---
# <a name="how-to-create-alerts-for-update-management"></a>Update Management のアラートを作成する方法

Azure のアラートでは、Runbook ジョブの結果、サービス正常性の問題、または Automation アカウントに関連するその他のシナリオが事前に通知されます。 Azure Automation には事前に構成されたアラート ルールは含まれませんが、生成されるデータに基づいて独自のものを作成することができます。 この記事では、Update Management に含まれるメトリックを使用してアラート ルールを作成する方法について説明します。

## <a name="available-metrics"></a>使用可能なメトリック

Azure Automation では、Update Management に関連する 2 つの異なるプラットフォーム メトリックが作成、収集されて Azure Monitor に転送されます。 これらのメトリックは、[メトリックス エクスプローラー](../../azure-monitor/platform/metrics-charts.md)を使用した分析、および[メトリック アラート ルール](../../azure-monitor/platform/alerts-metric.md)を使用したアラートに使用できます。

次の 2 つのメトリックが生成されます。

* 更新プログラムのデプロイ マシンの合計実行回数
* 更新プログラムのデプロイの合計実行回数

どちらのメトリックも、アラートに使用すると、アラートを特定の更新プログラムの展開の詳細に限定するための追加情報を含むディメンションがサポートされます。 次の表では、アラートを構成するときに使用できるメトリックとディメンションの詳細を示します。

|シグナル名|Dimensions|説明
|---|---|---|
|`Total Update Deployment Runs`|- Update Deployment Name (更新プログラムの展開の名前)<br>- Status (状態) | 更新プログラムの展開の全体的な状態に関するアラート。|
|`Total Update Deployment Machine Runs`|- Update Deployment Name (更新プログラムの展開の名前)</br>- Status (状態)</br>- Target Computer (ターゲット コンピューター)</br>- Update Deployment Run ID (更新プログラムの展開の実行 ID)    |特定のマシンを対象とした更新プログラムの展開の状態に関するアラート。|

## <a name="create-alert"></a>アラートを作成する

更新プログラムの展開の状態を通知するアラートを設定するには、次の手順のようにします。 Azure アラートを初めて使用する場合は、[Azure アラートの概要](../../azure-monitor/platform/alerts-overview.md)に関するページを参照してください。

1. お使いの Automation アカウントで、 **[監視]** の **[アラート]** を選択し、 **[新しいアラート ルール]** を選択します。

2. **[アラート ルールの作成]** ページでは、お使いの Automation アカウントがリソースとして既に選択されています。 これを変更する場合は、 **[リソースの編集]** を選択します。

3. [リソースを選択してください] ページで、 **[リソースの種類でフィルター]** ドロップダウンの一覧から **[Automation アカウント]** を選択します。

4. 使用する Automation アカウントを選択し、 **[完了]** を選択します。

5. **[条件の追加]** を選択し、要件に適したシグナルを選択します。

6. ディメンションには、リストから有効な値を選択します。 必要な値がリストにない場合は、ディメンションの横にある **\+** を選択してカスタム名を入力します。 その後、検索対象の値を選択します。 ディメンションにすべての値を選択したい場合は、 **[選択 \*]** ボタンを選択します。 ディメンションに値を選択しないと、Update Management ではそのディメンションが無視されます。

    ![シグナル ロジックの構成](./media/update-mgmt-manage-updates-for-vm/signal-logic.png)

7. **[アラート ロジック]** の下の、 **[時間の集計]** と **[しきい値]** のフィールドに値を入力し、 **[完了]** を選択します。

8. 次のページで、アラートの名前と説明を入力します。

9. **[重大度]** フィールドを、成功した実行には **[Informational(Sev 2)]\(情報 (重大度 2)\)** に設定し、失敗した実行には **[Informational(Sev 1)]\(情報 (重大度 1)\)** に設定します。

    ![シグナル ロジックの構成](./media/update-mgmt-manage-updates-for-vm/define-alert-details.png)

10. **[はい]** を選択してアラート ルールを有効にします。

## <a name="configure-action-groups-for-your-alerts"></a>アラートにアクション グループを構成する

アラートを構成したら、アクション グループを設定できます。これは複数のアラートに対して使用するアクションのグループです。 このアクションには、メール通知、Runbook、Webhook など多くのものを含めることができます。 アクション グループの詳細については、[アクション グループの作成および管理](../../azure-monitor/platform/action-groups.md)に関するページを参照してください。

1. アラートを選択し、 **[アクション グループ]** で **[新規作成]** を選択します。

2. アクション グループの完全な名前と短い名前を入力します。 Update Management では、指定のグループを使用して通知を送信する場合に短縮名を使用します。

3. **[アクション]** で、アクションを指定する名前 (**電子メール通知**など) を入力します。

4. **[アクションの種類]** には、適切な種類 (**メール、SMS、プッシュ、音声**など) を選択します。

5. **[詳細の編集]** を選択します。

6. アクションの種類のペインに入力します。 たとえば、**メール、SMS、プッシュ、音声**を使用している場合は、アクション名を入力し、 **[電子メール]** チェックボックスをオンにして、有効なメール アドレスを入力し、 **[OK]** を選択します。

    ![電子メール アクション グループの構成](./media/update-mgmt-manage-updates-for-vm/configure-email-action-group.png)

7. [アクション グループの追加] ペインで、 **[OK]** を選択します。

8. アラート メールでは、メールの件名をカスタマイズできます。 **[ルールの作成]** で **[アクションをカスタマイズする]** を選択し、 **[メールの件名]** を選択します。

9. 完了したら、 **[アラート ルールの作成]** を選択します。

## <a name="next-steps"></a>次のステップ

