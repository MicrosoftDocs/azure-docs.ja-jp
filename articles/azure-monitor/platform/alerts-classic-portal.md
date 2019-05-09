---
title: Azure Monitor を使用してクラシック メトリック アラートを作成、表示、管理する
description: Azure portal、CLI、または PowerShell を使用して、クラシック メトリック アラート ルールを作成、表示、管理する方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785327"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Azure Monitor を使用してクラシック メトリック アラートを作成、表示、および管理する

Azure Monitor のクラシック メトリック アラートには、メトリックのいずれかがしきい値を超えた場合に通知を受け取る方法が用意されています。 クラシック メトリック アラートは、ディメンションを持たないメトリックについてのみアラートを生成できるようにする古い機能です。 クラシック メトリック アラートよりも機能が向上したメトリック アラートと呼ばれる新しい機能が既にあります。 新しいメトリック アラート機能の詳細については、[メトリック アラートの概要](../../azure-monitor/platform/alerts-metric-overview.md)に関するページをご覧ください。 この記事では、Azure portal、Azure CLI、および PowerShell を使用して、クラシック メトリック アラート ルールを作成、表示、管理する方法について説明します。

## <a name="with-azure-portal"></a>Azure Portal の場合

1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。

2. **[監視]** セクションで、**[アラート (クラシック)]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。 **[アラート (クラシック)]** が見つからない場合は、**[アラート]** または **[アラート ルール]** 内で見つかることがあります。

    ![監視](media/alerts-classic-portal/AlertRulesButton.png)

3. **[メトリック アラートの追加 (クラシック)]** コマンドを選択して、フィールドに入力します。

    ![[アラートの追加]](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **[名前]** にアラート ルール名を入力します。 次に **[説明]** を選択します。この説明は通知電子メールにも表示されます。

5. 監視する **[メトリック]** を選択します。 次に、メトリックの **[条件]** と **[しきい値]** を選択します。 また、 **[期間]** では、どのくらいの期間メトリック ルールが満たされた後、アラートがトリガーされるかを選択します。 たとえば、期間として "直近 5 分" を使用すると、アラートは 80% を超える CPU を見つけて、その CPU が 5 分間継続して 80% を超えた時点でトリガーされます。 最初のトリガーが発生したら、次のアラートは、CPU が 5 分間継続して 80% を下回ったときにトリガーされます。 CPU のメトリック測定は、1 分ごとに発生します。

6. アラートが発生したときに管理者と共同管理者に電子メール通知が送信されるようにするには、**[メールの所有者]** を選択します。

7. アラートが発生したときに、他の電子メール アドレスにも通知を送信する場合は、**[追加する管理者の電子メール]** フィールドにそのアドレスを入力します。 複数のメール アドレスは、*email\@contoso.com;email2\@contoso.com* のようにセミコロンで区切ります。

8. **[webhook]** フィールドに、アラートが発生したときに呼び出す webhook の有効な URI を入力します。

9. Azure Automation を使用する場合は、アラートが発生したときに実行する Runbook を選択できます。

10. **[OK]** を選択してアラートを作成します。

数分後にアラートがアクティブになり、前述のようにトリガーされます。

アラートを作成したら、それを選択して次のタスクのいずれかを実行できます。

* メトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* 編集または削除する。
* そのアラートの受信を一時的に停止または再開する必要がある場合に、そのアラートを**無効**または**有効**にする。

## <a name="with-azure-cli"></a>Azure CLI の場合

前のセクションでは、Azure portal を使用してメトリック アラート ルールを作成、表示、および 管理する方法について説明しました。 このセクションでは、クロスプラットフォームの [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) を使用して同じ操作を行う方法について説明します。 Azure CLI の使用を開始する最も簡単な方法は、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を使用することです。

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>リソース グループ内のすべてのクラシック メトリック アラート ルールを取得する

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>特定のクラシック メトリック アラート ルールの詳細を表示する

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>クラシック メトリック アラート ルールを作成する

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>クラシック メトリック アラート ルールを削除する

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>PowerShell の場合

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

このセクションでは、PowerShell コマンドを使用してクラシック メトリック アラートを作成、表示、管理する方法を示します。この記事の例では、クラシック メトリック アラート用の Azure Monitor コマンドレットを使用する方法を示しています。

1. コンピューターで実行するために PowerShell をセットアップします (まだセットアップしていない場合)。 詳細については、[PowerShell をインストールして構成する方法](/powershell/azure/overview)に関するページを参照してください。 「[Azure Monitor (Insights) Cmdlets (Azure Monitor (Insights) コマンドレット)](https://docs.microsoft.com/powershell/module/az.applicationinsights)」で、Azure Monitor PowerShell コマンドレットのすべてのリストを確認することもできます。

2. まず、Azure サブスクリプションにログインします。

    ```powershell
    Connect-AzAccount
    ```

3. サインイン画面が表示されます。 アカウントにサインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。 すべての Azure コマンドレットは、既定のサブスクリプションのコンテキストで動作します。 アクセスできるサブスクリプションのリストを表示するには、次のコマンドを使用します。

    ```powershell
    Get-AzSubscription
    ```

4. 作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. リソース グループのすべてのクラシック メトリック アラート ルールを取得できます。

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. クラシック メトリック アラート ルールの詳細を表示できます

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. ターゲット リソースに設定されたすべてのアラート ルールを取得できます。 たとえば、VM に設定されたすべてのアラート ルールを取得します。

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. クラシック アラート ルールは、PowerShell で作成できなくなっています。 アラート ルールを作成するには、新しい "[Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule)" コマンドを使用する必要があります。

## <a name="next-steps"></a>次の手順

- [Resource Manager テンプレートを使用してクラシック メトリック アラートを作成する](../../azure-monitor/platform/alerts-enable-template.md)。
- [Webhook を使用してクラシック メトリック アラートが Azure 以外のシステムに通知するように設定する](../../azure-monitor/platform/alerts-webhooks.md)。
