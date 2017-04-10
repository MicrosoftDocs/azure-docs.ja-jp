---
title: "Azure Automation での子 Runbook | Microsoft Docs"
description: "Azure Automation で別の Runbook から Runbook を開始し、それらの間で情報共有する異なる方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 919887b9-43e2-4c16-883c-f81807fe37db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cf3d1ae66483fed4aa9cd31f674729e4b875653c
ms.lasthandoff: 11/17/2016


---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation での子 Runbook
 Azure Automation では、他の Runbook でも使用できる個別の関数で再利用可能なモジュールの Runbook を作成することをお勧めします。 親 Runbook は多くの場合、必要な機能を実行する 1 つまたは複数の子 Runbook を呼び出します。 子 Runbook を呼び出すには 2 つの方法があります。それぞれの特徴的な相違点を理解しておくと、さまざまな状況でどちらが適しているかを判断できるようになります。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>インライン実行で子 Runbook を呼び出す 
別の Runbook からインラインで Runbook を呼び出すには、Runbook の名前を使用し、アクティビティやコマンドレットに使用するのと同じパラメーター値を指定します。  同じ Automation アカウントのすべての Runbook は、他のすべての Runbook で同じ方法で使用されます。 親 Runbook は子 Runbook が完了してから次の行に移動し、すべての出力は直接親に返されます。親 Runbook は子 Runbook が完了してから次の行に移動し、すべての出力は直接親に返されます。

Runbook をインラインで呼び出すと、親 Runbook と同じジョブで実行されます。 実行された子 Runbook はジョブ履歴には表示されません。 子 Runbook からのすべての例外とストリーム出力は、親に関連付けられます。 これより、子 Runbook やストリーム出力のいずれかによって発生した例外が親 Runbook のジョブに関連付けられるため、ジョブの数が減って追跡しやすくなり、問題の解決がしやすくなります。

Runbook を発行する場合、呼び出される子 Runbook はすべて発行済みでなければなりません。 これは、Runbook のコンパイル時に、Azure Automation によってすべての子 Runbook と関連付けが行われるためです。 関連付けられていないと、親 Runbook は正常に発行されたかのように見えますが、起動時に例外が発生します。 例外が発生した場合、親 Runbook を再発行して、子 Runbook が正しく参照されるようにします。 子 Runbook が変更されても、関連付けは作成済みになっているため、親 Runbook を再発行する必要はありません。

インラインで呼び出された子 Runbook のパラメーターは、複雑なオブジェクトを含め任意のデータ型を使用できるほか、Microsoft Azure 管理ポータルや Start-AzureRmAutomationRunbook コマンドレットを使用して Runbook が起動されるため、 [JSON のシリアル化](automation-starting-a-runbook.md#runbook-parameters) は行われません。

### <a name="runbook-types"></a>Runbook の種類
相互に呼び出すことができる種類:

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) と[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) は、相互にインラインで呼び出すことができます (両方とも PowerShell ベースです)。
* [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) とグラフィカル PowerShell ワークフロー Runbook は、相互にインラインで呼び出すことができます (両方とも PowerShell ワークフロー ベースです)
* PowerShell の型と PowerShell ワークフローの型は、相互にインラインで呼び出すことはできません。また、Start-AzureRmAutomationRunbook を使用する必要があります。

発行順序が重要になる状況:

* Runbook の発行順序は、PowerShell ワークフロー Runbook とグラフィカル PowerShell ワークフロー Runbook に対してのみ重要です。

インライン実行を使用してグラフィカル Runbook または PowerShell ワークフロー Runbook を子として呼び出すときは、Runbook の名前だけを使用します。  PowerShell の子 Runbook を呼び出すときは、名前の前に *.\\*  を付けて、そのスクリプトがローカル ディレクトリにあることを指定する必要があります。 

### <a name="example"></a>例
次の例では、複雑なオブジェクト、整数、およびブール値の 3 つのパラメーターを受け入れるテスト用の子 Runbook を呼び出します。 子 Runbook の出力は、変数に割り当てられます。  この場合は、子 Runbook は PowerShell ワークフロー Runbook です。

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

次に示すのは、子として PowerShell Runbook を使用する場合の例です。

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


## <a name="starting-a-child-runbook-using-cmdlet"></a>コマンドレットを使用して子 Runbook を開始する
「[Windows PowerShell での Runbook の開始](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell)」で説明されているように、[Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) コマンドレットを使用して Runbook を開始できます。 このコマンドレットの使用には 2 つのモードがあります。  一方のモードでは、コマンドレットは、子 Runbook の子ジョブが作成されるとすぐにジョブ ID を返します。  **-wait** を指定すると有効になる他のモードでは、コマンドレットは、子ジョブが完了して子 Runbook からの出力を返すまで待機します。

コマンドレットで開始した子 Runbook のジョブは、親 Runbook とは別のジョブで実行されます。 これにより、インラインで Runbook を呼び出す場合よりも多くのジョブが実行されるため、追跡が困難になります。 親は、それぞれの子 Runbook の完了を待たずに、複数の子 Runbook を非同期に開始できます。 複数の子 Runbook をインラインで同じように並列実行するには、親 Runbook で [parallel キーワード](automation-powershell-workflow.md#parallel-processing)を使用する必要があります。

コマンドレットで開始した子 Runbook のパラメーターは、「 [Runbook のパラメーター](automation-starting-a-runbook.md#runbook-parameters)」で説明されているハッシュテーブルとして提供されます。 単純なデータ型のみを使用できます。 Runbook に複雑なデータ型のパラメーターが使用されている場合は、インラインで呼び出す必要があります。

### <a name="example"></a>例
次の例では、Start-AzureRmAutomationRunbook -wait パラメーターを使用することにより、パラメーターを含む子 Runbook を開始して、完了まで待機します。 完了すると、その出力が子 Runbook から収集されます。

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook を呼び出す方法の比較
次の表は、別の Runbook から Runbook を呼び出すための次の 2 つの方法の相違点をまとめたものです。

|  | インライン | コマンドレット |
|:--- |:--- |:--- |
| ジョブ |子 Runbook は、親と同じジョブで実行されます。 |子 Runbook 用に別のジョブが作成されます。 |
| 実行 |親 Runbook は、子 Runbook の完了を待ってから続行します。 |親 Runbook は、子 Runbook が開始されたらすぐに続行するか、 *または* 、子ジョブの完了を待ちます。 |
| 出力 |親 Runbook は、子 Runbook から出力を直接取得できます。 |親 Runbook は、子 Runbook ジョブから出力を取得する必要があるか、 *または* 、子 Runbook から出力を直接取得できます。 |
| パラメーター |子 Runbook のパラメーター値は個別に指定され、任意のデータ型を使用できます。 |子 Runbook のパラメーターの値は、1 つのハッシュテーブルに結合する必要があり、JSON のシリアル化を利用する、単純、配列、およびオブジェクトの各データ型のみを含めることができます。 |
| Automation アカウント |親 Runbook は、同じ Automation アカウントの子 Runbook のみを使用できます。 |親 Runbook は、同じ Azure サブスクリプションの Automation アカウントの子 Runbook のほか、接続されていれば別のサブスクリプションの Automation アカウントの子 Runbook も使用できます。 |
| 発行 |親 Runbook を発行する前に、子 Runbook を発行する必要があります。 |親 Runbook を開始する前のある時点で、子 Runbook をパブリッシュする必要があります。 |

## <a name="next-steps"></a>次のステップ
* [Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)
* [Runbook output and messages in Azure Automation (Azure Automation での Runbook の出力および メッセージ)](automation-runbook-output-and-messages.md)


