---
title: Azure Monitor を使用してメトリック アラートを作成、表示、管理する
description: Azure portal または CLI を使用して、メトリック アラート ルールを作成、表示、管理する方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 117b65265c853194e93a97fe5e2b2dcc6e9f5bc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807390"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Azure Monitor を使用してメトリック アラートを作成、表示、管理する

Azure Monitor のメトリック アラートには、メトリックのいずれかがしきい値を超えた場合に通知を受け取る方法が用意されています。 メトリック アラートは、多次元プラットフォーム メトリック、カスタム メトリック、Application Insights の標準メトリックとカスタム メトリックで動作します。 この記事では、Azure portal および Azure CLI を使用して、メトリック アラート ルールを作成、表示、管理する方法について説明します。 Azure Resource Manager テンプレートを使用してメトリック アラート ルールを作成することもできます。これについては、[別の記事](alerts-metric-create-templates.md)で説明されています。

メトリック アラートの動作について詳しくは、[メトリック アラートの概要](alerts-metric-overview.md)に関するページをご覧ください。

## <a name="create-with-azure-portal"></a>Azure Portal での作成

次の手順では、Azure portal でメトリック アラート ルールを作成する方法について説明します。

1. [Azure portal](https://portal.azure.com) で、**[モニター]** をクリックします。 [モニター] ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

2. **[アラート]** をクリックして、**[+ 新しいアラート ルール]** をクリックします。

    > [!TIP]
    > ほとんどのリソース ブレードにも **[監視]** のリソース メニューに **[アラート]** があり、そこからもアラートを作成できます。

3. **[ターゲットの選択]** をクリックし、読み込まれるコンテキスト ウィンドウで、アラートを設定するターゲット リソースを選択します。 **サブスクリプション**と**リソースの種類**のドロップダウン リストを使用して、監視するリソースを検索します。 検索バーを使用して、リソースを検索することもできます。

4. 選択したリソースにアラートを作成できるメトリックがある場合は、右下の **[使用可能なシグナル]** にメトリックが表示されます。 メトリック アラートでサポートされているリソースの種類の完全な一覧については、[こちらの記事](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)をご覧ください。

5. ターゲット リソースを選択した後、**[条件の追加]** をクリックします。

6. リソースに対してサポートされているシグナルの一覧が表示されるので、アラートを作成するメトリックを選択します。

7. 必要に応じて、**[期間]** と **[集計]** を調整して、メトリックを設定し直します。 メトリックにディメンションがある場合は、**[ディメンション]** テーブルが表示されます。 ディメンションごとに 1 つまたは複数の値を選択します。 メトリック アラートは、選択された値のすべての組み合わせについて条件を評価します。 [多次元メトリックでのアラート生成のしくみの詳細を参照してください](alerts-metric-overview.md)。 **Select \*** で任意のディメンションを選択することもできます。 **Select \*** は、選択範囲をディメンションの現在と将来のすべての値に動的にスケーリングします。

8. 過去 6 時間のメトリックのグラフが表示されます。 アラート パラメーターの **[期間]**、**[頻度]**、**[演算子]**、**[しきい値]**、**[秘密度]** を定義すると、メトリック アラート ルールを評価するロジックが決まります。 [動的しきい値の条件の種類と秘密度のオプションの詳細については、こちらをご覧ください](alerts-dynamic-thresholds.md)。

9. 静的しきい値を使用している場合、メトリック グラフが妥当なしきい値の決定に役立つことがあります。 動的しきい値を使用している場合は、メトリック グラフに最新のデータに基づいて計算されたしきい値が表示されます。

10. **[完了]** をクリックします

11. 複雑なアラート ルールを監視する場合は、必要に応じて、別の条件を追加します。 現時点では、ユーザーは単一の条件としての動的しきい値条件を使用してアラート ルールを設定できます。

12. **[アラート ルール名]**、**[説明]**、**[重大度]** などの **[アラートの詳細]** を指定します

13. 既存のアクション グループを選択するか、新しいアクション グループを作成して、アラートにアクション グループを追加します。

14. **[完了]** をクリックして、メトリック アラート ルールを保存します。

> [!NOTE]
> ポータルで作成したメトリック アラート ルールは、ターゲット リソースと同じリソース グループに作成されます。

## <a name="view-and-manage-with-azure-portal"></a>Azure portal での表示と管理

[アラート] の [ルールの管理] ブレードを使用して、メトリック アラート ルールを表示および管理できます。 次の手順では、メトリック アラート ルールを表示し、それらの 1 つを編集する方法を示します。

1. Azure portal で **[モニター]** に移動します。

2. **[アラート]** をクリックし、**[ルールの管理]** をクリックします

3. **[ルールの管理]** ブレードでは、複数のサブスクリプションのすべてのアラート ルールを見ることができます。 **[リソース グループ]**、**[リソースの種類]**、**[リソース]** を使用して、ルールをさらにフィルター処理できます。 メトリック アラートのみを表示する場合は、メトリックとして **[シグナルの種類]** を選択します。

    > [!TIP]
    > **[ルールの管理]** ブレードでは、複数のアラート ルールを選択して、有効/無効にできます。 これは、特定のターゲット リソースをメンテナンスする必要がある場合に便利です

4. 編集するメトリック アラート ルールの名前をクリックします

5. [ルールの編集] で、編集する **[アラートの条件]** をクリックします。 メトリック、しきい値条件、他のフィールドを必要に応じて変更できます

    > [!NOTE]
    > メトリック アラートを作成した後では、**[ターゲット リソース]** と **[アラート ルール名]** を編集することはできません。

6. **[完了]** をクリックして編集内容を保存します。

## <a name="with-azure-cli"></a>Azure CLI の場合

前のセクションでは、Azure portal を使用してメトリック アラート ルールを作成、表示、および 管理する方法について説明しました。 このセクションでは、クロスプラットフォームの [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) を使用して同じ操作を行う方法について説明します。 Azure CLI の使用を開始する最も簡単な方法は、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を使用することです。 この記事では、Cloud Shell を使用します。

1. Azure portal に移動し、**[Cloud Shell]** をクリックします。

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

## <a name="next-steps"></a>次の手順

- [Azure Resource Manager テンプレートを使用してメトリック アラートを作成します](../../azure-monitor/platform/alerts-enable-template.md)。
- [メトリック アラートのしくみを理解します](alerts-metric-overview.md)。
- [動的しきい値条件のメトリックのアラートのしくみを理解します](alerts-dynamic-thresholds.md)。
- [メトリック アラートの Web hook スキーマを理解します](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

