---
title: Azure Automation Desired State Configuration (DSC) の問題をトラブルシューティングする
description: この記事では、Desired State Configuration (DSC) のトラブルシューティングに関する情報を説明します。
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8afa671a323e37a99be8b5a43d0a4823fe1877a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58800878"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) をトラブルシューティングする

この記事では、Desired State Configuration (DSC) の問題のトラブルシューティングに関する情報を説明します。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) の使用時に発生する一般的なエラー

### <a name="unsupported-characters"></a>シナリオ:特殊文字を含む構成を、ポータルから削除できません。

#### <a name="issue"></a>問題

ポータルから DSC 構成を削除しようとすると、次のエラーが表示されます。

```error
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

このエラーは、一時的な問題であり、解決される予定です。

#### <a name="resolution"></a>解決策

* "Remove-AzAutomationDscConfiguration" Az コマンドレットを使用して、構成を削除します。
* このコマンドレットのドキュメントは、まだ更新されていません。  それまでは、AzureRM モジュールのドキュメントを参照してください。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

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

  * Azure Portal を使用してノードにノード構成を割り当てるには、**[DSC ノード]** ページを開き、ノードを選択し、**[ノード構成の割り当て]** ボタンをクリックします。  
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

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
