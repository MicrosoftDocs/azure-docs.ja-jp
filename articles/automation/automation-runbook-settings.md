---
title: "Runbook の設定 | Microsoft Docs"
description: "Azure Automation の Runbook の構成設定について説明し、Azure 管理ポータルと Windows PowerShell のそれぞれで設定を変更する方法について取り上げます。"
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: ac8b5372aa06eac8c9a701f0621dbb675fbb565c
ms.openlocfilehash: 20ecbc270e61d234e026e6ba2634c7aad63b3355


---
# <a name="runbook-settings"></a>Runbook の設定
Azure Automation 内の各 Runbook には、それぞれを識別し、ログ記録の動作を変更するのに役立つ設定が複数あります。 これらの各設定を以下に示し、その後にそれらの変更方法手順を記します。

## <a name="settings"></a>設定
### <a name="name-and-description"></a>名前と説明
Runbook の作成後に、名前を変更することはできません。 説明は省略可能で、最大 512 文字まで可能です。

### <a name="tags"></a>タグ
タグを使用すると、個別の単語や語句を割り当て、Runbook を識別するのに役立ちます。 たとえば、Runbook を [PowerShell ギャラリー](https://www.powershellgallery.com/)に送信する場合、対象の Runbook が一覧表示されるカテゴリを特定するタグを指定します。 コンマで区切ると 1 つの Runbook に複数のタグを指定できます。

### <a name="logging"></a>ログの記録
既定では、詳細レコードと進行状況レコードはジョブ履歴に書き込まれません。 設定を変更し、特定の Runbook でこうしたレコードをログに記録できます。 これらのレコードの詳細については、[Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)に関するページをご覧ください。

## <a name="changing-runbook-settings"></a>Runbook の設定変更

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Azure Portal で Runbook の設定を変更する
Runbook の設定変更は、Azure Portal で対象 Runbook の **[設定]** ブレードから実行できます。

1. Azure ポータルで、 **[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. **[Runbook]** タブを選択します。
3. Runbook の名前をクリックすると、その Runbook の [設定] ブレードが表示されます。 ここで、タグや Runbook の説明を指定、変更したり、設定のログ記録とトレースを構成したり、問題を解決するためのサポート ツールにアクセスしたりできます。     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Windows PowerShell で Runbook の設定を変更する
[Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) コマンドレットを使用して、Runbook の設定を変更できます。 複数のタグを指定する場合は、配列を指定するか、コンマで区切った値を使用して 1 つの文字列をタグ パラメーターに指定できます。 [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx) を使用して、現在のタグを取得できます。

以下のサンプル コマンドは、ある Runbook のプロパティ設定方法を示しています。 このサンプルでは、既存のタグに 3 つのタグを追加し、詳細レコードをログに記録するように指定します。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>次のステップ
* [Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)に関するページで、Runbook の出力とエラー メッセージを作成および取得する方法について確認します 
* [Runbook の作成またはインポート](automation-creating-importing-runbook.md)に関するページで、コミュニティまたはその他のソースによって開発された Runbook を追加する方法、または独自の Runbook を作成する方法を確認します 




<!--HONumber=Nov16_HO3-->


