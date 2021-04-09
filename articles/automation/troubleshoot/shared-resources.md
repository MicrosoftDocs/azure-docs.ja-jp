---
title: Azure Automation 共有リソースの問題のトラブルシューティング
description: この記事では、Azure Automation 共有リソースに関する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99053621"
---
# <a name="troubleshoot-shared-resource-issues"></a>共有リソースの問題のトラブルシューティング

この記事では、Azure Automation で[共有リソース](../automation-intro.md#shared-resources)を使用するときに発生する可能性がある問題について説明します。

## <a name="modules"></a>モジュール

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>シナリオ:インポート中にモジュールが停止する

#### <a name="issue"></a>問題

Azure Automation モジュールをインポートまたは更新しているときに、モジュールが *[インポート中]* の状態で停止します。

#### <a name="cause"></a>原因

PowerShell モジュールのインポートは複雑なマルチステップ プロセスであるため、モジュールが正しくインポートされず、一時的な状態で停止することがあります。 このインポート プロセスの詳細については、「[PowerShell モジュールをインポートする](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)」を参照してください。

#### <a name="resolution"></a>解像度

この問題を解決するには、[Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) コマンドレットを使用して、停止しているモジュールを削除する必要があります。 その後、モジュールのインポートを再試行できます。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>シナリオ:更新の試行後、インポート中に AzureRM モジュールが停止する

#### <a name="issue"></a>問題

AzureRM モジュールの更新を試行した後、アカウントに以下のメッセージのバナーが残ります。

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

Automation アカウントでの AzureRM モジュールの更新には、既知の問題があります。 具体的には、0 から始まる数値名を持つリソース グループにモジュールが含まれている場合に問題が発生します。

#### <a name="resolution"></a>解像度

Automation アカウントで AzureRM モジュールを更新するには、英数字名を持つリソース グループにアカウントが含まれている必要があります。 0 から始まる数値名を持つリソース グループは、現時点で AzureRM モジュールを更新できません。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>シナリオ:モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない

#### <a name="issue"></a>問題

モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

#### <a name="cause"></a>原因

モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。

* その構造が、Automation で必要な構造と一致しません。
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。
* モジュールのフォルダーにその依存関係がありません。
* モジュールのアップロードに [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) コマンドレットを使用していますが、完全なストレージ パスを指定していないか、パブリックにアクセスできる URL でモジュールを読み込んでいません。

#### <a name="resolution"></a>解像度

この問題を修正するには、次の解決策のいずれかを使用します。

* モジュールが次の形式に従っていることを確認します。ModuleName.zip -> ModuleName またはバージョン番号 -> (ModuleName.psm1、ModuleName.psd1)。
* **.psd1** ファイルを開き、モジュールに依存関係があるかどうかを確認します。 依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。
* 参照される **.dll** ファイルがモジュール フォルダーにあることを確認します。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>シナリオ:モジュールの更新中に Update-AzureModule.ps1 が中断する

#### <a name="issue"></a>問題

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を使用して Azure モジュールを更新しているときに、モジュールの更新プロセスが中断されます。

#### <a name="cause"></a>原因

この Runbook の場合、同時に更新されるモジュール数を決定する既定値は 10 です。 更新プロセスは、同時に更新されるモジュールが多すぎると、エラーが発生しやすくなります。

#### <a name="resolution"></a>解像度

同じ Automation アカウントですべての AzureRM または Az モジュールが必要になることは、通常はありません。 必要な特定のモジュールのみをインポートする必要があります。

> [!NOTE]
> `Az.Automation` または `AzureRM.Automation` モジュール全体をインポートしないようにしてください。これにより、含まれているすべてのモジュールがインポートされます。

更新プロセスが中断する場合は、`SimultaneousModuleImportJobCount` パラメーターを **Update-AzureModules.ps1** スクリプトに追加し、既定値の 10 より小さい値を指定してください。 このロジックを実装する場合、3 または 5 の値から始めてください。 `SimultaneousModuleImportJobCount` は、Azure モジュールの更新に使用される **Update-AutomationAzureModulesForAccount** システム Runbook のパラメーターです。 この調整を行うと、更新プロセスの実行時間は長くなりますが、完了する可能性が高くなります。 次の例に、パラメーターと Runbook のそれを配置する場所を示します。

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>実行アカウント

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>シナリオ:実行アカウントを作成または更新できない

#### <a name="issue"></a>問題

実行アカウントを作成または更新しようとすると、次のようなエラーが表示されます。

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

実行アカウントを作成または更新するために必要なアクセス許可がないか、リソースがリソース グループ レベルでロックされています。

#### <a name="resolution"></a>解像度

実行アカウントを作成または更新するには、実行アカウントで使用するさまざまなリソースに対する適切な[アクセス許可](../automation-security-overview.md#permissions)が必要です。

問題の原因がロックである場合は、ロックを削除しても構わないか確認します。 次に、Azure portal でロックされているリソースに移動し、ロックを右クリックして、 **[削除]** を選択します。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>シナリオ:Runbook の実行時に "DLL 'iplpapi.dll' の 'GetPerAdapterInfo' というエントリ ポイントが見つかりません" というエラーが表示される

#### <a name="issue"></a>問題

Runbook を実行すると、以下の例外を受け取ります。

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

このエラーは、正しく構成されていない [[アカウントとして実行]](../automation-security-overview.md) によって発生する可能性があります。

#### <a name="resolution"></a>解像度

実行アカウントが正しく構成されていることを確認します。 次に、Azure で認証するための適切なコードが Runbook にあることを確認します。 次のサンプルは、[アカウントとして実行] を使用して Runbook で Azure を認証するコードのスニペットを示しています。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>次のステップ

この記事で問題を解決できない場合は、追加のサポートを得るために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 これは、Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐための、Microsoft Azure の公式アカウントです。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。