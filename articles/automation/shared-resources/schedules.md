---
title: Azure Automation のスケジュールを管理する
description: この記事では、Azure Automation でスケジュールを作成して操作する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8bd988029b8d78a29de38e995c36ee1860d8cda9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187355"
---
# <a name="manage-schedules-in-azure-automation"></a>Azure Automation のスケジュールを管理する

指定の時刻に開始するように Azure Automation の Runbook をスケジュール設定するには、Runbook を 1 つ以上のスケジュールにリンクします。 Azure Portal の Runbook では、1 回だけ実行するようにスケジュールを構成することも、時間または日単位で繰り返すスケジュールを指定することもできます。 さらに、週単位、月単位、特定の曜日や日にち、または月の特定の日のスケジュールも指定できます。 1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。

> [!NOTE]
> Azure Automation は、夏時間をサポートしており、オートメーション操作でこれを適切にスケジュールします。

> [!NOTE]
> 現在、Azure Automation DSC 構成ではスケジュールを使用できません。

## <a name="powershell-cmdlets-used-to-access-schedules"></a>スケジュールへのアクセスに使用する PowerShell コマンドレット

PowerShell を使用して Automation スケジュールを作成して管理するためのコマンドレットを次の表に示します。 これらは、[Az モジュール](modules.md#az-modules)の一部として出荷されています。 

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |スケジュールを取得します。 |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |スケジュール済みの Runbook を取得します。 |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |新しいスケジュールを作成します。 |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook をスケジュールに関連付けます。 |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |スケジュールを削除します。 |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |既存のスケジュールのプロパティを設定します。 |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook とスケジュールの関連付けを解除します。 |

## <a name="create-a-schedule"></a>スケジュールを作成する

Runbook の新しいスケジュールは、Azure portal または PowerShell を使用して作成できます。 Runbook およびそれらが自動化するプロセスに影響が及ばないようにするには、テスト専用の Automation アカウントを使用して、スケジュールがリンクされている Runbook を最初にテストする必要があります。 テストでは、スケジュールされた Runbook が引き続き正常に動作することを検証します。 問題が発生した場合は、更新された Runbook バージョンを運用環境に移行する前に、トラブルシューティングを行い、必要な変更を適用することができます。

> [!NOTE]
> **[モジュール]** の [[Azure モジュールの更新]](../automation-update-azure-modules.md) オプションを選択してモジュールを手動で更新しない限り、Automation アカウントで新しいバージョンのモジュールが自動的に取得されることはありません。 Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Azure portal で新しいスケジュールを作成する

1. Azure Portal の Automation アカウントから、左側の **[共有リソース]** セクションで **[スケジュール]** を選択します。
1. ページ上部の **[スケジュールの追加]** を選択します。
1. **[新しいスケジュール]** ウィンドウで、新しいスケジュールの名前と、必要に応じて説明を入力します。
1. スケジュールを 1 回だけ実行するか、繰り返し実行するかを、 **[Once]\(1 回\)** または **[Recurring]\(繰り返し\)** から選択します。 **[Once]\(1 回\)** を選択した場合は、開始時刻を指定してから、 **[作成]** を選択します。 **[Recurring]\(繰り返し\)** を選択した場合は、開始時刻を指定します。 **[Recur every]\(繰り返し間隔\)** では、Runbook を繰り返す頻度を選択します。 これは、時間、日、週、月から選択できます。
    1. **[週]** を選択すると、選択対象の曜日が表示されます。 必要な曜日をすべて選択してください。 ご自身のスケジュールが最初に実行されるのは、開始時刻後に選択されている最初の曜日です。 たとえば、週末のスケジュールを選択するには、土曜日と日曜日を選択します。
    
       ![週末の定期スケジュールの設定](../media/schedules/week-end-weekly-recurrence.png)

    2. **[月]** を選択すると、別のオプションが表示されます。 **[毎月の特定曜日]** オプションで、 **[月の日付]** と **[平日]** のいずれかを選択します。 **[月の日付]** を選択すると、任意の日数を選択できるカレンダーが表示されます。 現在の月にはない日付 (31 日など) を選択すると、スケジュールは実行されません。 月の最後の日にスケジュールを実行する場合は、 **[Run on last day of month]\(月の最終日に実行\)** で **[はい]** を選択します。 **[Week days]\(曜日\)** を選択すると、 **[Recur every]\(繰り返し間隔\)** オプションが表示されます。 **[第 1]** 、 **[第 2]** 、 **[第 3]** 、 **[第 4]** 、または **[Last]\(最終\)** を選択します。 最後に、スケジュールを繰り返す曜日を選択します。

       ![初日、15 日、末日の月単位のスケジュール](../media/schedules/monthly-first-fifteenth-last.png)

1. 完了したら、 **[作成]** をクリックします。

### <a name="create-a-new-schedule-with-powershell"></a>PowerShell で新しいスケジュールを作成する

スケジュールを作成するには、[New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) コマンドレットを使用します。 スケジュールの開始時刻と実行の頻度を指定します。 次の例では、多くのさまざまなスケジュール シナリオを作成する方法を示します。

#### <a name="create-a-one-time-schedule"></a>1 回限りのスケジュールを作成する

次の例は、1 回限りのスケジュールを作成します。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>定期的なスケジュールを作成する

次の例では、1 年間、毎日午後 1 時に実行される定期的なスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>週単位の定期的なスケジュールを作成する

次の例では、平日のみ実行される週単位のスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>週単位 (週末) の定期的なスケジュールを作成する

次の例では、週末のみ実行される週単位のスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>月単位 (初日、15 日、末日) の定期的なスケジュールを作成する

次の例では、毎月 1 日、15 日、および末日に実行される定期的なスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>スケジュールを Runbook にリンクする

1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。 Runbook にパラメーターがある場合は、その値を指定できます。 必須のパラメーターについては必ず値を指定してください。また、省略可能なパラメーターについては値の指定は任意です。 このスケジュールで Runbook が開始されるたびに、これらの値が使用されます。 同じ Runbook を別のスケジュールに関連付けて、異なるパラメーター値を指定することができます。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure portal を使用して Runbook にスケジュールをリンクする

1. Azure portal の Automation アカウントから、 **[プロセス オートメーション]** の下で **[Runbook]** を選択します。
1. Runbook の名前を選択して、スケジュールを設定します。
1. 現在、Runbook がスケジュールにリンクされていない場合は、新しいスケジュールを作成するオプション、または既存のスケジュールにリンクするオプションが表示されます。
1. Runbook にパラメーターがある場合は、 **[Modify run settings (Default:Azure)]\(実行設定を変更する (既定: Azure\))** オプションを選択すると **[パラメーター]** ウィンドウが表示されます。 パラメーター情報はここに入力できます。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>PowerShell を使用してスケジュールを Runbook にリンクする

[Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) コマンドレットを使用して、スケジュールをリンクします。 Parameters パラメーターを使用して、Runbook のパラメーターに値を指定できます。 パラメーター値を指定する方法について詳しくは、「[Azure Automation で Runbook を開始する](../start-runbooks.md)」を参照してください。
次の例では、パラメーターを使用して Azure Resource Manager コマンドレットでスケジュールを Runbook にリンクする方法を示します。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Runbook をより頻繁に実行するためのスケジュール設定

Azure Automation のスケジュールを構成できる最も短い間隔は 1 時間です。 それより頻繁にスケジュールを実行する必要がある場合は、2 つのオプションがあります。

* Runbook 用の [Webhook](../automation-webhooks.md) を作成し、[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) を使って Webhook を呼び出します。 Azure Logic Apps を使うと、さらに小さい単位でスケジュールを定義できます。

* 1 時間に 1 回実行する 4 つのスケジュールを、それぞれ 15 分以下の間隔で作成します。 このシナリオでは、Runbook は異なるスケジュールにより 15 分ごとに実行できます。

## <a name="disable-a-schedule"></a>スケジュールを無効にする

スケジュールを無効にすると、そのスケジュールにリンクされた Runbook はいずれもそのスケジュールに基づいて実行できなくなります。 スケジュールは手動で無効にしたり、頻度が指定されているスケジュールの有効期限を、作成時に設定したりすることができます。 有効期限の時刻になると、スケジュールは無効になります。

### <a name="disable-a-schedule-from-the-azure-portal"></a>Azure portal からスケジュールを無効にする

1. Automation アカウントで、 **[共有リソース]** の下にある **[スケジュール]** を選択します。
1. スケジュールの名前を選択して、その詳細ウィンドウを開きます。
1. **有効** を **いいえ** に変更します

> [!NOTE]
> 開始時刻が過去であるスケジュールを無効にする場合は、保存する前に、開始日を将来の時刻に変更する必要があります。

### <a name="disable-a-schedule-with-powershell"></a>PowerShell を使用してスケジュールを無効にする

[Set-AzureRmAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) コマンドレットを使用して、既存のスケジュールのプロパティを変更します。 スケジュールを無効にするには、`IsEnabled` パラメーターに False を指定します。

次の例は、Azure Resource Manager コマンドレットを使用して Runbook のスケジュールを無効にする方法を示しています。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>スケジュールを削除する

スケジュールを削除する準備ができたら、Azure portal または PowerShell のいずれかを使用できます。 削除できるのは、前のセクションで説明したように無効になっているスケジュールのみであることに注意してください。

### <a name="remove-a-schedule-using-the-azure-portal"></a>Azure portal を使用してスケジュールを削除する

1. Automation アカウントで、 **[共有リソース]** の下にある **[スケジュール]** を選択します。
2. スケジュールの名前をクリックして、その詳細ウィンドウを開きます。
3. **[削除]** をクリックします。

### <a name="remove-a-schedule-with-powershell"></a>PowerShell を使用してスケジュールを削除する

次に示すように `Remove-AzAutomationSchedule` コマンドレットを使用して、既存のスケジュールを削除できます。 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>次のステップ

* スケジュールにアクセスするためのコマンドレットの詳細については、「[Azure Automation でモジュールを管理する](modules.md)」を参照してください。
* Runbook の一般的な情報については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」を参照してください。
