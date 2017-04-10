---
title: "Azure Automation での仮想マシンの起動と停止 - PowerShell ワークフロー | Microsoft Docs"
description: "クラシック仮想マシンを起動および停止するための Runbook を含む Azure Automation シナリオのグラフィカル バージョン。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: d380bd43-d45d-45af-a5b2-78e7f66263c3
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
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 820ef7238593cd99d41cc3fe5d1d7a29699397fc

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

これは、このシナリオの Runbook の PowerShell ワークフロー バージョンです。 [グラフィカル Runbook](automation-solution-startstopvm-graphical.md)を使用して利用することもできます。

## <a name="getting-the-scenario"></a>シナリオの取得
このシナリオは、次のリンクからダウンロード可能な 2 つの PowerShell ワークフロー Runbook で構成されています。  グラフィカル Runbook へのリンクについては、このシナリオの [グラフィカル バージョン](automation-solution-startstopvm-graphical.md) を参照してください。

| Runbook | リンク | 型 | Description |
|:--- |:--- |:--- |:--- |
| Start-AzureVMs |[Azure クラシック VM の起動](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) |PowerShell ワークフロー |Azure サブスクリプションのすべてのクラシック仮想マシンまたは特定のサービス名を持つすべての仮想マシンを起動します。 |
| Stop-AzureVMs |[Azure クラシック VM の停止](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) |PowerShell ワークフロー |Azure アカウントのすべての仮想マシンまたは特定のサービス名を持つすべての仮想マシンを停止します。 |

## <a name="installing-and-configuring-the-scenario"></a>シナリオのインストールと構成
### <a name="1-install-the-runbooks"></a>1.Runbook をインストールする
Runbook をダウンロードしたら、 [Runbook のインポート](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook)の手順を使用してインポートすることができます。

### <a name="2-review-the-description-and-requirements"></a>2.説明と要件を確認する
Runbook には、説明と必要な資産を含むコメント付きヘルプ テキストが含まれています。  また、この記事から同じ情報を取得することもできます。

### <a name="3-configure-assets"></a>3.資産を構成する
Runbook には以下の資産が必要です。これらを作成し、適切な値を設定する必要があります。

| 資産の種類 | 資産名 | Description |
|:--- |:--- |:--- |:--- |
| 資格情報 |AzureCredential |Azure サブスクリプションの仮想マシンを起動および停止する権限を持つアカウントの資格情報が含まれています。  代わりに別の資格情報資産を **Add-AzureAccount** アクティビティの **Credential** パラメーターで指定することもできます。 |
| 変数 |AzureSubscriptionId |Azure サブスクリプションのサブスクリプション ID が含まれています。 |

## <a name="using-the-scenario"></a>シナリオの使用
### <a name="parameters"></a>パラメーター
Runbook にはそれぞれ以下のパラメーターがあります。  すべての必須パラメーターの値を指定する必要があります。必要に応じて、要件に合わせて他のパラメーターの値を指定することができます。

| パラメーター | 型 | 必須 | Description |
|:--- |:--- |:--- |:--- |
| ServiceName |string |なし |値が指定されている場合、そのサービス名を持つすべての仮想マシンが起動または停止します。  値が指定されていない場合、Azure サブスクリプションのすべてのクラシック仮想マシンが起動または停止します。 |
| AzureSubscriptionIdAssetName |string |なし |Azure サブスクリプションのサブスクリプション ID を含む [変数資産](#installing-and-configuring-the-scenario) の名前が含まれています。  値を指定しない場合、 *AzureSubscriptionId* が使用されます。 |
| AzureCredentialAssetName |string |なし |使用する Runbook の資格情報を含む [資格情報資産](#installing-and-configuring-the-scenario) の名前が含まれます。  値を指定しない場合、 *AzureCredential* が使用されます。 |

### <a name="starting-the-runbooks"></a>Runbook の開始
「 [Azure Automation での Runbook の開始](automation-starting-a-runbook.md) 」に示されている方法のいずれかを使用して、このシナリオの Runbook のいずれかを開始できます。

次のサンプル コマンドでは、Windows PowerShell を使用して **StartAzureVMs** を実行し、 *MyVMService*というサービス名のすべての仮想マシンを起動します。

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>出力
Runbook は各仮想マシンに対して[メッセージを出力](automation-runbook-output-and-messages.md)します。このメッセージは、開始または停止命令が正常に送信されたかどうかを示します。  出力の特定の文字列を検索して、各 Runbook の結果を判別することができます。  考えられる出力文字列を次の表にリストします。

| Runbook | 条件 | メッセージ |
|:--- |:--- |:--- |
| Start-AzureVMs |仮想マシンが既に実行されている |MyVM is already running |
| Start-AzureVMs |仮想マシンの起動要求が正常に送信されました |MyVM has been started |
| Start-AzureVMs |仮想マシンの起動要求に失敗しました |MyVM failed to start |
| Stop-AzureVMs |仮想マシンは既に停止しています |MyVM is already stopped |
| Stop-AzureVMs |仮想マシンの停止要求が正常に送信されました |MyVM has been stopped |
| Stop-AzureVMs |仮想マシンの停止要求に失敗しました |MyVM failed to stop |

たとえば、Runbook からの次のコード スニペットでは、 *MyServiceName*というサービス名のすべての仮想マシンの起動を試みます。  起動要求に失敗した場合は、エラー アクションを実行できます。

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>詳細な内訳
このシナリオでの Runbook の詳細な内訳を以下に示します。  Runbook をカスタマイズする場合や、独自の自動化シナリオの作成方法の学習のみの目的でこの情報を使用することができます。

### <a name="parameters"></a>パラメーター
    param (
        [Parameter(Mandatory=$false)]
        [String]  $AzureCredentialAssetName = 'AzureCredential',

        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)]
        [String] $ServiceName
    )

ワークフローは [入力パラメーター](#using-the-scenario)の値の取得から始まります。  資産名が指定されていない場合は、既定の名前が使用されます。

### <a name="output"></a>出力
    # Returns strings with status messages
    [OutputType([String])]

この行では、Runbook の出力が文字列になることを宣言します。  これは必須ではありませんが、親 Runbook は予想される出力の種類を認識できるように、 [子 Runbook](automation-child-runbooks.md) として Runbook が使用される場合のベスト プラクティスです。

### <a name="authentication"></a>認証
    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

次の行では、[資格情報](automation-credentials.md)および Azure サブスクリプションを設定します。これらは、Runbook の残りの部分で使用されます。
まず、**Get-AutomationPSCredential** を使用して、Azure サブスクリプションの仮想マシンを起動および停止するためのアクセス権を持つ資格情報を保持する資産を取得します。 次に、**Add-AzureAccount** がこの資産を使用して、資格情報を設定します。  出力はダミー変数に割り当てられるため、Runbook 出力に含まれません。  

その後、サブスクリプション ID を持つ変数資産が **Get AutomationVariable** で取得され、サブスクリプションが **Select-AzureSubscription** で設定されます。

### <a name="get-vms"></a>VM の取得
    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName)
    {
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else
    {
        $VMs = Get-AzureVM
    }

**Get-AzureVM** が使用されます。  **ServiceName** 入力変数に値が指定されている場合は、そのサービス名を持つ仮想マシンのみが取得されます。  **ServiceName** が空の場合は、すべての仮想マシンが取得されます。

### <a name="startstop-virtual-machines-and-send-output"></a>仮想マシンの起動/停止および出力の送信
    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

次の行では、各仮想マシンを順に確認します。  まず、仮想マシンの **PowerState** を調べ、Runbook に応じて、既に実行されているか停止しているかを確認します。  既に対象の状態になっている場合は、メッセージが出力に送信されてから、Runbook が終了します。  対象の状態でない場合は、**Start-AzureVM** または **Stop-AzureVM** を使用して、変数に格納されている要求の結果に応じて仮想マシンの起動または停止を試みます。  その後、起動または停止要求が正常に送信されたかどうかを示すメッセージが出力に送信されます。

## <a name="next-steps"></a>次のステップ
* 子 Runbook の操作に関する詳細については、「 [Azure Automation での子 Runbook](automation-child-runbooks.md)
* トラブルシューティングに役立つ Runbook 実行時やログ記録中の出力メッセージに関する詳細については、「 [Azure Automation での Runbook の出力およびメッセージ](automation-runbook-output-and-messages.md)




<!--HONumber=Nov16_HO3-->


