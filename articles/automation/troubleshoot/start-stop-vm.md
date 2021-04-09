---
title: Azure Automation の Start/Stop VMs during off-hours の問題のトラブルシューティング
description: この記事では、Start/Stop VMs during off-hours 機能の使用中に発生する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953852"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Start/Stop VMs during off-hours の問題のトラブルシューティング

この記事では、お使いの VM に Azure Automation の Start/Stop VMs during off-hours 機能をデプロイするときに発生する問題のトラブルシューティングと解決方法に関する情報を提供します。 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>シナリオ:Start/Stop VMs during off-hours を正常にデプロイできない

### <a name="issue"></a>問題

[Start/Stop VMs during off-hours](../automation-solution-vm-management.md) をデプロイするときに、次のいずれかのエラーが発生します。

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>原因

デプロイは、次のいずれかの理由で失敗する場合があります。

- 選択したリージョンに同じ名前の Automation アカウントが既に存在している。
- Start/Stop VMs during off-hours のデプロイがポリシーで許可されていない。
- `Microsoft.OperationsManagement`、`Microsoft.Insights`、または `Microsoft.Automation` のリソースの種類が登録されていない。
- Log Analytics ワークスペースがロックされている。
- AzureRM モジュールまたは Start/Stop VMs during off-hours 機能のバージョンが古い。

### <a name="resolution"></a>解像度

考えられる解決策について、次の修正を確認してください。

* Automation アカウントは、異なるリソース グループ内にある場合でも、Azure リージョン内で一意である必要があります。 ターゲット リージョンにある既存の Automation アカウントを確認します。
* 既存のポリシーが、Start/Stop VMs during off-hours をデプロイするために必要なリソースの使用を妨げています。 Azure portal でポリシーの割り当てに移動し、そのリソースのデプロイを許可していないポリシーの割り当てがあるかどうかを確認します。 詳細については、[RequestDisallowedByPolicy エラー](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)を参照してください。
* Start/Stop VMs during off-hours をデプロイするには、次の Azure リソースの名前空間にサブスクリプションが登録されている必要があります。

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   プロバイダーの登録時のエラーの詳細については、「[リソース プロバイダーの登録エラーの解決](../../azure-resource-manager/templates/error-register-resource-provider.md)」を参照してください。
* Log Analytics ワークスペースがロックされている場合は、Azure portal でワークスペースに移動し、すべてのリソースのロックを削除します。
* 以上の解決方法で問題が解決しない場合は、「[ソリューションを更新する](../automation-solution-vm-management.md#update-the-feature)」の手順に従って、Start/Stop VMs during off-hours 機能を再デプロイします。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>シナリオ:すべての VM を起動または停止できない

### <a name="issue"></a>問題

Start/Stop VMs during off-hours を構成したが、すべての VM が起動も停止もされません。

### <a name="cause"></a>原因

このエラーは、次に示すいずれかの理由で発生する可能性があります。

- スケジュールが正しく構成されていない。
- 実行アカウントが正しく構成されていない可能性がある。
- Runbook でエラーが発生した可能性がある。
- VM が除外されている可能性がある。

### <a name="resolution"></a>解像度

考えられる解決策について、次の一覧を確認してください。

* Start/Stop VMs during off-hours のスケジュールが適切に構成されていることを確認します。 スケジュールを構成する方法については、「[スケジュール](../shared-resources/schedules.md)」を参照してください。

* [ジョブ ストリーム](../automation-runbook-execution.md#job-statuses)を確認してエラーを探します。 次のいずれかの Runbook からジョブを探します。

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 起動または停止しようとしている VM に対する適切なアクセス許可が[実行アカウント](../automation-security-overview.md#run-as-accounts)にあることを確認します。 リソースに対するアクセス許可を確認する方法については、「[クイック スタート:Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値は、Azure portal の Automation アカウントに移動して取得できます。 **[アカウント設定]** で **[実行アカウント]** を選択し、適切な実行アカウントを選択します。

* 明示的に除外されている場合は、VM を起動または停止できない可能性があります。 除外対象の VM は、その機能のデプロイ先の Automation アカウントの `External_ExcludeVMNames` 変数で設定されます。 次の例は、PowerShell を使用してその値のクエリを実行する方法を示しています。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>シナリオ:一部の VM を起動または停止できない

### <a name="issue"></a>問題

Start/Stop VMs during off-hours を構成したが、構成されている一部の VM が起動も停止もされません。

### <a name="cause"></a>原因

このエラーは、次に示すいずれかの理由で発生する可能性があります。

- シーケンス シナリオで、タグが見つからないか正しくない。
- VM が除外されている可能性がある。
- 実行アカウントが、VM に対する十分なアクセス許可を持っていない可能性がある。
- VM の起動または停止を妨げる問題が発生している可能性がある。

### <a name="resolution"></a>解像度

考えられる解決策について、次の一覧を確認してください。

* Start/Stop VMs during off-hours の[シーケンス シナリオ](../automation-solution-vm-management.md)を使用するときは、起動または停止する各 VM に適切なタグが指定されていることを確認する必要があります。 起動する VM には `sequencestart` タグ、停止する VM には `sequencestop` タグが指定されていることを確認してください。 どちらのタグにも正の整数値を指定する必要があります。 次の例に示すようなクエリを使用して、すべての VM をタグとその値と共に検索することができます。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 明示的に除外されている場合は、VM を起動または停止できない可能性があります。 除外対象の VM は、その機能のデプロイ先の Automation アカウントの `External_ExcludeVMNames` 変数で設定されます。 次の例は、PowerShell を使用してその値のクエリを実行する方法を示しています。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM を起動および停止するには、Automation アカウントの実行アカウントが、VM に対する適切なアクセス許可を持っている必要があります。 リソースに対するアクセス許可を確認する方法については、「[クイック スタート:Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値は、Azure portal の Automation アカウントに移動して取得できます。 **[アカウント設定]** で **[実行アカウント]** を選択し、適切な実行アカウントを選択します。
* 起動または割り当て解除に関する問題が VM で発生している場合は、VM 自体に問題がある可能性があります。 たとえば、VM のシャットダウン試行時の更新プログラムの適用や、サービスのハングなどです。 VM リソースに移動し、**アクティビティ ログ** にエラーが記録されているかどうかを確認してください。 また、VM にログインしてイベント ログにエラーが記録されているかどうかを確認することもできます。 ご自分の VM のトラブルシューティングの詳細については、「[Azure Virtual Machines のトラブルシューティング](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)」を参照してください。
* [ジョブ ストリーム](../automation-runbook-execution.md#job-statuses)を確認してエラーを探します。 ポータルで Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>シナリオ:カスタム Runbook を使用して VM を起動または停止できない

### <a name="issue"></a>問題

作成したカスタム Runbook または PowerShell ギャラリーからダウンロードした Runbook が適切に機能しません。

### <a name="cause"></a>原因

このエラーには多くの原因が考えられます。 Azure portal で Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。 **[ジョブ]** ページで、Runbook のジョブを検索してジョブのエラーを表示します。

### <a name="resolution"></a>解像度

推奨事項は次のとおりです。

* [Start/Stop VMs during off-hours](../automation-solution-vm-management.md) を使用して Azure Automation で VM を起動および停止します。 
* Microsoft ではカスタム Runbook はサポートされていないことに注意してください。 「[Runbook の問題のトラブルシューティング](runbooks.md)」から、カスタム Runbook の解決策が見つかる場合があります。 [ジョブ ストリーム](../automation-runbook-execution.md#job-statuses)を確認してエラーを探します。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>シナリオ:VM が正しい順序で起動または停止しない

### <a name="issue"></a>問題

機能で有効にした VM が正しい順序で起動も停止もされません。

### <a name="cause"></a>原因

この問題は、VM でのタグ付けが正しく行われていないことが原因で発生します。

### <a name="resolution"></a>解像度

次の手順に従い、機能が正しく有効になっていることを確認してください。

1. 起動または停止されるすべての VM に、状況に応じて `sequencestart` タグまたは `sequencestop` タグが指定されていることを確認します。 これらのタグの値は正の整数にする必要があります。 VM は、この値に基づいて昇順で処理されます。
1. 起動または停止される VM のリソース グループが、状況に応じて `External_Start_ResourceGroupNames` 変数または `External_Stop_ResourceGroupNames` 変数に指定されていることを確認します。
1. **SequencedStartStop_Parent** Runbook を実行して、変更内容をテストします。変更内容をプレビューするために、`WHATIF` パラメーターを True に設定します。

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>シナリオ:Start/Stop VMs during off-hours ジョブが 403 forbidden エラーで失敗する

### <a name="issue"></a>問題

Start/Stop VMs during off-hours Runbook に対してジョブが `403 forbidden` エラーで失敗します。

### <a name="cause"></a>原因

この問題は、実行アカウントが正しく構成されていないか、有効期限が切れている場合に発生する可能性があります。 また、実行アカウントによる VM リソースへのアクセス許可が不十分であることが原因である場合もあります。

### <a name="resolution"></a>解像度

実行アカウントが正しく構成されていることを確認するには、Azure portal で Automation アカウントにアクセスし、 **[アカウント設定]** の下で **[実行アカウント]** を選択します。 実行アカウントが正しく構成されていないか、有効期限が切れている場合、状態にその状況が表示されます。

実行アカウントの構成が誤っている場合、お使いの実行アカウントを削除して再作成します。 詳細については、[Azure Automation - 実行アカウント](../automation-security-overview.md#run-as-accounts)に関する記事を参照してください。

実行アカウントの証明書の期限が切れている場合は、「[自己署名証明書の書き換え](../manage-runas-account.md#cert-renewal)」の手順に従って証明書を書き換えてください。

アクセス許可が不足している場合は、「[クイックスタート: Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値は、Azure portal の Automation アカウントに移動して取得できます。 **[アカウント設定]** で **[実行アカウント]** を選択し、適切な実行アカウントを選択します。

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>シナリオ:問題がここの一覧にありません

### <a name="issue"></a>問題

Start/Stop VMs during off-hours を使用しているときに、このページに記載されていない問題や予期しない結果が発生します。

### <a name="cause"></a>原因

使用している機能のバージョンが古いと、何度もエラーが発生する可能性があります。

> [!NOTE]
> Start/Stop VMs during off-hours 機能は、この機能を VM にデプロイするときに、お使いの Automation アカウントにインポートされた Azure モジュールを使用してテストされています。 この機能は現在、Azure モジュールの新しいバージョンでは動作しません。 この制約は、Start/Stop VMs during off-hours の実行に使用している Automation アカウントのみに影響します。 「[Azure PowerShell モジュールの更新](../automation-update-azure-modules.md)」で説明されているように、他の Automation アカウントでは引き続き Azure モジュールの新しいバージョンを使用できます。

### <a name="resolution"></a>解像度

何度も発生するエラーを解決するには、[Start/Stop VMs during off-hours を削除して更新](../automation-solution-vm-management.md#update-the-feature)します。 また、[ジョブ ストリーム](../automation-runbook-execution.md#job-statuses)を確認してエラーがないか探します。 

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。