---
title: Azure Automation 共有リソースのエラーをトラブルシューティングする
description: Azure Automation 共有リソースのエラーをトラブルシューティングする方法を説明します
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9313b042433489307a2bd2822a96d1e0e127362b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849294"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>共有リソースのエラーをトラブルシューティングする

この記事では、Azure Automation の共有リソースの使用時に発生する可能性がある問題の解決策について説明します。

## <a name="modules"></a>モジュール

### <a name="module-stuck-importing"></a>シナリオ:モジュールでインポートが停止する

#### <a name="issue"></a>問題

Azure Automation でモジュールのインポートまたは更新を行うと、モジュールが**インポート**状態で停止します。

#### <a name="cause"></a>原因

PowerShell モジュールのインポートは、複雑な複数手順のプロセスです。 このプロセスでは、モジュールが正しくインポートされない可能性があります。 この問題が発生すると、インポートしているモジュールが一時的な状態で停止することがあります。 このプロセスの詳細については、[PowerShell モジュールのインポート](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)に関する記事を参照してください。

#### <a name="resolution"></a>解決策

この問題を解決するには、[Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) コマンドレットを使用して、**インポート**状態で停止しているモジュールを削除する必要があります。 その後、モジュールのインポートを再試行できます。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>シナリオ:AzureRM モジュールは、これらの更新の試行後にインポートするスタックです。

#### <a name="issue"></a>問題

AzureRM モジュールの更新を試行した後、アカウントに以下のメッセージのバナーが残ります。

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

0 から始まる数値名を持つリソース グループにある、Automation Account の AzureRM モジュールの更新については、既知の問題があります。

#### <a name="resolution"></a>解決策

Automation Account で Azure モジュールを更新するには、それが英数字名を持つリソース グループになければなりません。 0 から始まる数値名を持つリソース グループは、現時点で AzureRM モジュールを更新できません。

### <a name="module-fails-to-import"></a>シナリオ:モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない

#### <a name="issue"></a>問題

モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

#### <a name="cause"></a>原因

モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。

* 構造が Automation で必要とされる構造と一致しません。
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。
* モジュールのフォルダーにその依存関係がありません。
* モジュールのアップロードに `New-AzureRmAutomationModule` コマンドレットを使用していますが、完全なストレージ パスを指定していないか、パブリックにアクセスできる URL でモジュールを読み込んでいません。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* モジュールの形式が次のようになっていることを確認します。ModuleName.Zip **->** モジュール名またはバージョン番号 **->** (ModuleName.psm1、ModuleName.psd1)
* .psd1 ファイルを開き、モジュールに依存関係があるかどうかを確認します。 依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。
* 参照される .dll がモジュール フォルダーにあることを確認します。

### <a name="all-modules-suspended"></a>シナリオ:モジュールの更新中に Update-AzureModule.ps1 が中断する

#### <a name="issue"></a>問題

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を使用して、Azure モジュールを更新しているときに、モジュール更新プロセスが中断します。

#### <a name="cause"></a>原因

`Update-AzureModule.ps1` スクリプトを使用している場合、同時に更新されるモジュール数を決定する既定値は 10 です。 更新プロセスは、同時に更新されるモジュールが多すぎると、エラーが発生しやすくなります。

#### <a name="resolution"></a>解決策

同じ Automation アカウントですべての AzureRM モジュールが必要になることは、あまりありません。 必要な AzureRM モジュールのみをインポートすることをお勧めします。

> [!NOTE]
> **AzureRM** モジュールをインポートしないようにします。 **AzureRM** モジュールをインポートすると、すべての **AzureRM\*** モジュールがインポートされることになり、これは推奨されません。

更新プロセスが中断する場合、`Update-AzureModules.ps1` スクリプトに `SimultaneousModuleImportJobCount` パラメーターを追加し、既定値の 10 より小さい値を指定する必要があります。 このロジックを実装する場合、3 または 5 の値から始めることをお勧めします。 `SimultaneousModuleImportJobCount` は、Azure モジュールの更新に使用される `Update-AutomationAzureModulesForAccount` システム Runbook のパラメーターです。 この変更により、プロセスの実行時間が長くなりますが、完了する可能性が高くなります。 次の例に、パラメーターと Runbook のそれを配置する場所を示します。

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

### <a name="unable-create-update"></a>シナリオ:実行アカウントを作成または更新できない

#### <a name="issue"></a>問題

実行アカウントを作成または更新しようとすると、次のエラー メッセージのようなエラーが表示されます。

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

実行アカウントを作成または更新するために必要なアクセス許可がないか、リソースがリソース グループ レベルでロックされています。

#### <a name="resolution"></a>解決策

実行アカウントを作成または更新するには、実行アカウントで使用するさまざまなリソースに対する適切なアクセス許可が必要です。 実行アカウントの作成または更新に必要なアクセス許可については、[実行アカウントのアクセス許可](../manage-runas-account.md#permissions)に関する記事を参照してください。

問題の原因がロックである場合、ロックを解除しても構わないか確認します。 次に、ロックされているリソースに移動し、ロックを右クリックして、 **[削除]** を選択して、ロックを解除します。

### <a name="iphelper"></a>シナリオ:Runbook の実行時に「DLL 'iplpapi.dll' の 'GetPerAdapterInfo' というエントリ ポイントが見つかりません」というエラーが表示されます。

#### <a name="issue"></a>問題

Runbook を実行すると、以下の例外を受け取ります。

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

このエラーは、正しく構成されていない [[アカウントとして実行]](../manage-runas-account.md) によって発生する可能性があります。

#### <a name="resolution"></a>解決策

[[アカウントとして実行]](../manage-runas-account.md) が正しく構成されていることを確認します。 正しく構成されていれば、Runbook に Azure を認証するための適切なコードがあることを確認します。 次のサンプルは、[アカウントとして実行] を使用して Runbook で Azure を認証するコードのスニペットを示しています。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
