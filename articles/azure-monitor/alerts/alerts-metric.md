---
title: Azure Monitor を使用してメトリック アラートを作成、表示、管理する
description: Azure portal または CLI を使用して、メトリック アラート ルールを作成、表示、管理する方法について説明します。
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 0b2f70e2727832514f1ce92d1ce0da90f0a6a2e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038035"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Azure Monitor を使用してメトリック アラートを作成、表示、管理する

Azure Monitor のメトリック アラートには、メトリックのいずれかがしきい値を超えた場合に通知を受け取る方法が用意されています。 メトリック アラートは、多次元プラットフォーム メトリック、カスタム メトリック、Application Insights の標準メトリックとカスタム メトリックで動作します。 この記事では、Azure portal および Azure CLI を使用して、メトリック アラート ルールを作成、表示、管理する方法について説明します。 Azure Resource Manager テンプレートを使用してメトリック アラート ルールを作成することもできます。これについては、[別の記事](./alerts-metric-create-templates.md)で説明されています。

メトリック アラートの動作について詳しくは、[メトリック アラートの概要](./alerts-metric-overview.md)に関するページをご覧ください。

## <a name="create-with-azure-portal"></a>Azure Portal での作成

次の手順では、Azure portal でメトリック アラート ルールを作成する方法について説明します。

1. [Azure portal](https://portal.azure.com) で、 **[モニター]** をクリックします。 [モニター] ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

2. **[アラート]** をクリックして、 **[+ 新しいアラート ルール]** をクリックします。

    > [!TIP]
    > ほとんどのリソース ブレードにも **[監視]** のリソース メニューに **[アラート]** があり、そこからもアラートを作成できます。

3. **[ターゲットの選択]** をクリックし、読み込まれるコンテキスト ウィンドウで、アラートを設定するターゲット リソースを選択します。 **サブスクリプション** と **リソースの種類** のドロップダウン リストを使用して、監視するリソースを検索します。 検索バーを使用して、リソースを検索することもできます。

4. 選択したリソースにアラートを作成できるメトリックがある場合は、右下の **[使用可能なシグナル]** にメトリックが表示されます。 メトリック アラートでサポートされているリソースの種類の完全な一覧については、[こちらの記事](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)をご覧ください。

5. ターゲット リソースを選択した後、 **[条件の追加]** をクリックします。

6. リソースに対してサポートされているシグナルの一覧が表示されるので、アラートを作成するメトリックを選択します。

7. 過去 6 時間のメトリックのグラフが表示されます。 **[グラフの期間]** ドロップダウンを使用して、メトリックのより長期間の履歴を確認することを選択します。

8. メトリックにディメンションがある場合は、ディメンション テーブルが表示されます。 ディメンションごとに 1 つまたは複数の値を選択します。
    - 表示されるディメンション値は、過去 1 日間からのメトリック データに基づいています。
    - 探しているディメンション値が表示されない場合は、[カスタム値を追加] をクリックしてカスタム ディメンション値を追加します。
    - また、任意のディメンションの **現在および将来の値をすべて選択する** こともできます。 これにより、選択範囲がディメンションの現在と将来のすべての値に動的にスケーリングされます。

    メトリック アラート ルールでは、選択された値のすべての組み合わせについての条件が評価されます。 [多次元メトリックでのアラート生成のしくみの詳細を参照してください](./alerts-metric-overview.md)。

9. **[しきい値]** の種類、 **[演算子]** 、および **[集計の種類]** を選択します。 これにより、メトリック アラート ルールによって評価されるロジックが決まります。
    - **静的な** しきい値を使用している場合は、引き続き **[しきい値]** を定義します。 メトリック グラフを使用すると、想定される妥当なしきい値を決定できます。
    - **動的な** しきい値を使用している場合は、引き続き **[しきい値の感度]** を定義します。 メトリック グラフに、最新のデータに基づいて計算されたしきい値が表示されます。 [動的しきい値の条件の種類と秘密度のオプションの詳細については、こちらをご覧ください](../alerts/alerts-dynamic-thresholds.md)。

10. 必要に応じて、 **[Aggregation granularity]\(集計の粒度\)** および **[評価の頻度]** を調整して、条件を詳細に設定します。 

11. **[Done]** をクリックします。

12. 複雑なアラート ルールを監視する場合は、必要に応じて、別の条件を追加します。 現時点では、ユーザーは単一の条件としての動的しきい値条件を使用してアラート ルールを設定できます。

13. **[アラート ルール名]** 、 **[説明]** 、および **[重大度]** などの **[アラートの詳細]** を指定します。

14. 既存のアクション グループを選択するか、新しいアクション グループを作成して、アラートにアクション グループを追加します。

15. **[完了]** をクリックして、メトリック アラート ルールを保存します。

> [!NOTE]
> ポータルで作成したメトリック アラート ルールは、ターゲット リソースと同じリソース グループに作成されます。

## <a name="view-and-manage-with-azure-portal"></a>Azure portal での表示と管理

[アラート] の [ルールの管理] ブレードを使用して、メトリック アラート ルールを表示および管理できます。 次の手順では、メトリック アラート ルールを表示し、それらの 1 つを編集する方法を示します。

1. Azure portal で **[モニター]** に移動します。

2. **[アラート]** をクリックし、 **[ルールの管理]** をクリックします

3. **[ルールの管理]** ブレードでは、複数のサブスクリプションのすべてのアラート ルールを見ることができます。 **[リソース グループ]** 、 **[リソースの種類]** 、および **[リソース]** を使用して、ルールをさらにフィルター処理できます。 メトリック アラートのみを表示する場合は、メトリックとして **[シグナルの種類]** を選択します。

    > [!TIP]
    > **[ルールの管理]** ブレードでは、複数のアラート ルールを選択して、有効/無効にできます。 これは、特定のターゲット リソースをメンテナンスする必要がある場合に便利です

4. 編集するメトリック アラート ルールの名前をクリックします

5. [ルールの編集] で、編集する **[アラートの条件]** をクリックします。 メトリック、しきい値条件、他のフィールドを必要に応じて変更できます

    > [!NOTE]
    > メトリック アラートを作成した後では、 **[ターゲット リソース]** と **[アラート ルール名]** を編集することはできません。

6. **[完了]** をクリックして編集内容を保存します。


## <a name="with-azure-cli"></a>Azure CLI の場合

前のセクションでは、Azure portal を使用してメトリック アラート ルールを作成、表示、および管理する方法について説明しました。 このセクションでは、クロスプラットフォームの [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して同じ操作を行う方法について説明します。 Azure CLI の使用を開始する最も簡単な方法は、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用することです。 この記事では、Cloud Shell を使用します。

1. Azure portal に移動して、 **[Cloud Shell]** をクリックします。

2. プロンプトでは、``--help`` オプションを指定してコマンドを実行することで、コマンドとその使用方法の詳細を確認できます。 たとえば、次のコマンドでは、メトリック アラートの作成、表示、管理に使用できるコマンドの一覧が表示されます

    ```azurecli
    az monitor metrics alert --help
    ```

3. VM の平均 CPU 使用率が 90% を超えているかどうかを監視する、簡単なメトリック アラート ルールを作成できます

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. 次のコマンドを使用して、リソース グループ内のすべてのメトリック アラートを表示できます

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. ルールの名前またはリソース ID を使用して、特定のメトリック アラート ルールの詳細を確認できます。

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. 次のコマンドを使用して、メトリック アラート ルールを無効にできます。

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. 次のコマンドを使用して、メトリック アラート ルールを削除できます。

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>PowerShell の場合

メトリック アラート ルールでは、専用の PowerShell コマンドレットを使用できます。

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2):新しいメトリック アラート ルールを作成するか、既存のものを更新します。
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2):1 つまたは複数のメトリック アラート ルールを取得します。
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2):メトリック アラート ルールを削除します。

## <a name="with-rest-api"></a>REST API を使用する

- [作成または更新](/rest/api/monitor/metricalerts/createorupdate):新しいメトリック アラート ルールを作成するか、既存のものを更新します。
- [取得](/rest/api/monitor/metricalerts/get):特定のメトリック アラート ルールを取得します。
- [リソース グループ別の一覧表示](/rest/api/monitor/metricalerts/listbyresourcegroup):特定のリソース グループ内のメトリック アラート ルールの一覧を取得します。
- [サブスクリプション別の一覧表示](/rest/api/monitor/metricalerts/listbysubscription):特定のサブスクリプション内のメトリック アラート ルールの一覧を取得します。
- [更新](/rest/api/monitor/metricalerts/update):メトリック アラート ルールを更新します。
- [[削除]](/rest/api/monitor/metricalerts/delete):メトリック アラート ルールを削除します。

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager テンプレートを使用してメトリック アラートを作成します](./alerts-metric-create-templates.md)
- [メトリック アラートのしくみを理解します](./alerts-metric-overview.md)
- [動的しきい値条件のメトリックのアラートのしくみを理解します](../alerts/alerts-dynamic-thresholds.md)
- [メトリック アラートの Web hook スキーマを理解します](./alerts-metric-near-real-time.md#payload-schema)
- [メトリック アラートに関する問題をトラブルシューティングします](./alerts-troubleshoot-metric.md)