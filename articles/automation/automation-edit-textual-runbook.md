---
title: Azure Automation でのテキスト形式の Runbook の編集
description: この記事では、Azure Automation テキスト エディターを使用して PowerShell と PowerShell Workflow Runbook を操作する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d6b786ffaf309e147de27e8cd8be314a3d8a5fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896988"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Azure Automation でのテキスト形式の Runbook の編集

Azure Automation のテキスト エディターを使用して、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) および [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を編集できます。 このエディターは、Intellisense などの他のコード エディターの一般的な機能を備えています。 また、Runbook に共通するリソースへのアクセスを補助するための特別な追加機能を備えた色の設定を使用します。 

このテキスト エディターには、コマンドレット、資産、および子 Runbook のコードを Runbook に挿入するための機能が含まれます。 コードを自分で入力する代わりに、使用可能なリソースの一覧から選択すると、エディターによって適切なコードが Runbook に挿入されます。

Azure Automation の各 Runbook には、ドラフトと発行の 2 つのバージョンがあります。 Runbook のドラフト バージョンを編集し、実行できるように発行します。 発行されたバージョンを編集することはできません。 詳細については、「[Runbook の発行](manage-runbooks.md#publish-a-runbook)」を参照してください。

この記事では、このエディターのさまざまな機能を実行する詳細な手順を説明します。 これらは、[グラフィカル runbook](automation-runbook-types.md#graphical-runbooks)には適用されません。 これらの Runbook を使用する場合は、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。

## <a name="edit-a-runbook-with-the-azure-portal"></a>Azure portal で Runbook を編集する

1. Azure portal で、お使いの Automation アカウントを選択します。
2. **[プロセス オートメーション]** の **[Runbook]** を選択して、Runbook の一覧を開きます。
3. 編集する Runbook を選択し、 **[編集]** をクリックします。
4. Runbook を編集します。
5. 編集が完了したら、 **[保存]** をクリックします。
6. 最新のドラフト バージョンの Runbook を発行する場合は、 **[発行]** をクリックします。

### <a name="insert-a-cmdlet-into-a-runbook"></a>コマンドレットを Runbook に挿入する

1. テキスト エディターのキャンバスで、コマンドレットを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[コマンドレット]** ノードを展開します。
3. 使用するコマンドレットを含むモジュールを展開します。
4. 挿入するコマンドレット名を右クリックして、 **[キャンバスに追加]** を選択します。 コマンドレットに複数のパラメーター セットがある場合は、エディターによって既定のセットが追加されます。 コマンドレットを展開して別のパラメーター セットを選択することもできます。
5. コマンドレットのコードがパラメーターの一覧全体と共に挿入されます。
6. 必須パラメーターについて、山括弧 (<>) で囲まれている値の代わりに適切な値を入力します。 不要なパラメーターを削除します。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>子 Runbook のコードを Runbook に挿入する

1. テキスト エディターのキャンバスで、 [子 Runbook](automation-child-runbooks.md) のコードを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[Runbook]** ノードを展開します。
3. 挿入する Runbook を右クリックして、 **[キャンバスに追加]** を選択します。
4. 子 Runbook のコードがすべての Runbook パラメーターのプレースホルダーと共に挿入されます。
5. 各パラメーターのプレースホルダーを適切な値に置き換えます。

### <a name="insert-an-asset-into-a-runbook"></a>資産を Runbook に挿入する

1. テキスト エディターのキャンバス コントロールで、子 Runbook のコードを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[アセット]** ノードを展開します。
3. 必要な資産の種類のノードを展開します。
4. 挿入する資産名を右クリックして、 **[キャンバスに追加]** を選択します。 [変数資産](./shared-resources/variables.md)の場合、変数を取得するのか設定するのかに応じて、 **["変数の取得" をキャンバスに追加する]** または **["変数の設定" をキャンバスに追加する]** を選択します。
5. 資産のコードが Runbook に挿入されます。

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Azure Automation の Runbook を編集する

Windows PowerShell を使用して Runbook を編集するには、任意のエディターを使用して、Runbook を " **.ps1**" ファイルに保存します。 [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) コマンドレットを使用して、Runbook の内容を取得できます。 [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) コマンドレットを使用して、既存のドラフト Runbook を、変更済みのものに置き換えることができます。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Runbook の内容を取得する

次のサンプル コマンドでは、Runbook のスクリプトを取得し、スクリプト ファイルに保存する方法を示します。 この例では、ドラフト バージョンを取得します。 Runbook の発行済みバージョンを取得することもできますが、このバージョンを変更することはできません。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Runbook の内容を変更する

次のサンプル コマンドでは、Runbook の既存の内容を、スクリプト ファイルの内容に置き換える方法を示します。 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

* [Azure Automation で Runbook を管理する](manage-runbooks.md)。
* [PowerShell ワークフローについての説明](automation-powershell-workflow.md)。
* [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)。
* [証明書](./shared-resources/certificates.md)。
* [接続](automation-connections.md)。
* [資格情報](./shared-resources/credentials.md)。
* [スケジュール](./shared-resources/schedules.md)。
* [変数](./shared-resources/variables.md)。
* [PowerShell コマンドレット リファレンス](/powershell/module/az.automation)。
