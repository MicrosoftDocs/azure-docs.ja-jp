---
title: Azure Automation を使用した VM の起動と停止のトラブルシューティング
description: この記事では、Azure Automation での VM の起動と停止のトラブルシューティングについて説明します
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1817d8e060f944b1bcc31c8ea9eb4fbcff58a165
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850110"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Start/Stop VMs during off-hours ソリューションのトラブルシューティング

## <a name="deployment-failure"></a>シナリオ:VM の起動/停止ソリューションを正常にデプロイできない

### <a name="issue"></a>問題

[時間外の VM の起動/停止ソリューション](../automation-solution-vm-management.md)のデプロイで、次のいずれかのエラーが発生しました。

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

1. 選択したリージョンに同じ名前の Automation アカウントが既に存在している。
2. VM の起動/停止ソリューションのデプロイを許可しないポリシーが設定されている。
3. リソースの種類として `Microsoft.OperationsManagement`、`Microsoft.Insights`、または `Microsoft.Automation` が登録されていない。
4. Log Analytics ワークスペースがロックされている。
5. 古いバージョンの AzureRM モジュールまたは起動/停止ソリューションを使用しています。

### <a name="resolution"></a>解決策

問題の考えられる解決策または参照する場所については、次の一覧を確認してください。

1. Automation アカウントは、異なるリソース グループ内にある場合でも、Azure リージョン内で一意である必要があります。 ターゲット リージョンの既存の Automation アカウントをチェックします。
2. 既存のポリシーによって、VM の起動/停止ソリューションをデプロイするために必要なリソースの使用が妨げられています。 Azure portal でポリシーの割り当てに移動し、そのリソースのデプロイを許可していないポリシーの割り当てがあるかどうかを確認します。 詳細については、[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md) を参照してください。
3. VM の起動/停止ソリューションをデプロイするには、次の Azure リソースの名前空間にサブスクリプションが登録されている必要があります。
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   プロバイダーの登録時のエラーについては、「[Resolve errors for resource provider registration (リソースプロバイダーの登録エラーを解決する)](../../azure-resource-manager/resource-manager-register-provider-errors.md)」を参照してください。
4. Log Analytics ワークスペースがロックされている場合は、Azure portal でワークスペースに移動し、すべてのリソースのロックを削除します。
5. 上記の解決方法でイシューが解決しない場合は、「[ソリューションを更新する](../automation-solution-vm-management.md#update-the-solution)」の手順に従って、起動/停止ソリューションを再デプロイします。

## <a name="all-vms-fail-to-startstop"></a>シナリオ:すべての VM を起動/停止できない

### <a name="issue"></a>問題

VM の起動/停止ソリューションを構成したが、構成されているすべての VM が起動または停止しません。

### <a name="cause"></a>原因

このエラーは、次に示すいずれかの理由で発生する可能性があります。

1. スケジュールが正しく構成されていない
2. 実行アカウントが正しく構成されていない
3. Runbook でエラーが発生した
4. VM が除外されている

### <a name="resolution"></a>解決策

問題の考えられる解決策または参照する場所については、次の一覧を確認してください。

* VM の起動/停止ソリューションのスケジュールが適切に構成されていることを確認します。 スケジュールを構成する方法については、[スケジュール](../automation-schedules.md)に関する記事を参照してください。

* [ジョブ ストリーム](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)を確認してエラーを探します。 ポータルで Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。 **[ジョブ]** ページで、次に示すいずれかの Runbook からジョブを探します。

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* 起動または停止しようとしている VM に対する適切なアクセス許可が[実行アカウント](../manage-runas-account.md)にあることを確認します。 リソースに対するアクセス許可を確認する方法については、「[クイック スタート:Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値を取得するには、Azure portal で Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択して適切な実行アカウントをクリックします。

* VM が明示的に除外されている場合は、VM を起動または停止できません。 除外対象の VM は、ソリューションのデプロイ先の Automation アカウントの **External_ExcludeVMNames** 変数に設定されます。 次の例は、PowerShell を使用してその値のクエリを実行する方法を示しています。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>シナリオ:一部の VM を起動または停止できない

### <a name="issue"></a>問題

VM の起動/停止ソリューションを構成したが、構成されている一部の VM が起動または停止しません。

### <a name="cause"></a>原因

このエラーは、次に示すいずれかの理由で発生する可能性があります。

1. 順序指定のシナリオを使用する場合に、タグが見つからないか正しくない
2. VM が除外されている
3. VM に対する十分なアクセス許可が実行アカウントにない
4. VM の起動または停止を妨げる問題が発生した

### <a name="resolution"></a>解決策

問題の考えられる解決策または参照する場所については、次の一覧を確認してください。

* Start/Stop VMs during off-hours ソリューションの[順序指定のシナリオ](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)を使用する場合は、起動または停止する各 VM に適切なタグが指定されていることを確認する必要があります。 起動する VM には `sequencestart` タグ、停止する VM には `sequencestop` タグが指定されていることを確認してください。 どちらのタグにも正の整数値を指定する必要があります。 次の例に示すようなクエリを使用して、すべての VM をタグとその値と共に検索することができます。

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM が明示的に除外されている場合は、VM を起動または停止できません。 除外対象の VM は、ソリューションのデプロイ先の Automation アカウントの **External_ExcludeVMNames** 変数に設定されます。 次の例は、PowerShell を使用してその値のクエリを実行する方法を示しています。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM を起動および停止するには、Automation アカウントの実行アカウントに VM に対する適切なアクセス許可が必要です。 リソースに対するアクセス許可を確認する方法については、「[クイック スタート:Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値を取得するには、Azure portal で Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択して適切な実行アカウントをクリックします。

* 起動または割り当て解除に関する問題が VM で発生している場合は、VM 自体の問題が原因でこの動作が引き起こされる可能性があります。 一部の例または潜在的な問題としては、シャットダウン試行時の更新の適用、サービスのハングなどがあります。 VM リソースに移動して、**アクティビティ ログ**にエラーが記録されているかどうかを確認してください。 また、VM にログインしてイベント ログにエラーが記録されているかどうかを確認することもできます。 ご自分の VM のトラブルシューティングの詳細については、「[Azure Virtual Machines のトラブルシューティング](../../virtual-machines/troubleshooting/index.md)」を参照してください。

* [ジョブ ストリーム](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)を確認してエラーを探します。 ポータルで Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。

## <a name="custom-runbook"></a>シナリオ:カスタム Runbook を使用して VM を起動または停止できない

### <a name="issue"></a>問題

作成したカスタム Runbook または PowerShell ギャラリーからダウンロードした Runbook が適切に機能しません。

### <a name="cause"></a>原因

このエラーには多くの原因が考えられます。 Azure portal で Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。 **[ジョブ]** ページで、Runbook のジョブを検索してジョブのエラーを表示します。

### <a name="resolution"></a>解決策

[Start/Stop VMs during off-hours ソリューション](../automation-solution-vm-management.md)を使用して Azure Automation で VM を起動および停止することをお勧めします。 このソリューションは Microsoft が作成したものです。 カスタム Runbook は Microsoft ではサポートされていません。 [Runbook のトラブルシューティング](runbooks.md)の記事を参照すると、カスタム Runbook に関する解決策が見つかる場合があります。 この記事では、すべての種類の Runbook に関する一般的なガイダンスとトラブルシューティングを示します。 [ジョブ ストリーム](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)を確認してエラーを探します。 ポータルで Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。

## <a name="dont-start-stop-in-sequence"></a>シナリオ:VM が正しい順序で起動または停止しない

### <a name="issue"></a>問題

ソリューションで構成した VM が正しい順序で起動または停止しません。

### <a name="cause"></a>原因

これは、VM 上でタグ付けが正しく行われていないために発生します。

### <a name="resolution"></a>解決策

次の手順を実行して、ソリューションが正しく構成されていることを確認してください。

1. 起動または停止するすべての VM に、状況に応じて `sequencestart` タグまたは `sequencestop` タグが指定されていることを確認します。 これらのタグの値は正の整数にする必要があります。 VM は、この値に基づいて昇順で処理されます。
2. 起動または停止する VM のリソース グループが、状況に応じて `External_Start_ResourceGroupNames` 変数または`External_Stop_ResourceGroupNames` 変数に指定されていることを確認します。
3. 変更内容をテストします。そのためには、WHATIF パラメーターを True に設定して `SequencedStartStop_Parent` Runbook を実行し、変更内容をプレビューします。

ソリューションを使用して VM を順番に起動/停止する方法の詳細および追加の手順については、[VM を順番に起動/停止する](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)方法に関するページを参照してください。

## <a name="403"></a>シナリオ:VM の起動/停止ジョブが "403 許可されていません" 状態で失敗する

### <a name="issue"></a>問題

Start/Stop VMs during off-hours ソリューションの Runbook に対する `403 forbidden` エラーでジョブが失敗します。

### <a name="cause"></a>原因

この問題は、実行アカウントが正しく構成されていない、または期限切れのために発生する可能性があります。 また、VM リソースに対する適切なアクセス許可が Automation アカウントの実行アカウントにないために発生する可能性もあります。

### <a name="resolution"></a>解決策

実行アカウントが適切に構成されていることを確認するには、Azure portal で Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択します。 ここでは、実行アカウントが適切に構成されていない場合や、実行アカウントの期限が切れている場合に、その状態が表示されます。

実行アカウントの[構成が誤っている](../manage-runas-account.md#misconfiguration)場合は、実行アカウントを削除して再作成する必要があります。

実行アカウントの証明書の期限が切れている場合は、「[自己署名証明書の書き換え](../manage-runas-account.md#cert-renewal)」の手順に従って証明書を書き換えてください。

この問題は、アクセス許可がない場合に発生する可能性があります。 リソースに対するアクセス許可を確認する方法については、「[クイック スタート:Azure portal を使用してユーザーに割り当てられているロールを表示する](../../role-based-access-control/check-access.md)」を参照してください。 実行アカウントで使用されるサービス プリンシパルのアプリケーション ID を指定する必要があります。 この値を取得するには、Azure portal で Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択して適切な実行アカウントをクリックします。

## <a name="other"></a>シナリオ:問題が上記の一覧にない

### <a name="issue"></a>問題

Start/Stop VMs during off-hours ソリューションの使用時に、このページに記載のない問題または予期しない結果が生じます。

### <a name="cause"></a>原因

使用しているソリューションのバージョンが古いと、何度もエラーが発生する可能性があります。

> [!NOTE]
> Start/Stop VMs during off-hours ソリューションは、このソリューションのデプロイ時にご使用の Automation アカウントにインポートされた Azure モジュールを使用してテストされています。 このソリューションは現在、Azure モジュールの新しいバージョンでは動作しません。 これは、Start/Stop VMs during off-hours ソリューションの実行に使用する Automation アカウントのみに影響します。 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」で説明しているように、ご使用の他の Automation アカウントでは引き続き Azure モジュールの新しいバージョンを使用できます。

### <a name="resolution"></a>解決策

何度も発生するエラーを解決するには、ソリューションを削除および更新することをお勧めします。 ソリューションを更新する方法については、[Start/Stop VMs during off-hours ソリューションの更新](../automation-solution-vm-management.md#update-the-solution)に関するページを参照してください。 さらに、[ジョブ ストリーム](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)を確認してエラーを探すことができます。 ポータルで Automation アカウントに移動し、 **[プロセス オートメーション]** の下で **[ジョブ]** を選択します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
