---
title: Azure Automation 機能のデプロイに関する問題のトラブルシューティング
description: この記事では、Azure Automation 機能をデプロイするときに発生する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651121"
---
# <a name="troubleshoot-feature-deployment-issues"></a>機能のデプロイに関する問題のトラブルシューティング

Azure Automation Update Management 機能、または Change Tracking および Inventory 機能を VM 上にデプロイするときに、エラー メッセージが表示される場合があります。 この記事では、発生する可能性があるエラーと、その解決方法について説明します。

## <a name="known-issues"></a>既知の問題

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>シナリオ:登録されたノードの名前を変更するには、登録の解除または再登録が必要です

#### <a name="issue"></a>問題

ノードが Azure Automation に登録された後、オペレーティング システムのコンピューター名が変更されました。 そのノードからのレポートは、引き続き元の名前で表示されます。

#### <a name="cause"></a>原因

登録されたノードの名前を変更しても、Azure Automation 内のノード名は更新されません。

#### <a name="resolution"></a>解決方法

Azure Automation State Configuration からノードの登録を解除し、再度登録してください。 それより前にサービスに発行されたレポートは、使用できなくなります。

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>シナリオ:HTTPS プロキシ経由の証明書の再署名がサポートされていません

#### <a name="issue"></a>問題

HTTPS トラフィックを終了してから新しい証明書を使用してそのトラフィックを再暗号化するプロキシを介して接続する場合に、その接続がサービスによって許可されません。

#### <a name="cause"></a>原因

Azure Automation では、トラフィックの暗号化に使用される証明書の再署名はサポートされていません。

#### <a name="resolution"></a>解決方法

現時点では、この問題の回避策はありません。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>シナリオ:機能のデプロイが失敗し、"ソリューションを有効にできません" というメッセージが表示されます

#### <a name="issue"></a>問題

VM 上で機能を有効にしようとすると、次のメッセージのいずれかが表示されます。

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

このエラーは、VM またはワークスペースに対するアクセス許可、またはユーザーのアクセス許可が正しくないか、不足しているために発生します。

#### <a name="resolution"></a>解決方法

[機能のデプロイの正しいアクセス許可](../automation-role-based-access-control.md#feature-setup-permissions)があることを確保してから、この機能を再度デプロイしてみてください。 エラー メッセージ `The solution cannot be enabled on this VM because the permission to read the workspace is missing` が表示された場合は、次の[トラブルシューティング情報](update-management.md#failed-to-enable-error)を参照してください。

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>シナリオ:機能のデプロイが失敗し、"診断ログのオートメーション アカウントを構成できませんでした" というメッセージが表示されます

#### <a name="issue"></a>問題

VM 上で機能を有効にしようとすると、次のメッセージが表示されます。

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

このエラーは、価格レベルがサブスクリプションの課金モデルと一致しない場合に発生することがあります。 詳細については、「[Azure Monitor での使用量と推定コストの監視](../../azure-monitor//usage-estimated-costs.md)」を参照してください。

#### <a name="resolution"></a>解決方法

Log Analytics ワークスペースを手動で作成し、機能のデプロイ プロセスを繰り返して、作成したワークスペースを選択します。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>シナリオ:ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

このエラー コードは、機能を選択するために使用された、コンピューター グループを検索する保存済みクエリの形式が正しくないことを意味します。 

#### <a name="cause"></a>原因

クエリが変更されたか、システムによってそれが変更されたことが考えられます。

#### <a name="resolution"></a>解決方法

その機能に対するクエリを削除してから、機能を再度有効にすれば、クエリが再度作成されます。 クエリはワークスペース内の、 **[保存された検索条件]** にあります。 クエリの名前は **MicrosoftDefaultComputerGroup** です。クエリのカテゴリは、関連付けられた機能の名前となります。 複数の機能が有効な場合は、**MicrosoftDefaultComputerGroup** クエリが複数回 **[保存された検索条件]** に表示されます。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>シナリオ:PolicyViolation

#### <a name="issue"></a>問題

このエラー コードは、1 つ以上のポリシーの違反が原因でデプロイが失敗したことを示します。

#### <a name="cause"></a>原因 

ポリシーによって操作の完了が阻止されています。

#### <a name="resolution"></a>解決方法

機能を正常にデプロイするには、指定されたポリシーの変更を検討する必要があります。 定義可能なポリシーが多数あるため、必要になる変更は、違反しているポリシーによって異なります。 たとえば、リソース グループに対して、含まれている一部のリソースの内容を変更するためのアクセス許可を拒否するポリシーが定義されている場合は、次のいずれかの修正を選択できます。

* ポリシーを完全に削除する
* 別のリソース グループに対する機能の有効化を試す。
* Automation アカウントなどの特定のリソースに対してポリシーを再ターゲットする。
* 拒否するようにポリシーが構成されているリソースのセットを変更する。

Azure portal の右上にある通知を確認するか、Automation アカウントを含むリソース グループに移動し、 **[設定]** の **[デプロイ]** を選択して、失敗したデプロイを表示します。 Azure Policy の詳細については、[Azure Policy の概要](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)に関するページを参照してください。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>シナリオ:ワークスペースのリンクを解除しようとすると発生するエラー

#### <a name="issue"></a>問題

ワークスペースのリンクを解除しようとすると、次のエラー メッセージが届きます。

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

このエラーは、Log Analytics ワークス ペース内に Automation アカウントに依存するアクティブな機能がまだ存在し、Log Analytics ワークスペースがリンクされている場合に発生します。

### <a name="resolution"></a>解決方法

使用している場合は、次の機能のリソースをワークスペースから削除します。

* 更新管理
* 変更履歴とインベントリ
* 勤務時間外に VM を起動/停止する

機能のリソースを削除すると、ワークスペースのリンクを解除できます。 ご利用のワークスペースおよび Automation アカウントから、これらの機能の既存の成果物をクリーンアップすることが重要です。

* Update Management の場合は、Automation アカウントから、**更新プログラムのデプロイ (スケジュール)** を削除します。
* Start/Stop VMs during off-hours の場合、 **[設定]**  >  **[ロック]** で、Automation アカウントの機能コンポーネントに対するロックを解除します。 詳細については、「[機能を削除する](../automation-solution-vm-management-remove.md)」を参照してください。

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 拡張機能用の Log Analytics のエラー

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Windows 拡張機能用の Log Analytics エージェントのインストールは、さまざまな理由で失敗する可能性があります。 次のセクションでは、Windows 拡張機能用の Log Analytics エージェントのデプロイ中にエラーを引き起こす可能性がある機能のデプロイに関する問題について説明します。

>[!NOTE]
>Windows 用 Log Analytics エージェントは、Microsoft Monitoring Agent (MMA) の Azure Automation で現在使用されている名前です。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>シナリオ:WebClient 要求で例外が発生した

VM 上の Windows 拡張機能用の Log Analytics が外部リソースと通信できず、デプロイが失敗します。

#### <a name="issue"></a>問題

返されるエラー メッセージの例を次に示します。

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

このエラーの原因として考えられるものは次のとおりです。

* VM 内で構成されているプロキシでは、特定のポートのみ許可されます。
* ファイアウォールの設定によって、必要なポートとアドレスへのアクセスがブロックされた。

#### <a name="resolution"></a>解決方法

適切なポートとアドレスが通信のために開いていることを確認します。 ポートとアドレスの一覧は、[ネットワークの計画](../automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>シナリオ:環境の一時的な問題のためにインストールが失敗した

Windows 拡張機能用の Log Analytics のインストールは、デプロイ時に別のインストールまたは操作によって妨げられるため、失敗しました。

#### <a name="issue"></a>問題

返されるエラー メッセージの例を次に示します。

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

このエラーの原因として考えられるものは次のとおりです。

* 別のインストールが進行中です。
* テンプレートのデプロイ中にシステムの再起動がトリガーされます。

#### <a name="resolution"></a>解決方法

このエラーは本質的に一時的なものです。 デプロイを再試行して拡張機能をインストールしてください。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>シナリオ:インストールのタイムアウト

Windows 拡張機能用の Log Analytics のインストールがタイムアウトのために完了しませんでした。

#### <a name="issue"></a>問題

返される可能性のあるエラー メッセージの例を次に示します。

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

この種のエラーは、インストール中に VM の負荷が大きくなったために発生します。

### <a name="resolution"></a>解決方法

VM の負荷が小さい場合に、Windows 拡張機能用の Log Analytics エージェントをインストールしてみてください。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。