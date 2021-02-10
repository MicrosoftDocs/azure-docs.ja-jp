---
title: Webhook から Azure Automation の Runbook を開始する
description: この記事では、Webhook を使用して、HTTP 呼び出しから Azure Automation の Runbook を開始する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: df19f32be41b17e13a9da575e828830e29da4e55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894764"
---
# <a name="start-a-runbook-from-a-webhook"></a>webhook から Runbook を開始する

Webhook を使用すると、外部サービスは、1 つの HTTP 要求により、Azure Automation で特定の Runbook を開始することができます。 外部サービスには、Azure DevOps Services、GitHub、Azure Monitor ログ、およびカスタム アプリケーションが含まれます。 このようなサービスでは、Webhook を使用することで、完全な Azure Automation API を実装せずに、Runbook を開始できます。 Webhook と、Runbook の他の 開始方法を比較するには、「[Azure Automation で Runbook を開始する](./start-runbooks.md)」を参照してください。

> [!NOTE]
> Webhook を使用して Python Runbook を開始することはできません。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Webhook を使用する TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2 の強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)」のセクションを参照してください。

## <a name="webhook-properties"></a>Webhook のプロパティ

次のテーブルは、Webhook 用に構成する必要があるプロパティについて説明しています。

| プロパティ | 説明 |
|:--- |:--- |
| 名前 |Webhook の名前。 これはクライアントに公開されないため、任意の名前を指定できます。 これはユーザーが Azure Automation の Runbook を識別する場合にのみ使用されます。 ベスト プラクティスとして、webhook を使用するクライアントに関連した名前を webhook に付ける必要があります。 |
| URL |Webhook の URL。 これは、Webhook にリンクされた Runbook を開始するためにクライアントが HTTP POST で呼び出す一意のアドレスです。 これは、Webhook を作成するときに自動的に生成されます。 カスタム URL を指定することはできません。 <br> <br> この URL には、サードパーティのシステムが追加の認証なしに Runbook を呼び出すことを許可するセキュリティ トークンが含まれています。 このため、この URL はパスワードのように扱う必要があります。 セキュリティ上の理由により、この URL は、Webhook を作成するときに Azure portal でのみ表示できます。 将来の使用に備えて、URL を安全な場所にメモしてください。 |
| 有効期限 | Webhook の有効期限。これを過ぎると使用できなくなります。 この有効期限は、Webhook の有効期限が切れていない限り、Webhook の作成後に変更できます。 |
| Enabled | Webhook が既定で作成時に有効になるかどうかを示す設定。 このプロパティを [Disabled]\(無効\) に設定すると、クライアントは Webhook を使用できません。 このプロパティは、Webhook の作成時、または作成後にいつでも設定できます。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook が Runbook を開始するときに使用されるパラメーター

Webhook は、Runbook の開始時に使用される Runbook パラメーターの値を定義できます。 Webhook には、必須の Runbook パラメーターの値を含める必要があり、省略可能なパラメーターの値を含めることもできます。 Webhook に対して構成されているパラメーター値は、Webhook の作成後であっても変更できます。 1 つの Runbook にリンクされている複数の Webhook は、それぞれ異なる Runbook パラメーター値を使用できます。 Webhook を使用して Runbook を開始した場合、クライアントは Webhook で定義されているパラメーターの値をオーバーライドできません。

クライアントからデータを受け取るために、Runbook では `WebhookData` という名前の 1 つのパラメーターがサポートされています。 このパラメーターは、クライアントが POST 要求に含めるデータを格納するオブジェクトを定義します。

![WebhookData のプロパティ](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` パラメーターには、次のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `WebhookName` | Webhook の名前。 |
| `RequestHeader` | 受信 POST 要求のヘッダーを含むハッシュ テーブル。 |
| `RequestBody` | 受信 POST 要求の本文。 この本文には、文字列、JSON、XML、form-encoded など、データのすべての書式設定が保持されます。 想定されるデータ形式を操作するには、Runbook を記述する必要があります。 |

`WebhookData` パラメーターをサポートするために必要な Webhook の構成はなく、Runbook はこれを受け入れる必要ありません。 Runbook がこのパラメーターを定義していない場合、クライアントから送信された要求の詳細はすべて無視されます。

> [!NOTE]
> Webhook を呼び出すときは、呼び出しが失敗した場合に備えて、クライアントは常にパラメーター値を格納しておく必要があります。 ネットワークの停止や接続の問題がある場合、アプリケーションは、失敗した Webhook 呼び出しを取得できません。

Webhook 作成時に `WebhookData` の値を指定すると、それは、クライアントの POST 要求のデータで Webhook が Runbook を開始したときに上書きされます。 これは、アプリケーションが要求本文にデータを含まない場合でも発生します。 

Webhook 以外のメカニズムを使用して `WebhookData` を定義する Runbook を開始する場合は、Runbook が認識する `WebhookData` の値を指定できます。 この値は、`WebhookData` パラメーターと同じ[プロパティ](#webhook-properties)を持つオブジェクトである必要があります。これにより、Runbook は、Webhook によって渡される実際の `WebhookData` オブジェクトと同様にそれを扱うことができます。

たとえば、Azure portal から次の Runbook を開始するときにテスト用のサンプル Webhook データを渡す場合は、そのデータをユーザー インターフェイスで JSON として渡す必要があります。

![UI からの WebhookData パラメーター](media/automation-webhooks/WebhookData-parameter-from-UI.png)

次の Runbook の例では、`WebhookData` の以下のプロパティを定義します。

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

次に、`WebhookData` パラメーターに対して UI で以下の JSON オブジェクトを渡します。 この例は、復帰文字と改行文字を使用しており、Webhook から渡される形式と一致します。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI から WebhookData パラメーターを開始する](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation は、Runbook ジョブにすべての入力パラメーターの値を記録します。 したがって、Webhook 要求でクライアントが提供した入力はすべてログに記録され、Automation ジョブにアクセスできるすべてのユーザーが使用できます。 このため、Webhook の呼び出しに機密情報を含める場合には注意する必要があります。

## <a name="webhook-security"></a>Webhook のセキュリティ

Webhook のセキュリティは、Webhook の呼び出しを許可するセキュリティ トークンを含む URL のプライバシーに依存します。 Azure Automation は、正しい URL に対して要求が行われている限り、いかなる認証も実行しません。 この理由により、クライアントは、要求を検証する代替方法を使用せずに機密性の高い操作を実行する Runbook には Webhook を使用しないようにする必要があります。

次の戦略を考えます。

* Webhook で呼び出すかどうかを判断するためのロジックを Runbook 内に含めることができます。 `WebhookData` パラメーターの `WebhookName` プロパティを Runbook に検査させます。 Runbook は、`RequestHeader` と `RequestBody` のプロパティで特定の情報を検索することにより、追加の検証を実行できます。

* Webhook 要求の受信時に外部条件の検証を Runbook に実行させます。 たとえば、GitHub リポジトリへの新しいコミットが発生するたびに GitHub によって呼び出される Runbook について考えてみましょう。 Runbook は、GitHub に接続して、新しいコミットが発生したことを確認してから続行することができます。

* Azure Automation では、Azure 仮想ネットワーク サービス タグ、特に [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) がサポートされています。 サービス タグを使用して、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義し、仮想ネットワーク内から Webhook をトリガーできます。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 **GuestAndHybridManagement** を指定することで、Automation サービスのトラフィックを許可または拒否できます。 このサービス タグでは、IP 範囲を特定のリージョンに制限することによる、より詳細な制御はサポートされていません。

## <a name="create-a-webhook"></a>webhook を作成する

次の手順を使用して、Runbook にリンクされた新しい Webhook を Azure ポータルに作成します。

1. Azure portal の [Runbook] ページから、Webhook が開始する Runbook をクリックして、Runbook の詳細を表示します。 Runbook の **[状態]** フィールドが、 **[Published]\(公開済み\)** に設定されていることを確認します。
2. ページ上部にある **[Webhook]** をクリックして、[Webhook の追加] ページを開きます。
3. **[新しい Webhook の作成]** をクリックして、[webhook の作成] ページを開きます。
4. Webhook の **[名前]** と **[有効期限]** のフィールドに入力し、それを有効にするかどうかを指定します。 これらのプロパティの詳細については、「[Webhook のプロパティ](#webhook-properties)」を参照してください。
5. コピー アイコンをクリックし、Ctrl + C キーを押して Webhook の URL をコピーします。 次に、それを安全な場所に記録します。 

    > [!NOTE]
    > Webhook を作成した後は、その URL を再取得することはできません。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. **[パラメーター]** をクリックし、Runbook のパラメーターの値を指定します。 Runbook に必須のパラメーターがある場合、値を指定しない限り、Webhook を作成することはできません。

2. **[作成]** をクリックして Webhook を作成します。

## <a name="use-a-webhook"></a>Webhook を使用する

Webhook を作成後に使用するには、クライアントはその Webhook の URL を使用して HTTP `POST` 要求を発行する必要があります。 の構文は次のとおりです。

```http
http://<Webhook Server>/token?=<Token Value>
```

クライアントは、`POST` 要求から、次のいずれかのリターン コードを受け取ります。

| コード | Text | 説明 |
|:--- |:--- |:--- |
| 202 |承認済み |要求が承認され、Runbook が正常にキューに入れられました。 |
| 400 |正しくない要求 |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook の有効期限が切れている。</li> <li>webhook が無効になっている。</li> <li>URL 内のトークンが無効になっている。</li>  </ul> |
| 404 |見つかりません |次のいずれかの理由で要求が受け入れられませんでした。 <ul> <li>webhook が見つからない。</li> <li>Runbook が見つからない。</li> <li>アカウントが見つからない。</li>  </ul> |
| 500 |内部サーバー エラー |URL は有効ですが、エラーが発生しました。 要求を再送信してください。 |

要求が成功したと仮定した場合、Webhook の応答には、次に示すように JSON 形式のジョブ ID が含まれます。 ここに含まれるジョブ ID は 1 つですが、JSON 形式では将来の拡張が可能です。

```json
{"JobIds":["<JobId>"]}
```

Runbook ジョブの完了時期と Webhook からの完了状態は、クライアントからは判別できません。 この情報は、[Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) や [Azure Automation API](/rest/api/automation/job) などの別のメカニズムでジョブ ID を使用して確認できます。

## <a name="renew-a-webhook"></a>Webhook を更新する

Webhook が作成されると、有効期間が 10 年に設定され、それが経過すると自動的に有効期限が切れます。 有効期限が切れた Webhook は、再アクティブ化できません。 削除してから再作成するしかありません。 

有効期限に達していない Webhook は延長できます。 Webhook を延長するには、次のようにします。

1. Webhook が含まれている Runbook に移動します。 
2. **[リソース]** で **[Webhooks]** を選択します。 
3. 延長する Webhook をクリックします。 
4. Webhook のページで新しい有効期限の日付と時刻を選択し、 **[保存]** をクリックします。

## <a name="sample-runbook"></a>サンプル Runbook

次の例の Runbook では、webhook データを受け入れ、要求本文で指定された仮想マシンを起動します。 この Runbook をテストするには、Automation アカウントの **[Runbook]** で、 **[Runbook の作成]** をクリックします。 Runbook を作成する方法がわからない場合は、[Runbook の作成](automation-quickstart-create-runbook.md)に関する記事をご覧ください。

> [!NOTE]
> 非グラフィカル PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) のエイリアスです。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>サンプルをテストする

次の例では、Windows PowerShell を使用して Webhook で Runbook を開始します。 HTTP 要求を行うことができるすべての言語で、Webhook を使用できます。 ここでは、例として Windows PowerShell を使用します。

Runbook では、要求の本文に JSON 形式の仮想マシン一覧が必要です。 Runbook では、Webhook の呼び出し元が有効であることを検証するために、定義されたメッセージがヘッダーに含まれていることも検証されます。

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

次の例は、`WebhookData` の `RequestBody` プロパティで Runbook で使用できる要求の本文を示しています。 この値は、要求の本文に含まれている形式と互換性があるように JSON で書式設定されています。

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

## <a name="next-steps"></a>次のステップ

* アラートから Runbook をトリガーするには、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」を参照してください。