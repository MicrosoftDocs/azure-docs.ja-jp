---
title: "仮想マシンの起動と停止 - グラフ | Microsoft Docs"
description: "クラシック仮想マシンを起動および停止するための Runbook を含む Azure Automation シナリオの PowerShell ワークフロー バージョン。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 62d93170-ca3d-42ef-ac1d-6d50b61ac87d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2016
ms.author: magoedte;bwren
redirect_url: https://docs.microsoft.com/azure/automation/automation-solution-vm-management
redirect_document_id: FALSE
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: ba67183cf0518bee102579a8ab16a9be92ed7b73

---

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Azure Automation シナリオ - 仮想マシンの起動と停止
この Azure Automation シナリオには、クラシック仮想マシンを起動および停止するための Runbook が含まれています。  このシナリオは、次のいずれかの場合に使用できます。  

* 独自の環境で Runbook を変更せずに使用する場合。
* カスタマイズされた機能を実行するために Runbook を変更する場合。  
* ソリューション全体の一部として別の Runbook から Runbook を呼び出す場合。
* Runbook の作成概念を学習するためのチュートリアルとして Runbook を使用する場合。

> [!div class="op_single_selector"]
> * [グラフィカル](automation-solution-startstopvm-graphical.md)
> * [PowerShell ワークフロー](automation-solution-startstopvm-psworkflow.md)
>
>

これは、このシナリオの Runbook のグラフィカル バージョンです。 [PowerShell ワークフローの Runbook](automation-solution-startstopvm-psworkflow.md)を使用して利用することもできます。

## <a name="getting-the-scenario"></a>シナリオの取得
このシナリオは、次のリンクからダウンロード可能な 2 つのグラフィカル Runbook で構成されています。  PowerShell ワークフローの Runbook へのリンクについては、このシナリオの [PowerShell ワークフローのバージョン](automation-solution-startstopvm-psworkflow.md) を参照してください。

| Runbook | リンク | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| StartAzureClassicVM |[Azure クラシック VM のグラフィカル Runbook の開始](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) |グラフィカル |Azure サブスクリプションのすべてのクラシック仮想マシンまたは特定のサービス名を持つすべての仮想マシンを起動します。 |
| StopAzureClassicVM |[Azure クラシック VM のグラフィカル Runbook の停止](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) |グラフィカル |Azure アカウントのすべての仮想マシンまたは特定のサービス名を持つすべての仮想マシンを停止します。 |

## <a name="installing-and-configuring-the-scenario"></a>シナリオのインストールと構成
### <a name="1-install-the-runbooks"></a>1.Runbook をインストールする
Runbook をダウンロードしたら、 [グラフィカル Runbook の手順](automation-graphical-authoring-intro.md#graphical-runbook-procedures)に関するページの手順を使用してインポートすることができます。

### <a name="2-review-the-description-and-requirements"></a>2.説明と要件を確認する
Runbook には、説明と必要な資産を含む **Read Me** というアクティビティが含まれています。  この情報を表示するには、**[Read Me]** アクティビティ、**[ワークフロー スクリプト]** パラメーターの順に選択します。  また、この記事から同じ情報を取得することもできます。

### <a name="3-configure-assets"></a>3.資産を構成する
Runbook には以下の資産が必要です。これらを作成し、適切な値を設定する必要があります。  名前は既定値です。  Runbook の開始時に[入力パラメーター](#using-the-runbooks)に別の名前を指定すれば、その名前の資産を使用できます。

| 資産の種類 | 既定の名前 | 説明 |
|:--- |:--- |:--- |:--- |
| [資格情報](automation-credentials.md) |AzureCredential |Azure サブスクリプションの仮想マシンを起動および停止する権限を持つアカウントの資格情報が含まれています。 |
| [Variable](automation-variables.md) |AzureSubscriptionId |Azure サブスクリプションのサブスクリプション ID が含まれています。 |

## <a name="using-the-scenario"></a>シナリオの使用
### <a name="parameters"></a>parameters
Runbook にはそれぞれ以下の[入力パラメーター](automation-starting-a-runbook.md#runbook-parameters)があります。  すべての必須パラメーターの値を指定する必要があります。必要に応じて、要件に合わせて他のパラメーターの値を指定することができます。

| パラメーター | 型 | 必須 | Description |
|:--- |:--- |:--- |:--- |
| ServiceName |string |なし |値が指定されている場合、そのサービス名を持つすべての仮想マシンが起動または停止します。  値が指定されていない場合、Azure サブスクリプションのすべてのクラシック仮想マシンが起動または停止します。 |
| AzureSubscriptionIdAssetName |string |なし |Azure サブスクリプションのサブスクリプション ID を含む [変数資産](#installing-and-configuring-the-scenario) の名前が含まれています。  値を指定しない場合、 *AzureSubscriptionId* が使用されます。 |
| AzureCredentialAssetName |string |なし |使用する Runbook の資格情報を含む [資格情報資産](#installing-and-configuring-the-scenario) の名前が含まれます。  値を指定しない場合、 *AzureCredential* が使用されます。 |

### <a name="starting-the-runbooks"></a>Runbook の開始
「 [Azure Automation での Runbook の開始](automation-starting-a-runbook.md) 」に示されている方法のいずれかを使用して、この記事の Runbook のいずれかを開始できます。

次のサンプル コマンドでは、Windows PowerShell を使用して **StartAzureClassicVM** を実行し、 *MyVMService*というサービス名のすべての仮想マシンを起動します。

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### <a name="output"></a>出力
Runbook は各仮想マシンに対して[メッセージを出力](automation-runbook-output-and-messages.md)します。このメッセージは、開始または停止命令が正常に送信されたかどうかを示します。  出力の特定の文字列を検索して、各 Runbook の結果を判別することができます。  考えられる出力文字列を次の表にリストします。

| Runbook | 条件 | メッセージ |
|:--- |:--- |:--- |
| StartAzureClassicVM |仮想マシンが既に実行されている |MyVM is already running |
| StartAzureClassicVM |仮想マシンの起動要求が正常に送信されました |MyVM has been started |
| StartAzureClassicVM |仮想マシンの起動要求に失敗しました |MyVM failed to start |
| StopAzureClassicVM |仮想マシンが既に実行されている |MyVM is already stopped |
| StopAzureClassicVM |仮想マシンの起動要求が正常に送信されました |MyVM has been started |
| StopAzureClassicVM |仮想マシンの起動要求に失敗しました |MyVM failed to start |

サンプルのグラフィカル Runbook で **子 Runbook** として [StartAzureClassicVM](automation-child-runbooks.md) を使用するイメージを以下に示します。  この場合、以下の表の条件付きリンクが使用されます。

| リンク | 条件 |
|:--- |:--- |
| 正常なリンク |$ActivityOutput['StartAzureClassicVM'] -like "\* has been started" |
| エラー リンク |$ActivityOutput['StartAzureClassicVM'] -notlike "\* has been started" |

![子 Runbook の例](media/automation-solution-startstopvm/graphical-childrunbook-example.png)

## <a name="detailed-breakdown"></a>詳細な内訳
このシナリオでの Runbook の詳細な内訳を以下に示します。  Runbook をカスタマイズする場合や、独自の自動化シナリオの作成方法の学習のみの目的でこの情報を使用することができます。

### <a name="authentication"></a>認証
![認証](media/automation-solution-startstopvm/graphical-authentication.png)

Runbook は[資格情報](automation-credentials.md)および Azure サブスクリプションを設定するアクティビティから始まります。これらは Runbook の残りの部分で使用されます。

最初の 2 つのアクティビティである**サブスクリプション ID の取得**と **Azure 資格情報の取得**では、次の 2 つのアクティビティで使用される[資産](#installing-the-runbook)を取得します。  これらのアクティビティでは直接資産を指定できますが、資産名が必要になります。  ユーザーが [入力パラメーター](#using-the-runbooks)でこれらの名前を指定できるようにするため、これらのアクティビティでは入力パラメーターで指定された名前を持つ資産を取得する必要があります。

**Add-AzureAccount** では、Runbook の残りの部分で使用される資格情報を設定します。  **Azure 資格情報の取得** で取得される資格情報資産には、Azure サブスクリプションの仮想マシンを起動および停止するためのアクセス権が必要です。  使用されるサブスクリプションは、**サブスクリプション ID の取得**のサブスクリプション ID を使用する **Select-AzureSubscription** で選択されます。

### <a name="get-virtual-machines"></a>仮想マシンの取得
![VM の取得](media/automation-solution-startstopvm/graphical-getvms.png)

Runbook は、使用する仮想マシンと、(Runbook に応じて) それらが既に起動しているか停止しているかを判別する必要があります。   2 つのアクティビティのいずれかで VM が取得されます。  **サービス内の VM の取得** は、Runbook の *ServiceName* 入力パラメーターに値が含まれている場合に実行されます。  **すべての VM の取得** は、Runbook の *ServiceName* 入力パラメーターに値が含まれていない場合に実行されます。  このロジックは、各アクティビティの前の条件付きリンクで実行されます。

両方のアクティビティで **Get-azurevm** コマンドレットが使用されます。  **すべての VM の取得**では、すべての仮想マシンを返す **ListAllVMs** パラメーター セットを使用します。  **サービス内の VM の取得**では **GetVMByServiceAndVMName** パラメーター セットを使用し、**ServiceName** パラメーターの **ServiceName** 入力パラメーターを指定します。  

### <a name="merge-vms"></a>VM のマージ
![VM のマージ](media/automation-solution-startstopvm/graphical-mergevms.png)

**VM のマージ** アクティビティは、起動する VM の名前とサービス名を必要とする **Start-AzureVM** への入力を提供するために必要です。  その入力元は**すべての VM の取得**または**サービス内の VM の取得**の場合がありますが、その入力の 1 つのアクティビティを指定できるのは **Start-AzureVM** のみです。   

シナリオは、**Write-Output** コマンドレットを実行する **VM のマージ**を作成することです。  そのコマンドレットの **InputObject** パラメーターは、前の 2 つのアクティビティの入力を結合する PowerShell 式です。  これらのアクティビティの 1 つのみが実行されるため、予想される出力のセットは 1 つのみです。  **Start-AzureVM** では、入力パラメーターでその出力を使用することができます。

### <a name="startstop-virtual-machines"></a>仮想マシンの起動/停止
![VM の起動](media/automation-solution-startstopvm/graphical-startvm.png) ![VM の停止](media/automation-solution-startstopvm/graphical-stopvm.png)

Runbook に応じて、次のアクティビティでは、**Start-AzureVM** または **Stop-AzureVM** を使用して Runbook の起動または停止を試みます。  アクティビティはパイプライン リンクの前にあるため、 **VM のマージ**から返されるオブジェクトごとに 1 回実行されます。  リンクは条件付きであるため、アクティビティが実行されるのは、仮想マシンの *RunningState* が **Start-AzureVM** で *Stopped* になっており、**Stop-AzureVM** で *Started* になっている場合のみです。 この条件が満たされていない場合は、**開始済み通知**または**停止済み通知**が実行され、**Write-Output** を使用してメッセージが送信されます。

### <a name="send-output"></a>出力の送信
![VM の起動通知](media/automation-solution-startstopvm/graphical-notifystart.png) ![VM の停止通知](media/automation-solution-startstopvm/graphical-notifystop.png)

Runbook の最後の手順は、各仮想マシンの起動または停止要求が正常に送信されたかどうかを示す出力の送信です。 **Write-Output** はそれぞれ別個になっており、条件付きリンクでどれを実行するかを判別します。  **VM 起動通知**または **VM 停止通知**は、*OperationStatus* が *Succeeded* の場合に実行されます。  *OperationStatus* が他の値の場合、**起動失敗通知**または**停止失敗通知**が実行されます。

## <a name="next-steps"></a>次のステップ
* [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* [Azure Automation での子 Runbook](automation-child-runbooks.md)
* [Runbook output and messages in Azure Automation (Azure Automation での Runbook の出力および メッセージ)](automation-runbook-output-and-messages.md)



<!--HONumber=Nov16_HO3-->


