---
title: Azure Automation の Start/Stop VMs during off-hours を構成する
description: この記事では、さまざまなユース ケースまたはシナリオをサポートするように Start/Stop VMs during off-hours 機能を構成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3fbd6292f654071f74b4dfccc5e4de393ccfff02
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266717"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours を構成する

この記事では、記載されているシナリオをサポートするように [Start/Stop VMs during off-hours](automation-solution-vm-management.md) 機能を構成する方法について説明します。 その他の学習内容は次のとおりです。

* [電子メール通知を構成する](#configure-email-notifications)
* [VM を追加する](#add-a-vm)
* [VM を除外する](#exclude-a-vm)
* [起動および停止スケジュールを変更する](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>シナリオ 1:スケジュールに従って VM を開始/停止する

このシナリオは、初めて Start/Stop VMs during off-hours をデプロイするときの既定の構成です。 たとえば、夕方退社するときにサブスクリプション全体のすべての VM を停止し、朝の出社時に VM を起動するようにこの機能を構成できます。 デプロイ中にスケジュール **Scheduled-StartVM** と **Scheduled-StopVM** を構成すると、対象の VM が起動および停止します。 

VM の停止のみを行うようにこの機能を構成できます。 カスタム スケジュールを構成する方法については、「[起動および停止スケジュールを変更する](#modify-the-startup-and-shutdown-schedules)」を参照してください。

> [!NOTE]
> スケジュール タイム パラメーターを構成するとき、現在ご利用のタイム ゾーンが、この機能で使用されるタイム ゾーンになります。 ただし、Azure Automation では、その格納に UTC 形式が使用されます。 タイム ゾーンの変換はマシンのデプロイ中に処理されるので、自分で行う必要はありません。

対象とする VM を制御するには、`External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames`、`External_ExcludeVMNames` の各変数を構成します。

サブスクリプションおよびリソース グループに対するアクション、または特定の VM リストのいずれかを対象にできますが、両方を有効にすることはできません。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. ターゲット VM を指定するために、`External_Stop_ResourceGroupNames` と `External_ExcludeVMNames` 変数を構成します。

2. **Scheduled-StartVM** スケジュールと **Scheduled-StopVM** スケジュールを有効にして、更新します。

3. **ACTION** パラメーター フィールドを **start** に設定し、**WHATIF** パラメーター フィールドを True に設定して、**ScheduledStartStop_Parent** Runbook を実行し、ご自身の変更内容をプレビューします。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. **ACTION** を **start** に設定し、**ScheduledStartStop_Parent** Runbook を実行します。

2. VM のコンマ区切りリスト(スペースなし) を **VMList** パラメーター フィールドに追加します。 リストの例は `vm1,vm2,vm3`です。

3. **WHATIF** パラメーター フィールドを True に設定します。

4. コンマ区切り値の間にスペースを入れずに、VM のコンマ区切りリスト (VM1,VM2,VM3) で `External_ExcludeVMNames` 変数を構成します。

5. このシナリオでは、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupnames` の変数は考慮されません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、[Azure Automation の Runbook をスケジュールする](shared-resources/schedules.md)方法に関するページを参照してください。

    > [!NOTE]
    > **[ターゲット ResourceGroup 名]** の値は、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupNames` の両方の値として格納されます。 粒度を高めるためには、これらの変数をそれぞれ変更して、異なるリソース グループを対象にします。 開始アクションでは `External_Start_ResourceGroupNames` を使用し、停止アクションでは `External_Stop_ResourceGroupNames` を使用します。 VM が起動スケジュールと停止スケジュールに自動的に追加されます。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>シナリオ 2:タグを使用して VM を順番に起動/停止する

分散ワークロードをサポートする複数の VM のコンポーネントを 2 つ以上含む環境では、コンポーネントの起動および停止順序を指定できることが重要です。 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. `External_Start_ResourceGroupNames` および `External_Stop_ResourceGroupNames` 変数で対象となる VM に、正の整数値を持つ `sequencestart` と `sequencestop` タグを追加します。 起動アクションと停止アクションは昇順で実行されます。 VM にタグを付ける方法については、[Azure で Windows 仮想マシンにタグを付ける方法](../virtual-machines/windows/tag.md)と [Azure で Linux 仮想マシンにタグを付ける方法](../virtual-machines/linux/tag.md)に関するページを参照してください。

2. スケジュール **Sequenced-StartVM** と **Sequenced-StopVM** を、要件を満たす日付と時刻に変更し、スケジュールを有効にします。

3. **ACTION** を **start** に設定し、**WHATIF** を True に設定して、**SequencedStartStop_Parent** Runbook を実行し、ご自身の変更をプレビューします。

4. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** にセットして手動で Runbook を実行するか、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** が、指定されたスケジュールに従って自動的に実行されるようにします。

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>起動および停止アクションの対象を VM リストで指定する

1. `VMList` パラメーターに追加する予定の VM に、正の整数値を持つ `sequencestart` および `sequencestop` タグを追加します。

2. **ACTION** を **start** に設定し、**SequencedStartStop_Parent** Runbook を実行します。

3. VM のコンマ区切りリスト(スペースなし) を **VMList** パラメーター フィールドに追加します。 リストの例は `vm1,vm2,vm3`です。

4. **WHATIF** を True に設定します。 

5. コンマ区切り値の間にスペースを入れずに、VM のコンマ区切りリストで `External_ExcludeVMNames` 変数を構成します。

6. このシナリオでは、`External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupnames` の変数は考慮されません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、[Azure Automation の Runbook をスケジュールする](shared-resources/schedules.md)方法に関するページを参照してください。

7. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** に設定して、**monitoring-and-diagnostics/monitoring-action-groupsrunbook** を手動で実行します。 または、指定したスケジュールに従って、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** が自動的に実行されるようにします。

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>シナリオ 3:CPU 使用率に基づいて自動的に開始または停止する

Start/Stop VMs during off-hours は、営業時間外などの非ピーク期間中に使用されていないマシンを評価し、プロセッサ使用率が指定されたパーセンテージ未満の場合は自動的にシャットダウンすることで、サブスクリプションで実行中の Azure Resource Manager およびクラシック VM のコストを管理する上で役立ちます。

既定では、CPU 割合メトリックを評価して、平均使用率が 5% 以下であるかどうかを確認するように機能が事前構成されています。 このシナリオは次の変数によって制御され、既定値が要件を満たしていない場合は変更できます。

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

サブスクリプションおよびリソース グループに対するアクションを有効にして対象にするか、特定の VM リストを対象にすることができます。

**AutoStop_CreateAlert_Parent** Runbook を実行すると、対象となるサブスクリプション、リソース グループ、および VM が存在することが確認されます。 VM が存在する場合、Runbook では、親 Runbook によって確認された VM ごとに **AutoStop_CreateAlert_Child** Runbook を呼び出します。 この子 Runbook は次のことを実行します。

* 確認された VM ごとにメトリック アラート ルールを作成します。
* 指定された時間間隔で CPU が構成済みのしきい値を下回った場合は、特定の VM で **AutoStop_VM_Child** Runbook をトリガーします。 
* VM の停止を試みます。

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>サブスクリプション内のすべての VM を自動停止アクションの対象にする

1. 確実に、`External_Stop_ResourceGroupNames` 変数が空であるか、* (ワイルドカード) に設定されるようにします。

2. (省略可) VM の一部を自動停止アクションから除外する場合は、VM 名のコンマ区切りリストを `External_ExcludeVMNames` 変数に追加できます。

3. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして、サブスクリプション内のすべての VM で必要な VM の停止メトリック アラート ルールを作成します。 この種類のスケジュールを実行すると、新しい VM がサブスクリプションに追加されたときに新しいメトリック アラート ルールを作成できます。

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>リソース グループまたは複数のリソース グループ内のすべての VM を自動停止アクションの対象にする

1. リソース グループ名のコンマ区切りリストを、`External_Stop_ResourceGroupNames` 変数に追加します。

2. 一部の VM を自動停止から除外する場合は、VM 名のコンマ区切りリストを `External_ExcludeVMNames` 変数に追加することができます。

3. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして実行し、リソース グループのすべての VM で必要な VM 停止メトリック アラート ルールを作成します。 この操作をスケジュールに従って実行すると、新しい VM がリソース グループに追加されたときに新しいメトリック アラート ルールを作成できます。

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>VM のリストを自動停止アクションの対象にする

1. 新しい[スケジュール](shared-resources/schedules.md#create-a-schedule)を作成し、**AutoStop_CreateAlert_Parent** Runbook にリンクして、VM 名のコンマ区切りリストを `VMList` パラメーターに追加します。

2. (省略可) VM の一部を自動停止アクションから除外する場合、VM 名のコンマ区切りリスト (スペースなし) を `External_ExcludeVMNames` 変数に追加できます。

## <a name="configure-email-notifications"></a>電子メール通知の構成

Start/Stop VMs during off-hours のデプロイ後に電子メール通知を変更するには、デプロイ時に作成されたアクション グループを変更します。  

> [!NOTE]
> Azure Government Cloud のサブスクリプションでは、この機能の電子メール機能はサポートされません。

1. Azure portal で、 **[監視]** 、 **[アクション グループ]** の順に移動します。 **StartStop_VM_Notication** というアクション グループを選択します。

    ![Automation Update Management ページ](media/automation-solution-vm-management/azure-monitor.png)

2. [StartStop_VM_Notification] ページで、 **[詳細]** の **[詳細の編集]** をクリックします。 これで、[電子メール/SMS/プッシュ/音声] ページが開きます。 メール アドレスを更新し、 **[OK]** をクリックして変更を保存します。

    ![Automation Update Management ページ](media/automation-solution-vm-management/change-email.png)

    アクション グループにさらにアクションを追加することもできます。アクション グループの詳細については、「[アクション グループ](../azure-monitor/platform/action-groups.md)」を参照してください。

この機能によって仮想マシンがシャットダウンされたときに送信されるメールの例を以下に示します。

![Automation Update Management ページ](media/automation-solution-vm-management/email.png)

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>VM を追加または除外する

この機能を使用すると、VM を対象に追加したり、対象から除外したりすることができます。 

### <a name="add-a-vm"></a>VM を追加する

この機能の実行対象に VM を含めるには、次の 2 とおりの方法があります。

* この機能の親 [Runbook](automation-solution-vm-management.md#runbooks) のそれぞれに `VMList` パラメーターがあります。 状況に合わせて適切な親 Runbook をスケジュールするときに、このパラメーターに VM 名のコンマ区切りリスト (スペースなし) を渡すことができ、これらの VM は機能が実行されるときに含まれます。

* 複数の VM を選択するには、開始または停止する VM が含まれているリソース グループ名で `External_Start_ResourceGroupNames` と `External_Stop_ResourceGroupNames` を設定します。 サブスクリプションのすべてのリソース グループに対して機能を実行するように、変数を `*` の値に設定することもできます。

### <a name="exclude-a-vm"></a>VM を除外する

Stop/start VMs during off-hours から VM を除外するには、その名前を `External_ExcludeVMNames` 変数に追加します。 この変数は、機能から除外する特定の VM のコンマ区切りリスト (スペースなし) です。 このリストは 140 個の VM までに制限されています。 このリストに 140 個を超える VM を追加すると、除外するように設定した VM が誤って開始または停止される可能性があります。

## <a name="modify-the-startup-and-shutdown-schedules"></a>起動および停止スケジュールを変更する

この機能の起動および停止スケジュールを管理するには、[Azure Automation の Runbook をスケジュールする](shared-resources/schedules.md)方法に関するページで説明されている手順に従います。 VM の開始と停止それぞれについて、個別のスケジュールが必要となります。

特定の時刻に VM の停止のみを行うように機能を構成できます。 このシナリオでは、停止スケジュールを作成するだけで、対応する開始スケジュールは作成しません。 

1. シャットダウンする VM のリソース グループが `External_Stop_ResourceGroupNames` 変数に追加されていることを確認します。

2. VM をシャットダウンする時刻のスケジュールを独自に作成します。

3. **ScheduledStartStop_Parent** Runbook に移動し、 **[スケジュール]** をクリックします。 これにより、前の手順で作成したスケジュールを選択できます。

4. **[パラメーターと実行設定]** を選択し、**ACTION** フィールドを **Stop** に設定します。

5. **[OK]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

* 動作中に機能を監視する方法については、「[Start/Stop VMs during off-hours からログを照会する](automation-solution-vm-management-logs.md)」を参照してください。
* VM の管理中に発生した問題への対応については、「[Start/Stop VMs during off-hours の問題のトラブルシューティング](troubleshoot/start-stop-vm.md)」を参照してください。
