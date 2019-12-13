---
title: Azure Automation でのテキスト形式の Runbook の編集
description: この記事では、テキスト エディターを使用して Azure Automation で PowerShell Runbook および PowerShell ワークフロー Runbook の処理を行うためのさまざまな手順について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850858"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Azure Automation でのテキスト形式の Runbook の編集

Azure Automation のテキスト エディターを使用して、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) および [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を編集できます。 このエディターは、Intellisense や色の設定などの他のコード エディターの一般的な機能に加えて、Runbook に共通するリソースへのアクセスを補助するための特別な追加機能を備えています。 この記事では、このエディターのさまざまな機能を実行する詳細な手順を説明します。

このテキスト エディターには、コマンドレット、資産、および子 Runbook のコードを Runbook に挿入するための機能が含まれます。 コードを自分で入力しなくても、使用可能なリソースの一覧から選択して、適切なコードを Runbook に挿入できます。

Azure Automation の各 Runbook には、ドラフトと発行の 2 つのバージョンがあります。 Runbook のドラフト バージョンを編集し、実行できるように発行します。 発行されたバージョンを編集することはできません。 詳細については、「[Publish a runbook (Runbook の発行)](manage-runbooks.md#publish-a-runbook)」をご覧ください。

[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) 使用する場合は、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Azure ポータルで Runbook を編集するには

編集するためにテキスト エディターで Runbook を開くには、次の手順を使用します。

1. Azure ポータルで Automation アカウントを選択します。
2. **[プロセス オートメーション]** の **[Runbook]** を選択して、Runbook の一覧を開きます。
3. 編集する Runbook を選択して、 **[編集]** ボタンをクリックします。
4. Runbook を編集します。
5. 編集が完了したら、 **[保存]** をクリックします。
6. 最新のドラフト バージョンの Runbook を発行する場合は、 **[発行]** をクリックします。

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>コマンドレットを Runbook に挿入するには

1. テキスト エディターのキャンバスで、コマンドレットを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[コマンドレット]** ノードを展開します。
3. 使用するコマンドレットを含むモジュールを展開します。
4. 挿入するコマンドレットを右クリックして、 **[キャンバスに追加]** を選択します。 コマンドレットに複数のパラメーター セットがある場合は、既定のセットが追加されます。 コマンドレットを展開して別のパラメーター セットを選択することもできます。
5. コマンドレットのコードがパラメーターのリスト全体と共に挿入されます。
6. 必須パラメーターの山括弧 <> で囲まれているデータ型の代わりに適切な値を入力します。 必要のないパラメーターを削除します。

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>子 Runbook のコードを Runbook に挿入するには

1. テキスト エディターのキャンバスで、 [子 Runbook](automation-child-runbooks.md)のコードを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[Runbook]** ノードを展開します。
3. 挿入する Runbook を右クリックして、 **[キャンバスに追加]** を選択します。
4. 子 Runbook のコードがすべての Runbook パラメーターのプレースホルダーと共に挿入されます。
5. 各パラメーターのプレースホルダーを適切な値に置き換えます。

### <a name="to-insert-an-asset-into-a-runbook"></a>資産を Runbook に挿入するには

1. テキスト エディターのキャンバスで、子 Runbook のコードを配置する場所にカーソルを置きます。
2. ライブラリ コントロールで **[アセット]** ノードを展開します。
3. 必要な資産の種類のノードを展開します。
4. 挿入する資産を右クリックして、 **[キャンバスに追加]** を選択します。 [変数資産](automation-variables.md)の場合、変数を取得するのか設定するのかに応じて、 **["変数の取得" をキャンバスに追加する]** または **["変数の設定" をキャンバスに追加する]** を選択します。
5. 資産のコードが Runbook に挿入されます。

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Azure Automation の Runbook を編集するには

Windows PowerShell を使用して Runbook を編集するには、任意のエディターを使用して、`.ps1` ファイルに保存します。 [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) コマンドレットを使用して Runbook の内容を取得した後、[Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) コマンドレットを使用して既存のドラフト Runbook を変更したものに置き換えることができます。

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Runbook の内容を取得するには

次のサンプル コマンドでは、Runbook のスクリプトを取得し、スクリプト ファイルに保存する方法を示します。 この例では、ドラフト バージョンを取得します。 Runbook の発行済みバージョンを取得することもできますが、このバージョンを変更することはできません。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell を使用して Runbook の内容を変更するには

次のサンプル コマンドでは、Runbook の既存の内容を、スクリプト ファイルの内容に置き換える方法を示します。 これは、「[Windows PowerShell でスクリプト ファイルから Runbook をインポートするには](manage-runbooks.md#import-a-runbook)」と同じサンプル プロシージャです。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>関連記事

* [Azure Automation で Runbook を管理する](manage-runbooks.md)
* [Windows PowerShell ワークフローについて](automation-powershell-workflow.md)
* [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* [証明書](automation-certificates.md)
* [接続](automation-connections.md)
* [資格情報](automation-credentials.md)
* [スケジュール](automation-schedules.md)
* [変数](automation-variables.md)

