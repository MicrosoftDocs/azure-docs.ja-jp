---
title: "webhook を使用した Azure Automation の Runbook の開始 | Microsoft Docs"
description: "HTTP 呼び出しから Azure Automation の Runbook を開始することをクライアントに許可する Webhook。  この記事では、Webhook を作成する方法と、Webhook を呼び出して Runbook を開始する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: magoedte;bwren;sngun
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 6c65427fcd18e41a90dfb872aa9525f758b17b87
ms.lasthandoff: 03/18/2017


---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>webhook を使用した Azure Automation の Runbook の開始
*Webhook* を使用することにより、単一の HTTP 要求を通して Azure Automation で特定の Runbook を開始することができます。 これにより、Azure Automation API を使用した完全なソリューションを実装しなくても、Visual Studio Team Services、GitHub、Microsoft Operations Management Suite Log Analytics などの外部サービスやカスタム アプリケーションで Runbook を開始できます。  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Webhook の詳細
次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | Description |
|:--- |:--- |
| 名前 |Webhook に使用する任意の名前を指定できます。これはクライアントには公開されません。  これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。 <br>  ベスト プラクティスとして、Webhook を使用するクライアントに関連した名前を Webhook に付ける必要があります。 |
| URL |Webhook の URL は、クライアントが Webhook にリンクされた Runbook を開始するために HTTP POST で呼び出す一意のアドレスです。  これは、Webhook を作成するときに自動的に生成されます。  カスタム URL を指定することはできません。 <br> <br>  この URL には、追加の認証なしで、サードパーティ製システムによる Runbook 呼び出しを可能にするためのセキュリティ トークンが含まれています。 その理由で、これはパスワードと同じように扱う必要があります。  セキュリティ上の理由から、Webhook の作成時に Azure ポータルで表示できるのは URL だけです。 将来の使用に備えて、URL を安全な場所にメモしてください。 |
| 有効期限 |証明書のように、各 Webhook には有効期限があり、それ以降は使用できなくなります。  この有効期限は、webhook の作成後に変更できます。 |
| 有効 |既定では、Webhook は作成時に有効になります。  Disabled に設定した場合、クライアントはそれを使用できなくなります。  **Enabled** プロパティは、Webhook の作成時、または作成後はいつでも設定できます。 |

### <a name="parameters"></a>パラメーター
Webhook は、Runbook がその Webhook によって開始されたときに使用される Runbook のパラメーターの値を定義できます。 Webhook には、Runbook の任意の必須パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。 Webhook に対して構成されているパラメーター値は、Webhook の作成後であっても変更できます。 1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なるパラメーター値を使用することができます。

Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値を上書きできません。  クライアントからのデータを受け取るために、Runbook は、[object] 型の **$WebhookData** という&1; つのパラメーターを取ることができます。クライアントが POST 要求に含めたデータは、このパラメーターに入れられます。

![Webhookdata プロパティ](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| WebhookName |Webhook の名前。 |
| RequestHeader |受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| RequestBody |受信 POST 要求の本文。  文字列、JSON、XML、フォームのエンコード済みデータなどの書式設定が保持されます。 想定されるデータ形式を操作するには、Runbook を記述する必要があります。 |

**$WebhookData** パラメーターのサポートに必要な Webhook の構成はありません。また、これを受け入れるために Runbook は必要ありません。  Runbook がパラメーターを定義していない場合は、クライアントから送信された要求の詳細は無視されます。

Webhook の作成時に $WebhookData の値を指定した場合、クライアントの要求本文にデータが含まれていなくても、クライアントの POST 要求からのデータで Webhook が Runbook を開始した時点でその値はオーバーライドされます。  Webhook 以外の方法を使用して $WebhookData が含まれる Runbook を開始する場合、Runbook で認識される $Webhookdata の値を指定することができます。  この値は $Webhookdata と同じ[プロパティ](#details-of-a-webhook)を持つオブジェクトにする必要があります。それにより、Runbook は、Webhook によって渡された実際の WebhookData を操作しているかのように適切な処理を実行できます。

たとえば、Azure ポータルから次の Runbook を開始するときにテスト用のサンプル WebhookData を渡す場合、WebhookData はオブジェクトであるため、UI に JSON として渡す必要があります。

![UI からの WebhookData パラメーター](media/automation-webhooks/WebhookData-parameter-from-UI.png)

上記の Runbook で、WebhookData パラメーターに次のプロパティがあるとします。

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Test User*
3. RequestBody: *[“VM1”, “VM2”]*

この場合は、次の JSON 値を WebhookData パラメーター用の UI に渡します。  

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"[\"VM1\",\"VM2\"]"}

![UI から WebhookData パラメーターを開始する](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> すべての入力パラメーターの値は、Runbook のジョブに記録されます。  つまり、Webhook の要求でクライアントから提供された入力はすべて記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。  このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。
>

## <a name="security"></a>セキュリティ
Webhook のセキュリティは、呼び出しを許可するセキュリティ トークンが含まれる URL のプライバシーに依存します。 正しい URL に対して要求がなされる場合、Azure Automation はその要求に対してどの認証も実行しません。 このため、要求を検証する他の方法を使用しない場合は、機密性の高い機能を実行する Runbook で Webhook を使用しないでください。

Runbook 内にロジックを含め、$WebhookData パラメーターの **WebhookName** プロパティを調べることによって、Webhook で呼び出されたことを判別できます。 Runbook は、**RequestHeader** または **RequestBody** プロパティの特定の情報を探すことによって、追加の検証を実行できます。

別の方法として、Webhook 要求の受信時に外部条件のいくつかの検証を Runbook に実行させることです。  たとえば、GitHub リポジトリに新しいコミットがある場合は常に GitHub によって呼び出される Runbook について考えてみましょう。  Runbook は、新しいコミットが実際に発生したばかりであることを検証するために、GitHub に接続してから、処理を続行することができます。

## <a name="creating-a-webhook"></a>Webhook の作成
次の手順を使用して、Runbook にリンクされた新しい Webhook を Azure ポータルに作成します。

1. Azure ポータルの **[Runbook] ブレード** から、Webhook がその詳細ブレードの表示を開始する Runbook をクリックします。
2. ブレードの上部にある **Webhook** をクリックして、**[Webhook の追加]** ブレードを開きます。 <br>
   ![Webhook ボタン](media/automation-webhooks/webhooks-button.png)
3. **[新しい Webhook の作成]** をクリックして、**[Webhook ブレードの作成]** を開きます。
4. Webhook の**名前**、**有効期限**、およびそれを有効にする必要があるかどうかを指定します。 これらのプロパティの詳細については、「 [Webhook の詳細](#details-of-a-webhook) 」を参照してください。
5. コピー アイコンをクリックし、Ctrl + C キーを押して Webhook の URL をコピーします。  次に、それを安全な場所に記録します。  **Webhook を作成したら、再度 URL を取得することはできません。** <br>
   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
6. **[パラメーター]** をクリックし、Runbook のパラメーターの値を指定します。  Runbook に必須のパラメーターがある場合、値を指定しない限り Webhook を作成できません。
7. **[作成]** をクリックして Webhook を作成します。

## <a name="using-a-webhook"></a>Webhook の使用
Webhook を作成後に使用する場合、クライアント アプリケーションは Webhook の URL で HTTP POST を発行する必要があります。  Webhook の構文は、次の形式になります。

    http://<Webhook Server>/token?=<Token Value>

クライアントは、POST 要求から次のリターン コードのいずれかを受け取ります。  

| コード | テキスト | 説明 |
|:--- |:--- |:--- |
| 202 |承認済み |要求が承認され、Runbook が正常にキューに入れられました。 |
| 400 |正しくない要求 |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook の有効期限が切れている。</li> <li>webhook が無効になっている。</li> <li>URL 内のトークンが無効になっている。</li>  </ul> |
| 404 |見つかりません |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook が見つからない。</li> <li>Runbook が見つからない。</li> <li>アカウントが見つからない。</li>  </ul> |
| 500 |内部サーバー エラー |URL は有効ですが、エラーが発生しました。  要求を再送信してください。 |

要求が成功したと仮定した場合、Webhook の応答には、次のような JSON 形式のジョブ ID が含まれています。 ここに含まれるジョブ ID は&1; つですが、JSON 形式は将来拡張できるようになっています。

    {"JobIds":["<JobId>"]}  

Runbook ジョブの完了時期と Webhook からの完了状態は、クライアントからは判別できません。  この情報は、[Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) や [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163826.aspx) などの方法でジョブ ID を使用すると判別できます。

### <a name="example"></a>例
次の例では、Windows PowerShell を使用して Webhook で Runbook を開始します。  Webhook は、HTTP 要求を実行できる任意の言語で使用できます。ここでは、例として Windows PowerShell を使用します。

Runbook では、要求の本文に JSON 形式の仮想マシン一覧が必要です。 この例ではほかにも、要求のヘッダーに Runbook を開始したユーザーや日時などの情報を含めます。      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


次の図に、この要求の ( [Fiddler](http://www.telerik.com/fiddler) トレースを使用した) ヘッダー情報を示します。 この中には、追加したカスタムの Date ヘッダーや From ヘッダー以外に、HTTP 要求の標準ヘッダーが含まれます。  これらの値は、**WebhookData** の **RequestHeaders** プロパティとして Runbook で使用できます。

![Webhook ボタン](media/automation-webhooks/webhook-request-headers.png)

次の図に、**WebhookData** の **RequestBody** プロパティとして Runbook で利用できる ([Fiddler](http://www.telerik.com/fiddler) トレースを使用した) 要求の本文を示します。 要求の本文に含まれていた形式が JSON であったため、この形式は JSON になります。     

![Webhook ボタン](media/automation-webhooks/webhook-request-body.png)

次の図に、Windows PowerShell から送信される要求と、結果の応答を示します。  ジョブ ID が応答から抽出され、文字列に変換されます。

![Webhook ボタン](media/automation-webhooks/webhook-request-response.png)

次の例の Runbook では、前の例の要求を受け入れ、要求の本文で指定された仮想マシンを起動します。

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Azure アラートに応じて Runbook を開始する
Webhook 対応の Runbook を使用して、[Azure アラート](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)に対応することができます。 Azure 内のリソースは、Azure アラートを活用してパフォーマンス、可用性、および利用状況などの統計を収集することで監視できます。 Azure リソースの監視メトリックまたはイベントに基づいて通知を受け取ることができます。現在、Automation アカウントではメトリックしかサポートしていません。 指定したメトリックの値が割り当て済みのしきい値を超えた場合、または構成済みのイベントがトリガーされた場合に、アラートの解決を担当するサービス管理者または共同管理者に通知が送信されます。メトリックとイベントの詳細については、[Azure アラート](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)に関する記事を参照してください。

Azure アラートを通知システムとして使用するだけでなく、アラートに応じて Runbook を開始することもできます。 Azure Automation には、Azure アラートを使用して Webhook 対応の Runbook を開始する機能があります。 メトリックが構成済みのしきい値を超えると、アラート ルールがアクティブになって Automation Webhook がトリガーされ、この Webhook により Runbook が実行されます。

![Webhook](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>アラート コンテキスト
仮想マシンなどの Azure リソースを例に取ると、このマシンの CPU 使用率は重要なパフォーマンス メトリックの&1; つです。 長期間にわたり CPU 使用率が 100% になるか特定の値を超えた場合、仮想マシンを再起動して問題を解決する必要があります。 この問題は、CPU 使用率をメトリックとするアラート ルールを仮想マシンに構成することで解決できます。 ここで、CPU 使用率はほんの一例であり、Azure リソースに構成できるメトリックは他にも多数あります。また、この問題を解決するために行うアクションは仮想マシンの再起動ですが、他のアクションを実行するように Runbook を構成することもできます。

このアラート ルールがアクティブになって Webhook 対応の Runbook をトリガーするときに、ルールから Runbook にアラート コンテキストが送信されます。 [アラート コンテキスト](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)には、**SubscriptionID**、**ResourceGroupName**、**ResourceName**、**ResourceType**、**ResourceId**、**Timestamp** など、Runbook がアクション対象のリソースを特定するために必要になる詳細が含まれています。 アラート コンテキストは Runbook に送信される **WebhookData** オブジェクトの本文に埋め込まれており、**Webhook.RequestBody** プロパティを使用して評価することができます

### <a name="example"></a>例
サブスクリプション内に Azure 仮想マシンを作成し、[CPU パーセンテージ メトリックを監視するアラート](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)に関連付けます。 アラートの作成時には、[Webhook] フィールドに、Webhook の作成中に生成された Webhook の URL を必ず入力してください。

次のサンプル Runbook は、アラート ルールがアクティブになるとトリガーされ、アクション対象のリソースを特定するために Runbook が必要とするアラート コンテキストのパラメーターを収集します。

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>次のステップ
* Runbook を開始するさまざまな方法の詳細については、[Runbook の開始](automation-starting-a-runbook.md)に関する記事をご覧ください。
* Runbook ジョブの状態の表示については、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」をご覧ください。
* Azure Automation を使用して Azure アラートに対処する方法については、[Automation Runbook での Azure VM アラートの修復](automation-azure-vm-alert-integration.md)に関する記事をご覧ください。

