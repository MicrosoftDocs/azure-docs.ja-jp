---
title: webhook を使用した Azure Automation の Runbook の開始
description: HTTP 呼び出しから Azure Automation の Runbook を開始することをクライアントに許可する Webhook。  この記事では、Webhook を作成する方法と、Webhook を呼び出して Runbook を開始する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc03425a64486e449b4df93ea187435a1e893dda
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849600"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>webhook を使用した Azure Automation の Runbook の開始

*Webhook* を使用することにより、単一の HTTP 要求を通して Azure Automation で特定の Runbook を開始することができます。 これにより、Azure DevOps Services、GitHub、Azure Monitor ログなどの外部サービス、またはカスタム アプリケーションにおいて、Azure Automation API を使用した完全なソリューションを実装していなくても、Runbook を開始することができます。
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)

> [!NOTE]
> Webhook を使用して Python Runbook を開始することはできません。

## <a name="details-of-a-webhook"></a>Webhook の詳細

次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | Description |
|:--- |:--- |
| 名前 |Webhook に使用する任意の名前を指定できます。これはクライアントには公開されません。 これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。 <br> ベスト プラクティスとして、webhook を使用するクライアントに関連した名前を webhook に付ける必要があります。 |
| URL |Webhook の URL は、クライアントが Webhook にリンクされた Runbook を開始するために HTTP POST で呼び出す一意のアドレスです。 これは、Webhook を作成するときに自動的に生成されます。 カスタム URL を指定することはできません。 <br> <br> この URL には、追加の認証なしで、サードパーティ製システムによる Runbook 呼び出しを可能にするためのセキュリティ トークンが含まれています。 その理由で、これはパスワードと同じように扱う必要があります。 セキュリティ上の理由から、Webhook の作成時に Azure ポータルで表示できるのは URL だけです。 将来の使用に備えて、URL を安全な場所にメモしてください。 |
| 有効期限 |証明書のように、各 Webhook には有効期限があり、それ以降は使用できなくなります。 この有効期限は、切れる前であれば、Webhook の作成後に変更できます。 |
| 有効 |既定では、Webhook は作成時に有効になります。 Disabled に設定した場合、クライアントはそれを使用できなくなります。 **Enabled** プロパティは、Webhook の作成時、または作成後はいつでも設定できます。 |

### <a name="parameters"></a>parameters

Webhook は、Runbook がその Webhook によって開始されたときに使用される Runbook のパラメーターの値を定義できます。 Webhook には、Runbook の任意の必須パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。 Webhook に対して構成されているパラメーター値は、Webhook の作成後であっても変更できます。 1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なるパラメーター値を使用することができます。

Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値をオーバーライドできません。 クライアントからデータを受け取るために、Runbook が **$WebhookData** という名前の単一のパラメーターを受け入れることができます。 このパラメーターの型は [object] で、これにはクライアントによって POST 要求に含められるデータが含まれます。

![Webhookdata プロパティ](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| WebhookName |Webhook の名前。 |
| RequestHeader |受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| RequestBody |受信 POST 要求の本文。 文字列、JSON、XML、フォームのエンコード済みデータなどの書式設定が保持されます。 想定されるデータ形式を操作するには、Runbook を記述する必要があります。 |

**$WebhookData** パラメーターのサポートに必要な Webhook の構成はありません。また、これを受け入れるために Runbook は必要ありません。 Runbook がパラメーターを定義していない場合は、クライアントから送信された要求の詳細は無視されます。

> [!NOTE]
> Webhook を呼び出すときは、呼び出しが失敗した場合に備えて、常にパラメーター値を格納しておく必要があります。 ネットワークの停止や接続の問題が発生した場合は、失敗した Webhook 呼び出しを取得することはできません。

webhook の作成時に $WebhookData の値を指定した場合、クライアントの要求本文にデータが含まれていなくても、クライアントの POST 要求からのデータで webhook が Runbook を開始した時点でその値はオーバーライドされます。 webhook 以外の方法を使用して $WebhookData が含まれる Runbook を開始する場合、Runbook で認識される $Webhookdata の値を指定することができます。 この値は $Webhookdata と同じ[プロパティ](#details-of-a-webhook)を持つオブジェクトにする必要があります。それにより、Runbook は、Webhook によって渡された実際の WebhookData を操作しているかのように適切な処理を実行できます。

たとえば、Azure portal から次の Runbook を開始するときにテスト用のサンプル WebhookData を渡す場合、WebhookData はオブジェクトであるため、UI に JSON として渡す必要があります。

![UI からの WebhookData パラメーター](media/automation-webhooks/WebhookData-parameter-from-UI.png)

次の Runbook で、WebhookData パラメーターに次のプロパティがあるとします。

* WebhookName:*MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

この場合は、次の JSON 値を WebhookData パラメーター用の UI に渡します。 復帰文字と改行文字がある次の例は、webhook から渡される形式に対応します。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI から WebhookData パラメーターを開始する](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> すべての入力パラメーターの値は、Runbook のジョブに記録されます。 つまり、Webhook の要求でクライアントから提供された入力はすべて記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。  このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。

## <a name="security"></a>セキュリティ

webhook のセキュリティは、呼び出しを許可するセキュリティ トークンが含まれる URL のプライバシーに依存します。 正しい URL に対して要求がなされる場合、Azure Automation はその要求に対してどの認証も実行しません。 このため、要求を検証する他の方法を使用しない場合は、機密性の高い機能を実行する Runbook で Webhook を使用しないでください。

Runbook 内にロジックを含め、$WebhookData パラメーターの **WebhookName** プロパティを調べることによって、Webhook で呼び出されたことを判別できます。 Runbook は、**RequestHeader** または **RequestBody** プロパティの特定の情報を探すことによって、追加の検証を実行できます。

別の方法として、Webhook 要求の受信時に外部条件のいくつかの検証を Runbook に実行させることです。 たとえば、GitHub リポジトリに新しいコミットがある場合は常に GitHub によって呼び出される Runbook について考えてみましょう。 Runbook は、新しいコミットが発生したことを検証するために、GitHub に接続してから、処理を続行することができます。

## <a name="creating-a-webhook"></a>Webhook の作成

次の手順を使用して、Runbook にリンクされた新しい Webhook を Azure ポータルに作成します。

1. Azure portal の **[Runbook] ページ**から、webhook がその詳細ページの表示を開始する Runbook をクリックします。 Runbook の **[状態]** が **[発行済み]** を確認します。
2. ページの上部にある **Webhook** をクリックして、 **[Webhook の追加]** ページを開きます。
3. **[新しい Webhook の作成]** をクリックして、 **[webhook の作成] ページ**を開きます。
4. Webhook の**名前**、**有効期限**、およびそれを有効にする必要があるかどうかを指定します。 これらのプロパティの詳細については、「 [Webhook の詳細](#details-of-a-webhook) 」を参照してください。
5. コピー アイコンをクリックし、Ctrl + C キーを押して Webhook の URL をコピーします。 次に、それを安全な場所に記録します。 **Webhook を作成したら、再度 URL を取得することはできません。**

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. **[パラメーター]** をクリックし、Runbook のパラメーターの値を指定します。 Runbook に必須のパラメーターがある場合、値を指定しない限り webhook を作成できません。
1. **[作成]** をクリックして Webhook を作成します。

## <a name="using-a-webhook"></a>Webhook の使用

Webhook を作成後に使用する場合、クライアント アプリケーションは Webhook の URL で HTTP POST を発行する必要があります。 webhook の構文は、次の形式になります。

```http
http://<Webhook Server>/token?=<Token Value>
```

クライアントは、POST 要求から次のリターン コードのいずれかを受け取ります。

| コード | Text | 説明 |
|:--- |:--- |:--- |
| 202 |承認済み |要求が承認され、Runbook が正常にキューに入れられました。 |
| 400 |正しくない要求 |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook の有効期限が切れている。</li> <li>webhook が無効になっている。</li> <li>URL 内のトークンが無効になっている。</li>  </ul> |
| 404 |見つかりません |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook が見つからない。</li> <li>Runbook が見つからない。</li> <li>アカウントが見つからない。</li>  </ul> |
| 500 |内部サーバー エラー |URL は有効ですが、エラーが発生しました。 要求を再送信してください。 |

要求が成功したと仮定した場合、Webhook の応答には、次のような JSON 形式のジョブ ID が含まれています。 ここに含まれるジョブ ID は 1 つですが、JSON 形式は将来拡張できるようになっています。

```json
{"JobIds":["<JobId>"]}
```

Runbook ジョブの完了時期と Webhook からの完了状態は、クライアントからは判別できません。 この情報は、[Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) や [Azure Automation API](/rest/api/automation/job) などの方法でジョブ ID を使用すると判別できます。

## <a name="renew-webhook"></a> Webhook を更新する

Webhook が作成された時点での有効期間は 1 年です。 その日時が過ぎると、Webhook は自動的に期限切れになります。 有効期限が切れた Webhook は再アクティブ化できず、削除して作成しなおす必要があります。 Webhook が有効期限に達していない場合は延長できます。

Webhook を延長するには、その Webhook を含む Runbook に移動します。 **[リソース]** で **[Webhooks]** を選択します。 延長する Webhook をクリックすると、 **[Webhook]** ページが開きます。  新しい有効期限の日付と時刻を選択して、 **[保存]** をクリックします。

## <a name="sample-runbook"></a>サンプル Runbook

次の例の Runbook では、webhook データを受け入れ、要求本文で指定された仮想マシンを起動します。 この Runbook をテストするには、Automation アカウントの **Runbook** で、 **[+ Runbook の追加]** をクリックします。 Runbook を作成する方法がわからない場合は、[Runbook の作成](automation-quickstart-create-runbook.md)に関する記事をご覧ください。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>例をテストする

次の例では、Windows PowerShell を使用して Webhook で Runbook を開始します。 webhook は、HTTP 要求を実行できる任意の言語で使用できます。ここでは、例として Windows PowerShell を使用します。

Runbook では、要求の本文に JSON 形式の仮想マシン一覧が必要です。 Runbook では、Webhook の呼び出し元が有効であることを検証するために定義されたメッセージがヘッダーに含まれることも検証されます。

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

次の例は、**WebhookData** の **RequestBody** プロパティで Runbook に利用できる要求の本文を示しています。 要求の本文に含まれていた形式が JSON であったため、この値の形式は JSON になります。

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

次の図に、Windows PowerShell から送信される要求と、結果の応答を示します。 ジョブ ID が応答から抽出され、文字列に変換されます。

![Webhook ボタン](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>次の手順

* Azure Automation を使用して Azure アラートに対処する方法については、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」をご覧ください。

