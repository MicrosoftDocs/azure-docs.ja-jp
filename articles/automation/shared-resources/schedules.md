---
title: Azure Automation のスケジュール
description: Automation のスケジュールは、Azure Automation の Runbook を自動的に開始するためのスケジュール設定に使用します。 特定の時刻または定期的なスケジュールで自動的に Runbook を開始できるように スケジュールを作成および管理する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8daa87eca74570f5b1fdf1537b83dae60d292128
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849464"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Azure Automation の Runbook をスケジュール設定する

指定の時刻に開始するように Azure Automation の Runbook をスケジュール設定するには、Runbook を 1 つ以上のスケジュールにリンクします。 Azure Portal の Runbook では、1 回だけ実行するようにスケジュールを構成することも、時間または日単位で繰り返すスケジュールを指定することもできます。 さらに、週単位、月単位、特定の曜日や日にち、または月の特定の日のスケジュールも指定できます。 1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。

> [!NOTE]
> スケジュールは、現時点では Azure Automation DSC 構成をサポートしていません。

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット

Azure Automation から PowerShell を使用してスケジュールを作成したり管理したりするには、以下の表に示したコマンドレットを使用します。 これらは、 [Azure PowerShell モジュール](/powershell/azure/overview)の一部として出荷されます。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |スケジュールを取得します。 |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |新しいスケジュールを作成します。 |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |スケジュールを削除します。 |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |既存のスケジュールのプロパティを設定します。 |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |スケジュール済みの Runbook を取得します。 |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Runbook をスケジュールに関連付けます。 |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Runbook とスケジュールの関連付けを解除します。 |

## <a name="creating-a-schedule"></a>スケジュールを作成する

Runbook の新しいスケジュールは、Azure portal または PowerShell で作成できます。

> [!NOTE]
> Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。  Runbook およびそれらが自動化するプロセスに影響が及ばないようにするには、テスト専用の Automation アカウントを使用して、スケジュールがリンクされている Runbook を最初にテストする必要があります。  これにより、スケジュール済みの Runbook が引き続き正しく動作するかが検証されます。動作しない場合は、更新された Runbook バージョンを運用環境に移行する前に、トラブルシューティングを実行して必要な変更を適用できます。
> **[モジュール]** の [[Azure モジュールの更新]](../automation-update-azure-modules.md) オプションを選択してモジュールを手動で更新しない限り、Automation アカウントで新しいバージョンのモジュールが自動的に取得されることはありません。

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Azure ポータルで新しいスケジュールを作成するには

1. Azure Portal の Automation アカウントから、左側の **[共有リソース]** セクションで **[スケジュール]** を選択します。
2. ページ上部の **[スケジュールの追加]** をクリックします。
3. **[新しいスケジュール]** ウィンドウで、新しいスケジュールの**名前**と、必要に応じて**説明**を入力します。
4. スケジュールを 1 回だけ実行するか、繰り返し実行するかを、 **[1 回]** または **[Recurring]\(繰り返し\)** から選択します。 **[1 回]** を選択した場合は、 **[開始時刻]** を指定し、 **[作成]** をクリックします。 **[繰り返し]** を選択した場合は、 **[開始時刻]** を指定し、 **[間隔]** で、Runbook を繰り返す頻度を **[時間]** 、 **[日]** 、 **[週]** 、または **[月]** から選択します。
    1. **[週]** を選択すると、選択可能な曜日の一覧が表示されます。 必要な曜日をすべて選択してください。 ご自身のスケジュールが最初に実行されるのは、開始時刻後に選択されている最初の曜日です。 たとえば、週末のスケジュールを選択するには、 **[土曜日]** と **[日曜日]** を選択します。

       ![週末の定期スケジュールの設定](../media/schedules/week-end-weekly-recurrence.png)

    2. **[月]** を選択すると、別のオプションが表示されます。 **[毎月の特定曜日]** オプションで、 **[月の日付]** と **[平日]** のいずれかを選択します。 **[月の日]** を選択すると、必要な日付を選択できるカレンダーが表示されます。 現在の月にはない日付 (31 日など) を選択すると、スケジュールは実行されません。 月の最後の日にスケジュールを実行する場合は、 **[Run on last day of month]\(月の最終日に実行\)** で **[はい]** を選択します。 **[Week days]\(曜日\)** を選択すると、 **[間隔]** オプションが表示されます。 **[第 1]** 、 **[第 2]** 、 **[第 3]** 、 **[第 4]** 、または **[Last]\(最終\)** を選択します。 最後に、スケジュールを繰り返す曜日を選択します。

       ![初日、15 日、末日の月単位のスケジュール](../media/schedules/monthly-first-fifteenth-last.png)

5. 完了したら、 **[作成]** をクリックします。

### <a name="to-create-a-new-schedule-with-powershell"></a>PowerShell で新しいスケジュールを作成するには

スケジュールを作成するには、[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) コマンドレットを使います。 スケジュールの開始時刻と実行の頻度を指定します。 次の例では、多くのさまざまなスケジュール シナリオを作成する方法を示します。

#### <a name="create-a-one-time-schedule"></a>1 回だけのスケジュールを作成する

次のサンプル コマンドでは 1 回だけのスケジュールが作成されます。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>定期的なスケジュールを作成する

次のサンプル コマンドでは、1 年間毎日午後 1 時に実行する定期的なスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>週単位の定期的なスケジュールを作成する

次のサンプル コマンドでは、平日のみ実行する週単位のスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>週単位 (週末) の定期的なスケジュールを作成する

次のサンプル コマンドでは、週末のみ実行する週単位のスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>月単位 (初日、15 日、末日) の定期的なスケジュールを作成する

次のサンプル コマンドでは、毎月 1 日、15 日、および末日に実行する定期的なスケジュールを作成する方法を示します。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>スケジュールを Runbook にリンクする

1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。 Runbook にパラメーターがある場合は、その値を指定できます。 必須のパラメーターについては必ず値を指定してください。オプションのパラメーターについては値の指定は任意です。 このスケジュールで Runbook が開始されるたびに、これらの値が使用されます。 同じ Runbook を別のスケジュールに関連付けて、異なるパラメーター値を指定することができます。

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure ポータルで Runbook にスケジュールをリンクするには

1. Azure Portal の Automation アカウントから、左側の **[プロセス オートメーション]** セクションで **[Runbook]** を選択します。
2. Runbook の名前をクリックして、スケジュールを設定します。
3. 現在、Runbook がスケジュールにリンクされていない場合は、新しいスケジュールを作成するオプション、または既存のスケジュールにリンクするオプションが表示されます。
4. Runbook にパラメーターがある場合は、 **[実行設定を変更する (既定: Azure)]** オプションを選択すると **[パラメーター]** ウィンドウが表示されるので、情報を入力することができます。

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>PowerShell で Runbook にスケジュールをリンクするには

[Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) コマンドレットを使うと、スケジュールをリンクできます。 Parameters パラメーターを使用して、Runbook のパラメーターに値を指定できます。 パラメーター値を指定する方法について詳しくは、「[Azure Automation での Runbook の開始](../automation-starting-a-runbook.md)」をご覧ください。
次のサンプル コマンドは、パラメーターを使用して Azure Resource Manager コマンドレットでスケジュールを Runbook にリンクする方法を示しています。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>さらに頻繁に Runbook をスケジュールする

Azure Automation のスケジュールを構成できる最も短い間隔は 1 時間です。 それより頻繁にスケジュールを実行する必要がある場合は、2 つのオプションがあります。

* Runbook 用の [webhook](../automation-webhooks.md) を作成し、[Azure Scheduler](../../scheduler/scheduler-get-started-portal.md) を使って webhook を呼び出します。 Azure Scheduler を使うと、さらに小さい単位でスケジュールを定義できます。

* 1 時間に 1 回実行する 4 つのスケジュールを、相互の間隔を 15 分以下にして作成します。 このシナリオでは、Runbook は異なるスケジュールにより 15 分ごとに実行できます。

## <a name="disabling-a-schedule"></a>スケジュールを無効にする

スケジュールを無効にすると、そのスケジュールにリンクされた Runbook はいずれもそのスケジュールに基づいて実行できなくなります。 スケジュールは手動で無効にしたり、頻度が指定されているスケジュールの有効期限を、作成時に設定したりすることができます。 有効期限の時刻になると、スケジュールは無効になります。

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure ポータルからスケジュールを無効にするには

1. Azure Portal の Automation アカウントから、左側の **[共有リソース]** セクションで **[スケジュール]** を選択します。
2. スケジュールの名前をクリックして、その詳細ウィンドウを開きます。
3. **有効** を **いいえ** に変更します

> [!NOTE]
> 開始時刻が過去であるスケジュールを無効にする場合は、保存する前に、開始日を将来の時刻に変更する必要があります。

### <a name="to-disable-a-schedule-with-powershell"></a>PowerShell でスケジュールを無効にするには

[Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) コマンドレットを使って、既存のスケジュールのプロパティを変更できます。 To disable the schedule, specify **false** for the **IsEnabled** parameter.

次のサンプル コマンドは、Azure Resource Manager コマンドレットで Runbook のスケジュールを無効にする方法を示しています。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>次の手順

* Azure Automation の Runbook の使用を開始するには、「 [Azure Automation での Runbook の開始](../automation-starting-a-runbook.md)

