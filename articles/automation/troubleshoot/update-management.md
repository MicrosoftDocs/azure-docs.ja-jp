---
title: Azure Automation Update Management に関する問題のトラブルシューティング
description: この記事では、Azure Automation Update Management に関する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 01/13/2021
ms.topic: troubleshooting
ms.openlocfilehash: c16b032502401b633532ab0fcf9518aa85a1b8d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579733"
---
# <a name="troubleshoot-update-management-issues"></a>Update Management に関する問題のトラブルシューティング

この記事では、Update Management 機能をマシンにデプロイするときに発生する可能性がある問題について説明します。 根本的な問題を特定するためのハイブリッド Runbook Worker エージェント用のエージェント トラブルシューティング ツールがあります。 トラブルシューティング ツールの詳細については、「[Windows Update エージェントの問題をトラブルシューティングする](update-agent-issues.md)」と「[Linux Update エージェントに関する問題のトラブルシューティング](update-agent-issues-linux.md)」を参照してください。 その他の機能のデプロイについては、[「機能のデプロイに関する問題のトラブルシューティング」](onboarding.md)を参照してください。

>[!NOTE]
>Windows マシンに Update Management をデプロイしているときに問題が発生した場合は、Windows イベント ビューアーを開き、ローカル コンピューターの **[アプリケーションとサービス ログ]** の下にある **Operations Manager** イベント ログを確認します。 イベント ID が 4502 でイベントの詳細に `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` が含まれるイベントを探します。

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>シナリオ:Linux 更新プログラムが保留中として表示され、インストールされる更新プログラムが異なる

### <a name="issue"></a>問題

Linux マシンの場合、Update Management には、 **[セキュリティ]** と **[その他]** に分類される更新プログラムが表示されます。 ただし、 **[セキュリティ]** として分類される更新プログラムのみをインストールするなど、ある更新スケジュールがマシン上で実行されるとき、インストールされる更新プログラムは、その分類に一致する前に表示された更新プログラムとは異なるものになるか、その一部になります。

### <a name="cause"></a>原因

お使いの Linux マシンに対して保留になっている OS 更新プログラムの評価が終わると、Linux ディストリビューション ベンダーが提供する [Open Vulnerability and Assessment Language](https://oval.mitre.org/) (OVAL) が分類のために Update Management によって使用されます。 セキュリティの問題または脆弱性に対処する更新プログラムについて明記された OVAL ファイルに基づき、Linux 更新プログラムが **[セキュリティ]** か **[その他]** として分類されます。 ただし、更新プログラムが実行されるとき、YUM、APT、ZYPPER などの適切なパッケージ マネージャーを使用し、Linux マシン上で実行され、インストールされます。 Linux ディストリビューションのパッケージ マネージャーには、更新プログラムを分類する別のメカニズムが与えられていることがあります。結果は、Update Management によって OVAL ファイルから取得されるものと異なる場合があります。

### <a name="resolution"></a>解決方法

Linux マシン、適用できる更新プログラム、ディストリビューションのパッケージ マネージャー別のその分類を手動で確認できます。 お使いのパッケージ マネージャーによって **[セキュリティ]** として分類された更新プログラムを理解するには、次のコマンドを実行します。

YUM の場合、次のコマンドから、Red Hat によって **[セキュリティ]** として分類される更新プログラムのゼロではない一覧が返されます。 CentOS の場合、常に空の一覧が返されるか、セキュリティ分類が行われません。

```bash
sudo yum -q --security check-update
```

ZYPPER の場合、次のコマンドから、SUSE によって **[セキュリティ]** として分類される更新プログラムのゼロではない一覧が返されます。

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

APT の場合、次のコマンドから、Canonical for Ubuntu Linux ディストリビューションによって **[セキュリティ]** として分類される更新プログラムのゼロではない一覧が返されます。

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

この一覧から、コマンド `grep ^Inst` を実行し、保留中のセキュリティ更新プログラムをすべて取得します。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>シナリオ:"Failed to enable the Update solution" (Update ソリューションを有効にできませんでした) というエラーが表示される

### <a name="issue"></a>問題

Automation アカウントで Update Management ソリューションを有効にしようとすると、次のエラーが発生します。

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

このエラーは、次の理由で発生することがあります。

* Log Analytics エージェントのネットワーク ファイアウォールの要件が、正しく構成されていない可能性があります。 このような状況では、DNS URL を解決するときにエージェントが失敗する可能性があります。

* Update Management のターゲット設定が正しく構成されておらず、マシンが想定どおりに更新プログラムを受信していません。

* **[コンプライアンス]** に、マシンの状態が `Non-compliant` と表示されている場合もあります。 同時に、**エージェントの Desktop Analytics** でエージェントが `Disconnected` として報告されます。

### <a name="resolution"></a>解決方法

* OS に応じて、[Windows](update-agent-issues.md#troubleshoot-offline) 用または [Linux](update-agent-issues-linux.md#troubleshoot-offline) 用のトラブルシューティング ツールを実行します。

* [ネットワーク構成](../automation-hybrid-runbook-worker.md#network-planning)に関する記事にアクセスし、Update Management を動作させるために許可する必要があるアドレスとポートを確認してください。  

* スコープ構成に問題がないかどうかを確認します。 [スコープの構成](../update-management/scope-configuration.md)では、Update Management 用に構成されるマシンが決定されます。 ワークスペースに表示されているマシンが Update Management に表示されない場合、スコープ構成を設定してそのマシンをターゲットにする必要があります。 スコープ構成の詳細については、「[ワークスペースでのマシンの有効化](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)」を参照してください。

* 「[オンプレミスの Windows コンピューターから Hybrid Runbook Worker を削除する](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker)」または「[オンプレミスの Linux コンピューターから Hybrid Runbook Worker を削除する](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)」の手順に従い、worker 構成を削除します。

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>シナリオ:置き換え済みの更新プログラムが Update Management で不足として示される

### <a name="issue"></a>問題

古い更新プログラムは、置き換え済みであっても、Automation アカウントで不足として表示されます。 同じ脆弱性を修正しているより新しい更新プログラムを利用できるので、置き換え済みの更新プログラムは、インストールする必要がないものです。 Update Management では、置き換えが行われる更新プログラムを優先し、置き換え済みの更新プログラムは無視して適用しません。 関連する問題の詳細については、「[更新プログラムが置き換えられている](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)」を参照してください。

### <a name="cause"></a>原因

置き換え済みの更新プログラムは、適用外と見なされるように、Windows Server Update Services (WSUS) では拒否されません。

### <a name="resolution"></a>解決方法

置き換え済みの更新プログラムが 100% 適用されなくなっている場合には、その更新プログラムの承認状態を WSUS で `Declined` に変更する必要があります。 すべての更新プログラムの承認状態を変更するには:

1. Automation アカウントで **[Update Management]** を選択して、マシンの状態を表示します。 「[更新の評価を表示する](../update-management/view-update-assessments.md)」を参照してください。

2. 置き換え済みの更新プログラムが 100 パーセント適用されないことを確認します。

3. コンピューターの報告先となる WSUS サーバーで、[更新プログラムを拒否してください](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates)。

4. **[コンピューター]** を選択して、 **[コンプライアンス]** 列で、準拠するための再スキャンを適用します。 [VM の更新プログラムの管理](../update-management/manage-updates-for-vm.md)に関する記事を参照してください。

5. 置き換え済みの他の更新プログラムに対して、上記の手順を繰り返してください。

6. Windows Server Update Services (WSUS) の場合は、WSUS [Server クリーンアップ ウィザード](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)を使用し、置き換え済みの更新プログラムをすべて消去して、インフラストラクチャを更新します。

7. この手順を定期的に繰り返して表示の問題を修正し、更新プログラムの管理に使用されるディスク領域量を最小限に抑えます。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>シナリオ:Update Management のポータルにマシンが表示されない

### <a name="issue"></a>問題

マシンに次の現象が発生しています。

* VM の Update Management ビューでマシンが `Not configured` と表示される。

* Azure Automation アカウントの Update Management ビューに自分のマシンがない。

* **[コンプライアンス]** で `Not assessed` と表示されているマシンがある。 ただし、Hybrid Runbook Worker の Azure Monitor ログにはハートビート データが表示されているが、Update Management には表示されない。

### <a name="cause"></a>原因

この問題は、ローカル構成の問題や正しく設定されなかったスコープ構成が原因で発生する可能性があります。 考えられる具体的な原因は次のとおりです。

* Hybrid Runbook Worker の再登録と再インストールが必要になる場合があります。

* 自分のワークスペースで定義したクォータに達していて、それ以上のデータの格納が妨げられている可能性があります。

### <a name="resolution"></a>解決方法

1. OS に応じて、[Windows](update-agent-issues.md#troubleshoot-offline) 用または [Linux](update-agent-issues-linux.md#troubleshoot-offline) 用のトラブルシューティング ツールを実行します。

2. マシンが適切なワークスペースにレポートしていることを確認します。 この側面を確認する方法については、「[Azure Monitor へのエージェント接続を確認する](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)」を参照してください。 このワークスペースが Azure Automation アカウントにリンクされていることも確認します。 確認するには、自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. Automation アカウントにリンクされた Log Analytics ワークスペースにマシンが表示されることを確認します。 Log Analytics ワークスペースで、次のクエリを実行します。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    クエリ結果にマシンが表示されない場合は、最近チェックインされていません。 ローカルの構成に問題がある可能性があるため、[エージェントを再インストールする](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)必要があります。

    コンピューターがクエリ結果に一覧表示されている場合は、**Solutions** プロパティの下に **updates** が一覧表示されていることを確認します。 これにより、Update Management に登録されていることが確認できます。 そうでない場合は、スコープ構成に問題がないかどうかを確認します。 [スコープの構成](../update-management/scope-configuration.md)では、Update Management 用に構成されるマシンが決定されます。 対象のコンピューターのスコープ構成を構成するには、「[ワークスペースでのマシンの有効化](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)」を参照してください。

4. ワークスペースで、次のクエリを実行します。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` という結果が表示される場合、ワークスペースに定義されたクォータに達したため、データの保存が停止されています。 ワークスペースで **[使用量と推定コスト]** の **[データ ボリュームの管理]** に移動して、クォータを変更または削除します。

5. 問題が解決しない場合は、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」の手順に従って、Windows 用のハイブリッド worker を再インストールしてください。 Linux の場合は、「[Linux Hybrid Runbook Worker を展開する](../automation-linux-hrw-install.md)」の手順に従います。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>シナリオ:サブスクリプションで Automation リソース プロバイダーを登録できない

### <a name="issue"></a>問題

Automation アカウントで機能のデプロイを行うときに、次のエラーが発生します。

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

Automation リソース プロバイダーがサブスクリプションに登録されていません。

### <a name="resolution"></a>解決方法

Automation リソース プロバイダーを登録するには、Azure portal で次の手順に従います。

1. ポータルの下部にある Azure サービスの一覧で **[すべてのサービス]** を選択し、[全般] サービス グループの **[サブスクリプション]** を選択します。

2. サブスクリプションを選択します。

3. **[設定]** で、 **[リソース プロバイダー]** を選択します。

4. リソース プロバイダーの一覧で、Microsoft.Automation リソース プロバイダーが登録されていることを確認します。

5. 一覧に表示されていない場合は、「[リソース プロバイダーの登録エラーの解決](../../azure-resource-manager/templates/error-register-resource-provider.md)」の手順に従って、Microsoft.Automation プロバイダーを登録します。

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>シナリオ:動的スケジュールでスケジュールされた更新で一部のマシンが見つからない

### <a name="issue"></a>問題

更新プレビューに含まれるすべてのマシンが、スケジュールされた実行中に修正プログラムが適用されたマシンの一覧に表示されません。

### <a name="cause"></a>原因

この問題には、次のいずれかの原因が考えられます。

* 動的クエリのスコープで定義されているサブスクリプションが、登録済みの Automation リソース プロバイダーに対して構成されていません。

* スケジュールが実行されたときに、マシンが使用できなかったか、マシンに適切なタグがありませんでした。

### <a name="resolution"></a>解決方法

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>登録済みの Automation リソース プロバイダー用に構成されていないサブスクリプション

サブスクリプションが Automation リソース プロバイダー用に構成されていない場合、そのサブスクリプション内のマシンの情報をクエリまたはフェッチすることはできません。 サブスクリプションの登録を確認するには、次の手順のようにします。

1. [Azure portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) で、Azure サービス一覧にアクセスします。

2. **[すべてのサービス]** を選択し、[全般] サービス グループで **[サブスクリプション]** を選択します。

3. デプロイのスコープで定義されているサブスクリプションを検索します。

4. **[設定]** で、 **[リソース プロバイダー]** を選択します。

5. Microsoft.Automation リソース プロバイダーが登録されていることを確認します。

6. 一覧に表示されていない場合は、「[リソース プロバイダーの登録エラーの解決](../../azure-resource-manager/templates/error-register-resource-provider.md)」の手順に従って、Microsoft.Automation プロバイダーを登録します。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>スケジュールの実行時に、マシンが使用できない、またはマシンに正しくタグが付けられていない

サブスクリプションが Automation リソース プロバイダー用に構成されているのに、指定した[動的グループ](../update-management/configure-groups.md)で更新スケジュールを実行すると一部のマシンが見つからない場合は、次の手順を使用します。

1. Azure portal で、Automation アカウントを開き、 **[更新の管理]** を選択します。

2. [Update Management の履歴](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)を調べて、更新プログラムのデプロイが実行された正確な時刻を確認します。

3. Update Management によって検出されない疑いのあるマシンについて、Azure Resource Graph (ARG) を使用して[マシンの変更を特定](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)します。

4. 更新プログラムのデプロイが実行される前のある程度の期間 (1 日など) について、変更を検索します。

5. 検索結果で、この期間内にマシンに対して行われた、削除や更新の変更などの体系的な変更を確認します。 これらの変更により、マシンの状態またはタグが変更され、更新プログラムのデプロイ時にマシンの一覧でマシンが選択されなくなった可能性があります。

6. 必要に応じてマシンとリソースの設定を調整し、マシンの状態またはタグの問題を修正します。

7. 更新スケジュールを再実行し、指定した動的グループでのデプロイにすべてのマシンが含まれることを確認します。

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>シナリオ:予期されるマシンが動的グループのプレビューに表示されない

### <a name="issue"></a>問題

動的グループの選択したスコープの VM が、Azure portal のプレビュー一覧に表示されません。 この一覧は、選択したスコープに対して ARG クエリによって取得されたすべてのマシンで構成されます。 スコープは、Hybrid Runbook Worker がインストールされていて、ユーザーがアクセス許可を持っているマシンでフィルター処理されます。

### <a name="cause"></a>原因

この問題の考えられる原因は次のとおりです。

* 選択したスコープに対する適切なアクセス権がありません。
* ARG クエリで、予期されるマシンが取得されません。
* Hybrid Runbook Worker がマシンにインストールされていません。

### <a name="resolution"></a>解決方法 

#### <a name="incorrect-access-on-selected-scopes"></a>選択したスコープに対する正しくないアクセス権

Azure portal には、ユーザーが特定のスコープで書き込みアクセス権を持っているマシンのみが表示されます。 スコープに対する適切なアクセス権がない場合は、「[チュートリアル: Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する](../../role-based-access-control/quickstart-assign-role-user-portal.md)」を参照してください。

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG クエリで予期されるマシンが返されない

以下の手順のようにして、クエリが正常に機能しているかどうかを確認します。

1. Azure portal の Resource Graph エクスプローラー ブレードで、次のように書式設定された ARG クエリを実行します。 このクエリでは、Update Management で動的グループを作成したときに選択したフィルターが模倣されます。 「[Update Management を利用して動的グループを使用する](../update-management/configure-groups.md)」を参照してください。

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   たとえば次のようになります。

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. 検索しているマシンがクエリ結果に表示されるかどうかを確認します。

3. マシンが一覧に表示されない場合は、動的グループで選択されているフィルターに問題がある可能性があります。 必要に応じてグループの構成を調整します。

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker がマシンにインストールされていない

マシンは ARG クエリの結果には表示されますが、動的グループのプレビューにはやはり表示されません。 この場合、マシンがハイブリッド workerとして指定されていないため、Azure Automation ジョブや Update Management ジョブを実行できない可能性があります。 表示されるはずのマシンが Hybrid Runbook Worker として設定されていることを確認するには、次のようにします。

1. Azure portal で、正しく表示されないマシンの Automation アカウントに移動します。

2. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。

3. **[システム ハイブリッド worker グループ]** タブを選択します。

4. そのマシンにハイブリッド worker が存在することを確認します。

5. マシンがハイブリッド worker として設定されていない場合は、「[Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する](../automation-hybrid-runbook-worker.md)」の手順を使用して調整します。

6. マシンを Hybrid Runbook Worker グループに参加させます。

7. プレビューに表示されないすべてのマシンに対して上記の手順を繰り返します。

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>シナリオ:Update Management のコンポーネントが有効になっているが、VM は構成中と表示されたままである

### <a name="issue"></a>問題

デプロイの開始から 15 分経過しても、VM に関する次のメッセージが引き続き表示されます。

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

このエラーは、次の理由で発生することがあります。

* Automation アカウントとの通信がブロックされています。

* ソース コンピューター ID が異なるが、コンピューター名が重複するものがあります。 このシナリオは、異なる複数のリソース グループ内に、ある特定のコンピューター名を持つ VM が作成され、サブスクリプションの同じロジスティック エージェント ワークスペースにレポートしている場合に発生します。

* デプロイしている VM イメージの複製元が、Windows 用の Log Analytics エージェントがインストールされた状態でシステム準備 (sysprep) を使用して準備されなかった複製マシンである可能性があります。

### <a name="resolution"></a>解決方法

VM の問題を正確に特定するには、Automation アカウントにリンクされた Log Analytics ワークスペースで、次のクエリを実行します。

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Automation アカウントとの通信がブロックされる

[ネットワークの計画](../update-management/overview.md#ports)に関する記事にアクセスし、Update Management を動作させるために許可する必要があるアドレスとポートを確認してください。

#### <a name="duplicate-computer-name"></a>コンピューター名の重複

VM の名前を変更して、環境内で一意の名前となるようにします。

#### <a name="deployed-image-from-cloned-machine"></a>複製マシンからのデプロイされたイメージ

複製されたイメージを使用している場合は、異なるコンピューター名で同じソース コンピューター ID を保持します。 この場合、次のようになります。

1. Log Analytics ワークスペースで、`MicrosoftDefaultScopeConfig-Updates` スコープ構成の保存された検索条件から VM を削除します (表示された場合)。 保存された検索条件は、ワークスペース内の **[全般]** にあります。

2. 次のコマンドレットを実行します。

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. `Restart-Service HealthService` を実行して、ヘルス サービスを再起動します。 この操作により、キーが再作成され、新しい UUID が生成されます。

4. この方法がうまくいかない場合は、まずイメージで sysprep を実行してから、Windows 向け Log Analytics エージェントをインストールします。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>シナリオ:別の Azure テナントのマシンを対象とした更新プログラムの展開を作成するときに、リンクされているサブスクリプションのエラーが発生する

### <a name="issue"></a>問題

別の Azure テナントのマシンを対象とした更新プログラムの展開を作成しようとすると、次のエラーが発生します。

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

このエラーは、更新プログラムの展開に含まれる別のテナントの Azure VM を持つ更新プログラムの展開を作成するときに発生します。

### <a name="resolution"></a>解決方法

次の回避策を使用して、これらの項目をスケジュールします。 スケジュールを作成するには、`ForUpdateConfiguration` パラメーターを指定して [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) コマンドレットを使用します。 次に、[New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) コマンドレットを使用して、他のテナントのマシンを `NonAzureComputer` パラメーターに渡します。 以下の例は、その方法を示しています。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>シナリオ:原因不明の再起動

### <a name="issue"></a>問題

**[Reboot control]\(再起動制御\)** オプションを **[再起動しない]** に設定しても、更新プログラムのインストール後にマシンが再起動します。

### <a name="cause"></a>原因

Windows Update はいくつかのレジストリ キーによって変更でき、そのいずれかによって再起動の動作が変更されることがあります。

### <a name="resolution"></a>解決方法

「[レジストリを編集して自動更新を構成する](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)」と、[「再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に記載されているレジストリ キーを確認して、マシンが正しく構成されていることを確認します。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>シナリオ:更新プログラムの展開で "起動できませんでした" とマシンに表示される

### <a name="issue"></a>問題

マシンが `Failed to start` の状態と表示されます。 マシンの特定の詳細情報を表示すると、次のエラーが表示されます。

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

このエラーは、次のいずれかの理由で発生することがあります。

* そのマシンはもう存在しません。
* マシンの電源が切れていて到達できません。
* マシンにネットワーク接続の問題があるため、マシン上のハイブリッド worker にアクセスできません。
* Log Analytics エージェントに対する更新があり、ソース コンピューター ID が変更されました。
* Automation アカウントで 200 個の同時ジョブの制限に達した場合、更新の実行が制限されました。 各展開は 1 つのジョブと見なされ、更新プログラムの展開内の各マシンは 1 つのジョブとカウントされます。 Automation アカウントで現在実行されている他のオートメーション ジョブや更新プログラムの展開は、すべて同時ジョブ制限の対象になります。

### <a name="resolution"></a>解決方法

該当する場合は、更新プログラムの展開に[動的グループ](../update-management/configure-groups.md)を使用します。 さらに、次の手順を実行できます。

1. コンピューターまたはサーバーが[要件](../update-management/overview.md#system-requirements)を満たしていることを確認します。
2. Hybrid Runbook Worker エージェントのトラブルシューティングツールを使用して、Hybrid Runbook Worker への接続を確認します。 このトラブルシューティング ツールの詳細については、[更新エージェントの問題のトラブルシューティング](update-agent-issues.md)に関する記事を参照してください。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>シナリオ:展開なしで更新プログラムがインストールされる

### <a name="issue"></a>問題

Update Management に Windows マシンを登録すると、展開なしで更新プログラムがインストールされます。

### <a name="cause"></a>原因

Windows では、更新プログラムは、使用可能になるとすぐに自動的にインストールされます。 この動作が原因で、更新プログラムをマシンに展開するスケジュールを設定しなかった場合、混乱が生じる可能性があります。

### <a name="resolution"></a>解決方法

レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` の既定値は、4: `auto download and install` に設定されています。

Update Management クライアントの場合は、このキーを 3: `auto download but do not auto install` に設定することをお勧めします。

詳細については、[自動更新の構成](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)に関する記事を参照してください。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>シナリオ:マシンが違うアカウントに既に登録されている

### <a name="issue"></a>問題

次のエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

マシンが既に Update Management 用の別のワークスペースにデプロイされています。

### <a name="resolution"></a>解決方法

1. 「[Update Management のポータルにマシンが表示されない](#nologs)」の手順に従って、マシンのレポート先が正しいワークスペースであることを確認します。
2. [Hybrid Runbook グループを削除する](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)ことにより、マシン上のアーティファクトをクリーンアップしてから、再試行します。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>シナリオ:マシンがサービスと通信できない

### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>原因

プロキシ、ゲートウェイ、またはファイアウォールがネットワーク通信をブロックしている可能性があります。

### <a name="resolution"></a>解決方法

ネットワークを見直し、適切なポートとアドレスが許可されていることを確認します。 Update Management および Hybrid Runbook Worker で必要なポートとアドレスの一覧については、[ネットワーク要件](../automation-hybrid-runbook-worker.md#network-planning)を参照してください。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>シナリオ:自己署名証明書を作成できない

### <a name="issue"></a>問題

次のいずれかのエラー メッセージが表示されます。

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

Hybrid Runbook Worker が自己署名証明書を生成できませんでした。

### <a name="resolution"></a>解決方法

**C:\ProgramData\Microsoft\Crypto\RSA** フォルダーへの読み取りアクセスがシステム アカウントにあることを確認してから、再試行します。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>シナリオ:スケジュールされた更新が、MaintenanceWindowExceeded エラーで失敗しました

### <a name="issue"></a>問題

更新の既定のメンテナンス時間は 120 分です。 メンテナンス期間は、最大 6 時間つまり 360 分まで増やすことができます。

### <a name="resolution"></a>解決方法

正常に開始した後、更新プログラムの実行中にこれが発生した理由を理解するには、実行で影響を受けたマシンからの[ジョブ出力を確認](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)します。 マシンからの特定のエラー メッセージが見つかれば、調査して対処することができます。  

スケジュール済みの更新プログラムの展開で失敗したものがあれば編集し、メンテナンス期間を延長します。

メンテナンス期間の詳細については、[更新プログラムのインストール](../update-management/deploy-updates.md#schedule-an-update-deployment)に関する記事を参照してください。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>シナリオ:マシンに "Not assessed" (評価が行われていません) と表示され、HRESULT 例外が表示される

### <a name="issue"></a>問題

* **[コンプライアンス]** に `Not assessed` と表示されているマシンがあり、その下に例外メッセージが表示されます。
* ポータルに HRESULT エラー コードが表示されます。

### <a name="cause"></a>原因

更新エージェント (Windows 上の Windows Update エージェント、Linux ディストリビューション用のパッケージ マネージャー) が正しく構成されていません。 Update Management は、必要な更新プログラム、パッチの状態、展開されたパッチの結果を提供するために、マシンの更新エージェントを利用しています。 この情報がないと、Update Management は必要なパッチやインストール済みのパッチを適切にレポートすることができません。

### <a name="resolution"></a>解決方法

マシンで更新プログラムをローカルで実行してみてください。 この操作が失敗する場合は、通常、更新エージェントの構成にエラーがあることを意味します。

この問題は、ネットワーク構成とファイアウォールの問題が原因で発生することがよくあります。 以下のチェックを使用して問題を修正します。

* Linux の場合は、適切なドキュメントを確認して、パッケージ リポジトリのネットワーク エンドポイントにアクセスできることを確認します。

* Windows の場合は、「[イントラネット エンドポイントから更新プログラムがダウンロードされない (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)」の一覧にあるエージェントの構成を確認してください。

  * マシンが Windows Update 用に構成されている場合は、「[HTTP またはプロキシに関連する問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)」で説明されているエンドポイントにアクセスできることを確認します。
  * マシンが Windows Server Update Services (WSUS) 用に構成されている場合は、[WUServer レジストリ キー](/windows/deployment/update/waas-wu-settings)によって構成された WSUS サーバーにアクセスできることを確認します。

HRESULT が表示される場合は、赤で表示された例外をダブルクリックして、例外メッセージ全体を確認します。 考えられる解決策または推奨される対策については、次の表を参照してください。

|例外  |解決策または対策  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows Update エラー コード一覧](https://support.microsoft.com/help/938205/windows-update-error-code-list)で該当するエラー コードを検索して、例外の原因に関する詳細を確認します。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | これらはネットワーク接続の問題を示しています。 マシンが Update Management にネットワーク接続されていることを確認します。 必要なポートとアドレスの一覧については、「[ネットワークの計画](../update-management/overview.md#ports)」セクションを参照してください。        |
|`0x8024001E`| サービスまたはシステムがシャットダウン中のため、更新操作が完了しませんでした。|
|`0x8024002E`| Windows Update サービスが無効です。|
|`0x8024402C`     | WSUS サーバーを使用している場合は、レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` の下の `WUServer` と `WUStatusServer` のレジストリ値で正しい WSUS サーバーが指定されていることを確認します。        |
|`0x80072EE2`|ネットワーク接続の問題、または構成された WSUS サーバーとの通信に問題があります。 WSUS 設定を確認し、サービスがクライアントからアクセス可能であることを確認します。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows Update サービス (wuauserv) が実行されており、無効になっていないことを確認します。        |
|`0x80070005`| アクセス拒否エラーは、次に示すいずれかの原因により発生する可能性があります。<br> 感染したコンピューター<br> Windows Update の設定が正しく構成されていない<br> %WinDir%\SoftwareDistribution フォルダーのファイル アクセス許可エラー<br> システム ドライブ (C:) のディスク領域不足
|その他の一般的な例外     | 可能な解決策についてインターネットで検索を行い、最寄りの IT サポートと連携します。         |

また、 **%Windir%\Windowsupdate.log** ファイルを確認すると、考えられる原因の特定に役立ちます。 ログの読み取り方法の詳細については、「[Windowsupdate.log ファイルの解釈](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)」を参照してください。

[Windows Update トラブルシューティング ツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)をダウンロードして実行し、マシン上の Windows Update に問題がないか確認することもできます。

> [!NOTE]
> [Windows Update トラブルシューティングツール](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)のドキュメントに、Windows クライアントでの使用向けであると記載されていますが、Windows Server でも動作します。

## <a name="scenario-update-run-returns-failed-status-linux"></a>シナリオ:更新プログラムの実行で失敗状態が返される (Linux)

### <a name="issue"></a>問題

更新プログラムの実行が開始されますが、実行中にエラーが発生します。

### <a name="cause"></a>原因

考えられる原因:

* パッケージ マネージャーが異常です。
* 更新エージェント (Windows の場合は WUA、Linux の場合はディストリビューション固有のパッケージ マネージャー) が正しく構成されていません。
* 特定のパッケージが、クラウド ベースの修正プログラムの適用を妨げています。
* マシンにアクセスできません。
* 更新プログラムに、解決されていない依存関係がありました。

### <a name="resolution"></a>解決方法

正常に開始した後に更新プログラムの実行中にエラーが発生した場合は、実行で影響を受けたマシンからの[ジョブ出力を確認](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)します。 マシンからの特定のエラー メッセージが見つかれば、調査して対処することができます。 Update Management で更新プログラムをデプロイするには、パッケージ マネージャーが正常である必要があります。

ジョブが失敗する直前に特定の修正プログラム、パッケージ、または更新プログラムが表示される場合は、次の更新プログラムのデプロイからこれらの項目を[除外](../update-management/deploy-updates.md#schedule-an-update-deployment)してみることができます。 Windows Update からログ情報を収集するには、「[Windows Update のログ ファイル](/windows/deployment/update/windows-update-logs)」を参照してください。

修正プログラムの問題を解決できない場合は、次の更新プログラムのデプロイが開始される前に、トラブルシューティングのために **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** ファイルをコピーして、保存してください。

## <a name="patches-arent-installed"></a>修正プログラムがインストールされない

### <a name="machines-dont-install-updates"></a>マシンで更新プログラムがインストールされない

マシン上で直接更新プログラムを実行してみてください。 マシンで更新プログラムを適用できない場合は、[トラブルシューティング ガイドで、発生する可能性のあるエラーの一覧](#hresult)をご覧ください。

更新プログラムがローカルで実行される場合は、[Update Management からの VM の削除](../update-management/remove-vms.md)に関する記事のガイダンスに従って、マシン上でエージェントを削除し、再インストールしてみてください。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>更新プログラムを利用できることはわかっているが、自分のマシンに利用可能として表示されない

これは、多くの場合、WSUS または Microsoft Endpoint Configuration Manager から更新プログラムを取得するようにマシンが構成されているのに、WSUS および Configuration Manager で更新プログラムが承認されなかった場合に発生します。

WSUS と SCCM 用にマシンが構成されているかどうかを確認するには、[こちらの記事の「レジストリを編集して自動更新を構成する」セクション](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)内のレジストリ キーに対して `UseWUServer` レジストリ キーを相互参照することでチェックできます。

更新プログラムは、WSUS 内で承認されていない場合、インストールされません。 次のクエリを実行して、Log Analytics 内で未承認の更新プログラムを確認できます。

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>インストールされると更新プログラムが表示されますが、コンピューター上で検索できない

更新プログラムは多くの場合、他の更新プログラムによって置き換えられます。 詳細については、Windows Update のトラブルシューティング ガイドの「[更新プログラムは置き換えられました](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)」を参照してください。

### <a name="installing-updates-by-classification-on-linux"></a>Linux での更新プログラムの分類別インストール

分類 ([緊急更新プログラムとセキュリティ更新プログラム]) 別に Linux に更新プログラムを展開する場合、特に CentOS に関する重要な注意事項があります。 これらの制限事項は、[Update Management の概要に関するページ](../update-management/overview.md#linux)に記載されています。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 が常に欠落している

KB2267602 は [Windows Defender の定義更新](https://www.microsoft.com/wdsi/definitions)です。 これは毎日更新されます。

## <a name="next-steps"></a>次のステップ

自分の問題が見つからない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。
* Azure サポート インシデントを送信する。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
