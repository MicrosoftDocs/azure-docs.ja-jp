---
title: Azure Automation Desired State Configuration (DSC) の問題をトラブルシューティングする
description: この記事では、Desired State Configuration (DSC) のトラブルシューティングに関する情報を説明します。
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53fef426c927c690a3b697055f467f6cd35c532c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477516"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) をトラブルシューティングする

この記事では、Desired State Configuration (DSC) の問題のトラブルシューティングに関する情報を説明します。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) の使用時に発生する一般的なエラー

### <a name="unsupported-characters"></a>シナリオ:特殊文字を含む構成を、ポータルから削除できません。

#### <a name="issue"></a>問題

ポータルから DSC 構成を削除しようとすると、次のエラーが表示されます。

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

このエラーは、一時的な問題であり、解決される予定です。

#### <a name="resolution"></a>解決策

* "Remove-AzAutomationDscConfiguration" Az コマンドレットを使用して、構成を削除します。
* このコマンドレットのドキュメントは、まだ更新されていません。  それまでは、AzureRM モジュールのドキュメントを参照してください。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>シナリオ:Dsc エージェントの登録に失敗しました

#### <a name="issue"></a>問題

`Set-DscLocalConfigurationManager` または別の DSC コマンドレットを実行しようとすると、次のようなエラーが表示されます。

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

#### <a name="cause"></a>原因

このエラーは、通常、ファイアウォール、プロキシ サーバーの背後にあるマシン、またはその他のネットワーク エラーが原因で発生します。

#### <a name="resolution"></a>解決策

マシンが Azure Automation DSC の適切なエンドポイントへのアクセス権を持つことを確認し、もう一度やり直してください。 必要なポートとアドレスの一覧については、[ネットワークの計画](../automation-dsc-overview.md#network-planning)に関する記事を参照してください

### <a name="failed-not-found"></a>シナリオ:ノードが失敗状態になり、「見つかりません」というエラーが表示される

#### <a name="issue"></a>問題

ノードのレポートに **[失敗]** ステータスと次のエラーが含まれます。

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

通常、このエラーは、ノードがノード構成の名前 (ABC.WebServer など) ではなく構成名 (ABC など) に割り当てられている場合に発生します。

#### <a name="resolution"></a>解決策

* ノードに "構成名" ではなく、"ノード構成名" が割り当てられていることを確認してください。
* ノード構成は、Azure ポータルまたは PowerShell コマンドレットを使用してノードに割り当てることができます。

  * Azure Portal を使用してノードにノード構成を割り当てるには、 **[DSC ノード]** ページを開き、ノードを選択し、 **[ノード構成の割り当て]** ボタンをクリックします。  
  * PowerShell コマンドレットを使用してノードにノード構成を割り当てるには、**Set-AzureRmAutomationDscNode** コマンドレットを使用します。

### <a name="no-mof-files"></a>シナリオ:構成のコンパイルを実行しても、ノード構成 (MOF ファイル) が生成されなかった

#### <a name="issue"></a>問題

DSC コンパイル ジョブが次のエラーで中断します。

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

DSC 構成の **Node** キーワードに続く式の評価結果が `$null` の場合、ノード構成は生成されません。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* 構成定義内の **Node** キーワードに続く式の評価結果が $null になっていないことを確認します。
* 構成のコンパイル時に ConfigurationData を渡す場合は、 [ConfigurationData](../automation-dsc-compile.md#configurationdata)から、構成に必要な期待値を渡すようにしてください。

### <a name="dsc-in-progress"></a>シナリオ:DSC ノードのレポートが "処理中" の状態で停止する

#### <a name="issue"></a>問題

DSC エージェントによって次のように出力されます。

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

WMF のバージョンをアップグレードした結果、WMI が破損しています。

#### <a name="resolution"></a>解決策

この問題を解決するには、「[Desired State Configuration (DSC) の既知の問題と制限事項](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)」の記事にある手順に従ってください。

### <a name="issue-using-credential"></a>シナリオ:DSC 構成で資格情報が使用できない

#### <a name="issue"></a>問題

DSC コンパイル ジョブが次のエラーで中断されました。

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

構成に資格情報を使用したが、ノード構成ごとに **PSDscAllowPlainTextPassword** を true に設定するための適切な **ConfigurationData** を指定していませんでした。

#### <a name="resolution"></a>解決策

* 上記の構成の各ノード構成について **PSDscAllowPlainTextPassword** を true に設定するために、適切な **ConfigurationData** を渡してください。 詳細については、[Azure Automation DSC の資産](../automation-dsc-compile.md#assets)に関するページをご覧ください。

### <a name="failure-processing-extension"></a>シナリオ:DSC 拡張機能からのオンボード、"エラーの処理拡張機能" のエラー

#### <a name="issue"></a>問題

DSC 拡張機能を使用してオンボードするときに、下記を含むエラーが発生する

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

通常、このエラーは、サービスに存在しないノード構成名がノードに割り当てられたときに発生します。

#### <a name="resolution"></a>解決策

* ノードに割り当てるノード構成名が、サービスに存在するものと正確に一致していることを確認します。
* ノード構成名を含めないようにすることもできます。この場合、ノードはオンボードされますが、ノード構成は割り当てられません。

### <a name="failure-linux-temp-noexec"></a>シナリオ:Linux で構成を適用するときに、一般的なエラーで障害が発生する

#### <a name="issue"></a>問題

Linux で構成を適用するときに、次のエラーを含む障害が発生します。

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

/tmp の場所が noexec に設定されている場合に、現在のバージョンの DSC では構成の適用に失敗することを、顧客は特定しています。

#### <a name="resolution"></a>解決策

* /tmp の場所から noexec オプションを削除します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
