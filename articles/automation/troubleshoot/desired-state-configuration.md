---
title: Azure Automation State Configuration の問題のトラブルシューティング
description: この記事では、Azure Automation State Configuration に関する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896530"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Azure Automation State Configuration の問題のトラブルシューティング

この記事では、Azure Automation State Configuration で構成のコンパイルまたはデプロイを行っているときに発生する問題のトラブルシューティングと解決方法について説明します。 State Configuration の機能の一般的な情報については、[Azure Automation State Configuration の概要](../automation-dsc-overview.md)に関するページをご覧ください。

## <a name="diagnose-an-issue"></a>問題の診断

構成に対してコンパイル エラーまたはデプロイ エラーが発生した場合は、次の手順に従って問題を診断してください。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1.ローカル コンピューター上で構成が正常にコンパイルされていることを確認する

Azure Automation State Configuration は、PowerShell Desired State Configuration (DSC) に基づいて構築されています。 DSC の言語と構文については、[PowerShell DSC のドキュメント](/powershell/scripting/overview)を参照してください。

ご利用のローカル コンピューター上で DSC の構成をコンパイルすると、次のような一般的なエラーを検出して解決できます。

   - モジュールの不足。
   - 構文エラー。
   - 論理エラー。

### <a name="2-view-dsc-logs-on-your-node"></a>2.ノード上で DSC ログを表示する

構成のコンパイルは正常に行われても、ノードに適用されるときに失敗する場合は、DSC ログで詳細情報を確認できます。 これらのログを探す場所については、「[DSC イベント ログの場所](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)」を参照してください。

[xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) モジュールは、DSC ログからの詳細情報を解析するのに役立つ可能性があります。 サポートに問い合わせる場合、ご自分の問題を診断するために、これらのログが必要になります。

[安定バージョンのモジュールのインストール](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)に関するページにある手順に従って、ローカル コンピューター上に `xDscDiagnostics` モジュールをインストールできます。

ご利用の Azure のコンピューター上に `xDscDiagnostics` モジュールをインストールするには、[Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) を使用します。 また、「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](../../virtual-machines/windows/run-command.md)」の手順に従って、Azure portal で **[実行コマンド]** オプションを使用することもできます。

**xDscDiagnostics** の使用方法については、「[xDscDiagnostics を使用した DSC ログの分析](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)」を参照してください。 また、[xDscDiagnostics コマンドレット](https://github.com/PowerShell/xDscDiagnostics#cmdlets)に関するページも参照してください。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3.ノードと Automation ワークスペースに必要なモジュールがあることを確認する

DSC は、ノード上にインストールされているモジュールに依存します。 Azure Automation State Configuration を使用するときは、「[モジュールのインポート](../shared-resources/modules.md#import-modules)」の手順に従って、必要なモジュールを Automation アカウントにインポートします。 構成が特定のバージョンのモジュールに依存することもあります。 詳細については、[モジュールのトラブルシューティング](shared-resources.md#modules)に関するページを参照してください。

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>シナリオ:特殊文字が使われている構成をポータルから削除できない

### <a name="issue"></a>問題

ポータルから DSC 構成を削除しようとすると、次のエラーが表示されます。

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

このエラーは、一時的な問題であり、解決される予定です。

### <a name="resolution"></a>解決方法

[Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) コマンドレットを使用して、構成を削除します。

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>シナリオ:DSC エージェントを登録できなかった

### <a name="issue"></a>問題

[Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) または別の DSC コマンドレットの場合に、次のエラーが返されます。

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>原因

このエラーは、通常、ファイアウォール、プロキシ サーバーの背後にあるマシン、またはその他のネットワーク エラーが原因で発生します。

### <a name="resolution"></a>解決方法

DSC の適切なエンドポイントへのアクセス権がご利用のマシンにあることを確認し、もう一度やり直してください。 必要なポートとアドレスの一覧については、[ネットワークの計画](../automation-dsc-overview.md#network-planning)に関するページを参照してください。

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>シナリオ:状態レポートから応答コード "承認されていません" が返される

### <a name="issue"></a>問題

Azure Automation State Configuration にノードを登録すると、次のいずれかのエラー メッセージが表示されます。

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

この問題は、証明書が正しくないまたは期限切れになっていることが原因で発生します。 「[ノードを再登録する](../automation-dsc-onboarding.md#re-register-a-node)」を参照してください。

この問題は、* **.azure-automation.net** へのアクセスを許可しないプロキシ構成が原因である場合もあります。 詳細については、「[プライベート ネットワークの構成](../automation-dsc-overview.md#network-planning)」を参照してください。 

### <a name="resolution"></a>解決方法

失敗した DSC ノードを再登録するには、次の手順に従います。

#### <a name="step-1-unregister-the-node"></a>手順 1:ノードの登録を解除する

1. Azure portal で、 **[ホーム]**  >  **[Automation アカウント]** > (自分の Automation アカウント) > **[状態の構成 (DSC)]** の順に移動します。
1. **[ノード]** を選択し、問題が発生しているノードを選択します。
1. ノードの登録を解除するために、 **[登録解除]** を選択します。

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>手順 2:ノードから DSC 拡張機能をアンインストールする

1. Azure portal で、 **[ホーム]**  >  **[仮想マシン]** > (失敗したノード) > **[拡張機能]** の順に移動します。
1. PowerShell DSC 拡張機能である **[Microsoft.Powershell.DSC]** を選択します。
1. 拡張機能をアンインストールするために、 **[アンインストール]** を選択します。

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>手順 3:正しくないまたは期限切れになっている証明書をすべてノードから削除する

管理者特権の PowerShell プロンプトから、失敗したノード上で次のコマンドを実行します。

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>手順 4:失敗したノードを再登録する

1. Azure portal で、 **[ホーム]**  >  **[Automation アカウント]** > (自分の Automation アカウント) > **[状態の構成 (DSC)]** の順に移動します。
1. **[ノード]** を選択します。
1. **[追加]** を選択します。
1. 失敗したノードを選択します。
1. **[接続]** を選択し、必要なオプションを選択します。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>シナリオ:ノードが失敗状態になり、「見つかりません」というエラーが表示される

### <a name="issue"></a>問題

ノードに失敗状態のレポートがあり、次のエラーが含まれています。

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

通常、このエラーは、ノードがノード構成 (MOF ファイル) の名前 (**ABC.WebServer** など) ではなく構成名 (**ABC** など) に割り当てられている場合に発生します。

### <a name="resolution"></a>解決方法

* ノードに構成名ではなく、ノード構成名が割り当てられていることを確認してください。
* ノード構成は、Azure portal または PowerShell コマンドレットを使用してノードに割り当てることができます。

  * Azure portal で、 **[ホーム]**  >  **[Automation アカウント]** > (自分の Automation アカウント) > **[状態の構成 (DSC)]** の順に移動します。 次に、ノードを選択し、 **[ノード構成の割り当て]** を選択します。
  * [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) コマンドレットを使用します。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>シナリオ: 構成のコンパイル時に、ノード構成 (MOF ファイル) が生成されなかった

### <a name="issue"></a>問題

DSC コンパイル ジョブが次のエラーで中断します。

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

DSC 構成の `Node` キーワードに続く式の評価結果が `$null` の場合、ノード構成は生成されません。

### <a name="resolution"></a>解決方法

問題を解決するには、次のいずれかの解決策を使用します。

* 構成定義内の `Node` キーワードに続く式の評価結果が Null になっていないことを確認します。
* 構成のコンパイル時に [ConfigurationData](../automation-dsc-compile.md) を渡す場合は、構成のために必要な構成データの値を渡すようにしてください。

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>シナリオ: DSC ノードのレポートが "処理中" の状態で停止する

### <a name="issue"></a>問題

DSC エージェントによって次のように出力されます。

```error
No instance found with given property values
```

### <a name="cause"></a>原因

ご利用の Windows Management Framework (WMF) バージョンをアップグレードしましたが、Windows Management Instrumentation (WMI) が破損しています。

### <a name="resolution"></a>解決方法

[DSC の既知の問題と制限事項](/powershell/scripting/wmf/known-issues/known-issues-dsc)に関するページに示された手順に従ってください。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>シナリオ:DSC 構成で資格情報が使用できない

### <a name="issue"></a>問題

DSC コンパイル ジョブが次のエラーで中断されました。

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

構成に資格情報を使用したが、ノード構成ごとに `PSDscAllowPlainTextPassword` を true に設定するための適切な `ConfigurationData` を指定していませんでした。

### <a name="resolution"></a>解決方法

構成に示されている各ノード構成の `PSDscAllowPlainTextPassword` を true に設定するために、適切な `ConfigurationData` を渡してください。 [Azure Automation State Configuration での DSC 構成のコンパイル](../automation-dsc-compile.md)に関するページを参照してください。

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>シナリオ:DSC 拡張機能からマシンを有効にする時に "拡張機能の処理エラー" というエラーが発生する

### <a name="issue"></a>問題

DSC 拡張機能を使用してマシンを有効にするときに、次のエラーを含む障害が発生します。

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

通常、このエラーは、サービスに存在しないノード構成名がノードに割り当てられたときに発生します。

### <a name="resolution"></a>解決方法

* ノードに割り当てる名前が、サービスに存在する名前と正確に一致していることを確認します。
* ノード構成名を含めないようにすることもできます。この場合、ノードは有効化されますが、ノード構成は割り当てられません。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>シナリオ:PowerShell を使用してノードを登録すると "1 つ以上のエラーが発生しました" というエラーが発生する

### <a name="issue"></a>問題

[Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) または [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) を使用してノードを登録すると、次のエラーが表示されます。

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

このエラーは、Automation アカウントで使用されているものとは別のサブスクリプションにノードを登録しようとしたときに発生します。

### <a name="resolution"></a>解決方法

別のクラウドまたはオンプレミスに対して定義されているかのように、クロスサブスクリプション ノードを扱います。 マシンを有効化するための次のいずれかのオプションを使用してノードを登録します。

* Windows: [オンプレミスの、あるいは Azure と AWS 以外のクラウド上の物理または仮想 Windows マシン](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)。
* Linux: [オンプレミスの、あるいは Azure 以外のクラウド上の物理または仮想 Linux マシン](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)。

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>シナリオ:"プロビジョニングに失敗しました" というエラー メッセージ

### <a name="issue"></a>問題

ノードの登録時に、以下のエラーが表示されます。

```error
Provisioning has failed
```

### <a name="cause"></a>原因

ノードと Azure 間の接続に問題がある場合、このメッセージが表示されます。

### <a name="resolution"></a>解決方法

ご利用のノードが仮想プライベート ネットワーク (VPN) 内にあるのか、または Azure への接続に関する他の問題を抱えているかを判断します。 [「機能のデプロイに関する問題のトラブルシューティング」](onboarding.md)を参照してください。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>シナリオ:Linux で構成を適用するときに、一般的なエラーで障害が発生する

### <a name="issue"></a>問題

Linux で構成を適用するときに、次のエラーを含む障害が発生します。

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

**/tmp** の場所が `noexec` に設定されていると、現在のバージョンの DSC は構成の適用に失敗します。

### <a name="resolution"></a>解決方法

**/tmp** の場所から `noexec` オプションを削除します。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>シナリオ:重複するノード構成名のために正しくないリリースになる可能性がある

### <a name="issue"></a>問題

1 つの構成スクリプトを使用して複数のノード構成を作成する場合、一部のノード構成名が他の名前の一部である場合、コンパイル サービスによって最終的に正しくない構成が割り当てられる場合があります。 この問題は、1 つのスクリプトを使用してノードごとに構成データがある構成を生成する場合に限られ、しかも名前の重複が文字列の先頭にある場合にのみ発生します。 たとえば、コマンドレットを使用し、ハッシュテーブルとして渡されたノード データに基づいて構成を生成するために 1 つの構成スクリプトを使用した場合、およびノード データに **server** と **1server** という名前のサーバーが含まれる場合です。

### <a name="cause"></a>原因

これはコンパイル サービスにおける既知の問題です。

### <a name="resolution"></a>解決方法

最善の回避策としては、ローカルで、または CI/CD パイプラインでコンパイルを行い、サービスにノード構成 MOF ファイルを直接アップロードします。 サービス内でコンパイルを行うことが必須である場合、最善の回避策として次に推奨されるのは、名前が重複しないように、コンパイル ジョブを分割することです。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>シナリオ:DSC 構成のアップロードでゲートウェイ タイムアウト エラーが発生した

#### <a name="issue"></a>問題

DSC 構成をアップロードするときに、`GatewayTimeout` エラーが発生します。 

### <a name="cause"></a>原因

コンパイルに長い時間がかかる DSC 構成では、このエラーが発生する可能性があります。

### <a name="resolution"></a>解決方法

任意の [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource) 呼び出しに対して `ModuleName` パラメーターを明示的に含めることで、DSC 構成をより速く解析することができます。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
