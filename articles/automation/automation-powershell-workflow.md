---
title: "Azure Automation の PowerShell ワークフローについて | Microsoft Docs"
description: "この記事では、PowerShell に慣れている作成者を対象に、PowerShell と PowerShell ワークフローの具体的な違いと、Automation Runbook に適用できる概念ついて簡単に説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 480a40bd5ecd58f11b10c27e7e0d2828bcae1f17
ms.openlocfilehash: 50966ed518b79f2033680790432e29b0c9e7b289
ms.lasthandoff: 01/24/2017


---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Automation Runbook 向けの Windows PowerShell ワークフローの基本的な概念の説明 
Azure Automation の Runbook は Windows PowerShell ワークフローとして実装されています。  Windows PowerShell ワークフローは Windows PowerShell スクリプトと似ていますが、新規ユーザーにはわかりにくい大きな違いがいくつかあります。  この記事は、PowerShell ワークフローを使用して Runbook を作成するときに利用することを目的としていますが、チェックポイントを必要とする場合以外は、PowerShell を使用して Runbook を作成することをお勧めします。  PowerShell ワークフローの Runbook を作成する場合は構文の違いが多数あるため、効果的なワークフローを記述するにはさらに作業が必要になります。  

ワークフローとは、時間のかかるタスクを実行したり、複数のデバイスや管理ノードにまたがる複数のステップで調整を必要としたりする手順を、プログラミングで連結してひとつながりにしたものです。 ワークフローが通常のスクリプトよりも優れている点としては、同時に複数のデバイスに対して操作を実行できることや、障害から自動的に復元できることなどがあります。 Windows PowerShell ワークフローは、Windows Workflow Foundation を活用した Windows PowerShell スクリプトです。 ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。

この記事のトピックに関する詳細については、「 [Windows PowerShell ワークフローについて](http://technet.microsoft.com/library/jj134242.aspx)」をご覧ください。

## <a name="basic-structure-of-a-workflow"></a>ワークフローの基本構造
PowerShell スクリプトを PowerShell ワークフローに変換する最初のステップは、スクリプトを **Workflow** キーワードで囲むことです。  ワークフローは、 **Workflow** キーワードで始まり、中かっこで囲まれたスクリプトの本文が後に続きます。 次の構文に示すように、ワークフローの名前は **Workflow** キーワードの後に続きます。

    Workflow Test-Workflow
    {
       <Commands>
    }

ワークフローの名前は、Automation の Runbook の名前と一致する必要があります。 Runbook がインポートされている場合、ファイル名はワークフロー名と一致していなければならず、末尾は .ps1 でなければなりません。

ワークフローにパラメーターを追加するには、スクリプトの場合と同様に **Param** キーワードを使用します。

## <a name="code-changes"></a>コードの変更
PowerShell ワークフローのコードは PowerShell スクリプト コードとほぼ同じですが、いくつかの重要な変更点があります。  次のセクションでは、ワークフローで実行するための PowerShell スクリプトに対して行う必要がある変更について説明します。

### <a name="activities"></a>アクティビティ
アクティビティとは、ワークフロー内の特定のタスクです。 スクリプトが&1; つ以上のコマンドで構成されているのと同様に、ワークフローも順番に実行される&1; つ以上のアクティビティで構成されます。 Windows PowerShell ワークフローでは、ワークフローの実行時に Windows PowerShell コマンドレットの多くが自動でアクティビティに変換されます。 Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、実際には Windows Workflow Foundation で実行されます。 対応するアクティビティがないコマンドレットの場合、Windows PowerShell ワークフローは [InlineScript](#inlinescript) アクティビティ内のコマンドレットを自動的に実行します。 InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できない一連のコマンドレットがあります。 これらの概念の詳細については、「 [スクリプト ワークフローでのアクティビティの使用](http://technet.microsoft.com/library/jj574194.aspx)」を参照してください。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。 ワークフローの共通パラメーターの詳細については、「[about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)」を参照してください。

### <a name="positional-parameters"></a>位置指定パラメーター
ワークフローのアクティビティおよびコマンドレットでは、位置指定パラメーターを使用できません。  このため、パラメーターを使用する必要があります。

たとえば、実行中のすべてのサービスを取得する次のコードを考えてみます。

     Get-Service | Where-Object {$_.Status -eq "Running"}

同じコードをワークフローで実行しようとすると、「指定された名前のパラメーターを使用してパラメーター セットを解決できません。」というメッセージが表示されます。  これを修正するには、次のようにパラメーター名を指定します。

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>逆シリアル化されたオブジェクト
ワークフロー内のオブジェクトは逆シリアル化されます。  これは、オブジェクトのプロパティは使用できるけれども、メソッドが使用できないことを意味します。  例として、サービス オブジェクトの Stop メソッドを使用してサービスを停止する次の PowerShell コードを考えます。

    $Service = Get-Service -Name MyService
    $Service.Stop()

ワークフローでこれを実行しようとすると、「Windows PowerShell ワークフローでは、メソッド呼び出しはサポートされていません」というエラー メッセージが表示されます。  

これに対処する&1; つの方法は、これら&2; つのコード行を [InlineScript](#inlinescript) ブロックにラップすることであり、この場合 $Service はブロック内のサービス オブジェクトです。

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

もう&1; つの方法は、メソッドとして同じ機能を実行する別のコマンドレットがある場合は、それを使用することです。  このサンプルの場合、Stop-Service コマンドレットは Stop メソッドと同じ機能を提供し、ワークフローでは次のように使用できます。

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
**InlineScript** アクティビティは、PowerShell ワークフローではなく従来の PowerShell スクリプトとして&1; つまたは複数のコマンドを実行する必要がある場合に便利です。  ワークフロー内のコマンドは Windows Workflow Foundation に送信されて処理されますが、InlineScript ブロック内のコマンドは Windows PowerShell によって処理されます。

InlineScript は、次に示す構文を使用します。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

InlineScript の出力を変数に割り当てることで、出力を返すことができます。 次の例では、サービスを停止した後、サービス名を出力しています。

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


InlineScript ブロックに値を渡すことはできますが、 **$Using** スコープ修飾子を使用する必要があります。  次の例は前の例と同じですが、サービス名を変数によって渡しています。

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


InlineScript アクティビティは特定のワークフローにとって重要ですが、ワークフロー コンストラクトをサポートせず、次の理由により、必要な場合にのみ使用する必要があります。

* InlineScript ブロックの内部で[チェックポイント](#checkpoints)を使用することはできません。 ブロック内でエラーが発生した場合は、ブロックの先頭から再開する必要があります。
* InlineScript ブロックの内部で[並列実行](#parallel-processing)を使用することはできません。
* InlineScript は、InlineScript ブロックの長さ全体について Windows PowerShell セッションを保持するため、ワークフローの拡張性に影響します。

InlineScript の使用の詳細については、「[ワークフローでの Windows PowerShell コマンドの実行](http://technet.microsoft.com/library/jj574197.aspx)」および「[about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)」を参照してください。

## <a name="parallel-processing"></a>並列処理
Windows PowerShell ワークフローの利点の&1; つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。

**Parallel** キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。 これには、次に示す構文を使用します。 この場合、Activity1 と Activity2 は同時に開始されます。 Activity3 は、Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


例として、複数のファイルをネットワーク上にコピーする次の PowerShell コマンドを考えます。  これらのコマンドは、1 つのファイルのコピーが完了してから次へファイルのコピーが開始されるように、順番に実行されます。     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

次のワークフローでは、すべてのコピーが同時に開始されるように、同じコマンドが並列実行されます。  すべてのファイルのコピーが完了してはじめて、完了メッセージが表示されます。

    Workflow Copy-Files
    {
        Parallel
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


**ForEach -Parallel** の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。 コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。 これには、次に示す構文を使用します。 この場合、Activity1 は、コレクション内のすべての項目に対して同時に開始されます。 各項目について、Activity1 が完了してから Activity2 が開始されます。 Activity3 は、すべての項目における Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

次の例は、並列でファイルのコピーを行う前の例と似ています。  この場合、各ファイルのコピーが完了するたびに、メッセージが表示されます。  すべてのファイルのコピーが完了してはじめて、最終的な完了メッセージが表示されます。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> 子 runbook を並列で実行することはお勧めしません。これを行うと、結果の信頼性が低くなることが示されているためです。  子 Runbook からの出力が表示されないことがあり、1 つの子 Runbook での設定が並列に実行されている他の子 Runbook に影響を与える可能性があります。
>

## <a name="checkpoints"></a>チェックポイント
*チェックポイント* は、変数の現在の値と、そのポイントに生成された出力を含むワークフローの現在の状態のスナップショットです。 ワークフローがエラーで終了した場合、または中断した場合、次の実行時には、ワークフローの先頭からではなく、最後のチェックポイントから開始されます。  **Checkpoint-Workflow** アクティビティを使用してワークフローにチェックポイントを設定できます。

次のサンプル コードでは、Activity2 の後に例外が発生し、ワークフローが終了します。 ワークフローを再実行すると、設定された最後のチェックポイントの直後に Activity2 があるため、まず Activity2 が実行されます。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

例外を引き起こす可能性があり、ワークフローが再開された場合は繰り返す必要のないアクティビティの後に、ワークフローのチェックポイントを設定する必要があります。 たとえば、ワークフローによって仮想マシンが作成される場合があります。 チェックポイントをコマンドの前後に設定して、仮想マシンを作成できます。 作成に失敗した場合、ワークフローが再開されると、コマンドは繰り返し実行されます。 作成の成功後にワークフローが失敗した場合、ワークフローが再開されても仮想マシンが再び作成されることはありません。

次の例では、ネットワーク上の場所に複数のファイルをコピーし、各ファイルの後にチェックポイントを設定します。  ネットワーク上の場所が失われた場合は、ワークフローがエラーで終了します。  ワークフローを再び開始すると、最後のチェックポイントで再開するので、既にコピーされているファイルだけがスキップされます。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

ユーザー名資格情報は、[Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) アクティビティを呼び出した後、または最後のチェックポイントの後は保持されないため、資格情報を null に設定し、**Suspend-Workflow** またはチェックポイントが呼び出された後にアセット ストアから再取得する必要があります。  そうしないと、次のエラー メッセージが表示されます: *永続データの保存が完了できなかったか、保存された永続データが壊れているため、ワークフロー ジョブを再開できません。ワークフローを再起動する必要があります。*

次の同じコードは、PowerShell ワークフロー Runbook でこれを処理する方法を示しています。

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


これは、サービス プリンシパルで構成された実行アカウントを使用して認証を行う場合には必要ありません。  

チェックポイントの詳細については、「[スクリプト ワークフローへのチェックポイントの追加](http://technet.microsoft.com/library/jj574114.aspx)」を参照してください。

## <a name="next-steps"></a>次のステップ
* PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。

