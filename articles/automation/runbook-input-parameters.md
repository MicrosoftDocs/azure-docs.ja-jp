---
title: Azure Automation における Runbook の入力パラメーターを構成する
description: Runbook には、その開始時に、入力パラメーターを通じてデータを渡すことができます。この記事では、Runbook の入力パラメーターを構成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 73e4dbb24b4e7c0c651f7d082c75b0f4a17158b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890883"
---
# <a name="configure-runbook-input-parameters"></a>Runbook の入力パラメーターを構成する

Runbook の入力パラメーターを使用すると、開始時にデータを渡すことができるため、Runbook の柔軟性が向上します。 これらのパラメーターにより、Runbook アクションの対象を特定のシナリオや環境に設定できます。 この記事では、Runbook での入力パラメーターの構成と使用について説明します。

PowerShell、PowerShell Workflow、グラフィカル、および Python の Runbook の入力パラメーターを構成できます。 Runbook にはデータ型の異なる複数のパラメーターを指定することも、パラメーターをまったく指定しないこともできます。 入力パラメーターには必須と任意があります。任意のパラメーターには既定値を使用できます。

Runbook の入力パラメーターは、それを起動するときに値を割り当てます。 Runbook は、Azure portal、Web サービス、または PowerShell から開始できます。 また、別の Runbook のインラインで呼び出される子 Runbook として Runbook を起動することもできます。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>PowerShell Runbook の入力パラメーターを構成する

Azure Automation の PowerShell と PowerShell Workflow Runbook では、次のプロパティで定義されている入力パラメーターがサポートされます。 

| **プロパティ** | **説明** |
|:--- |:--- |
| Type |必須。 パラメーター値に必要なデータ型。 .NET 型はすべて有効です。 |
| 名前 |必須。 パラメーターの名前。 この名前は Runbook 内で一意にする必要があります。また、文字で始まる必要があり、文字、数字、またはアンダースコア文字のみを含めることができます。 |
| Mandatory |省略可能。 パラメーターに値が必要かどうかを指定するブール値。 これを True に設定した場合、Runbook の起動時に値を指定する必要があります。 これを False に設定した場合、値は任意になります。 `Mandatory` プロパティの値を指定しない場合、PowerShell では既定で入力パラメーターが任意と見なします。 |
| 既定値 |省略可能。 Runbook の起動時に入力値が渡されない場合にパラメーターに使用される値。 Runbook では、任意のパラメーターの既定値を設定できます。 |

Windows PowerShell では、検証、エイリアス、パラメーター セットなど、上記のものよりも多くの入力パラメーターの属性がサポートされています。 しかし、Azure Automation では、現在のところ、一覧にある入力パラメーター プロパティのみがサポートされています。

例として、PowerShell Workflow Runbook のパラメーター定義を見てみましょう。 この定義には、次の一般的な形式があります。複数のパラメーターはコンマで区切ります。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

ここで、仮想マシン (1 台の VM またはリソース グループ内の全 VM) の詳細を出力する PowerShell Workflow Runbook の入力パラメーターを構成します。 この Runbook には、次のスクリーンショットのとおり、仮想マシン名 (`VMName`) とリソース グループ名 (`resourceGroupName`) の 2 つのパラメーターがあります。

![Automation PowerShell ワークフロー](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

このパラメーター定義では、入力パラメーターは文字列型の単純なパラメーターです。

PowerShell と PowerShell Workflow Runbook では、`Object` や `PSCredential` などのすべての単純型と複合型の入力パラメーターをサポートしていることに留意してください。 Runbook にオブジェクト入力パラメーターがある場合は、名前と値のペアを持つ PowerShell ハッシュテーブルを使用して値を渡す必要があります。 たとえば、Runbook に次のパラメーターがあるとします。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

この場合、パラメーターに次の値を渡すことができます。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> null の既定値を指定して省略可能な文字列パラメーターに値を渡さない場合、パラメーターの値は Null ではなく空の文字列になります。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>グラフィカル Runbook の入力パラメーターを構成する

グラフィカル Runbook の入力パラメーターの構成を説明するために、仮想マシン (1 台の VM またはリソース グループ内の全 VM) の詳細を出力する Runbook を作成します。 詳細については、「[初めてのグラフィカルな Runbook](./learn/automation-tutorial-runbook-graphical.md)」を参照してください。

グラフィカル Runbook では、次の主要な Runbook アクティビティが使用されます。

* Azure で認証するための Azure 実行アカウントの構成。 
* VM プロパティを取得する [Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットの定義。
* [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) アクティビティの使用による VM 名の出力。 

`Get-AzVM` アクティビティでは、VM 名とリソース グループ名の 2 つの入力を定義します。 これらの名前は Runbook を開始するたびに異なる可能性があるため、これらの入力を受け入れるには、Runbook に入力パラメーターを追加する必要があります。 「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。

入力パラメーターを構成するには、次の手順に従います。

1. [Runbook] ページからグラフィカル Runbook を選択し、 **[編集]** をクリックします。
2. グラフィカル エディターで、 **[入力と出力]** ボタンをクリックし、次に **[入力の追加]** をクリックして、[Runbook 入力パラメーター] ペインを開きます。

   ![Automation graphical runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. [入力と出力] コントロールには、Runbook に定義されている入力パラメーターの一覧が表示されます。 ここで、新しい入力パラメーターを追加したり、既存の入力パラメーターの構成を編集したりすることができます。 Runbook に新しいパラメーターを追加するには、 **[入力の追加]** をクリックして **[Runbook 入力パラメーター]** ブレードを開きます。ここで、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」で定義されているプロパティを使用してパラメーターを構成できます。

    ![Add new input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. `Get-AzVM` アクティビティで使用される 2 つのパラメーターを次のプロパティで作成し、 **[OK]** をクリックします。

   * パラメーター 1:
        * **名前** -- **VMName**
        * **型** -- 文字列
        * **必須** -- **いいえ**

   * パラメーター 2:
        * **名前** -- **resourceGroupName**
        * **型** -- 文字列
        * **必須** -- **いいえ**
        * **既定値** -- **カスタム**
        * カスタム既定値 -- VM を含むリソース グループの名前

5. [入力と出力] コントロールでパラメーターを確認します。 
6. もう一度 **[OK]** をクリックし、次に **[保存]** をクリックします。
7. **[発行]** をクリックして、Runbook を発行します。

### <a name="configure-input-parameters-in-python-runbooks"></a>Python Runbook の入力パラメーターを構成する

PowerShell、PowerShell Workflow、およびグラフィカル Runbook とは異なり、Python Runbook は名前付きパラメーターを取りません。 Runbook エディターでは、すべての入力パラメーターは引数値の配列として解析されます。 `sys` モジュールをお使いの Python スクリプトにインポートし、`sys.argv` 配列を使用することで、配列にアクセスできます。 配列の最初の要素 `sys.argv[0]` はスクリプトの名前であることに注意してください。 したがって、最初の実際の入力パラメーターは `sys.argv[1]` です。

Python Runbook で入力パラメーターを使用する方法の例は、「[My first Python runbook in Azure Automation (初めての Azure Automation の Python Runbook)](./learn/automation-tutorial-runbook-textual-python2.md)」をご覧ください。

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Runbook の入力パラメーターに値を割り当てる

このセクションでは、Runbook の入力パラメーターに値を渡すいくつかの方法について説明します。 次の場合にパラメーター値を割り当てることができます。

* [Runbook を開始する](#start-a-runbook-and-assign-parameters)
* [Runbook をテストする](#test-a-runbook-and-assign-parameters)
* [Runbook のスケジュールをリンクする](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Runbook に対する Webhook を作成する](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook を起動し、パラメーターを割り当てる

Runbook は、Azure portal、Webhook、PowerShell コマンドレット、REST API、SDK など、さまざまな方法で起動できます。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Azure portal を使用して公開済み Runbook を起動し、パラメーターを割り当てる

Azure portal で [Runbook を起動する](start-runbooks.md#start-a-runbook-with-the-azure-portal)と、 **[Runbook の開始]** ブレードが開き、作成したパラメーターの値を入力できます。

![Start using the portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

入力ボックスの下にあるラベルでは、パラメーター属性を定義するために設定されたプロパティ (必須/任意、型、既定値など) を確認できます。 パラメーター名の横にあるヘルプ バルーンでは、パラメーター入力値を決定するために必要となる重要な情報も定義されています。 

> [!NOTE]
> 文字列パラメーターでは、文字列型の空の値がサポートされています。 入力パラメーター ボックスに「`[EmptyString]`」と入力すると、空の文字列がパラメーターに渡されます。 また、文字列パラメーターでは Null がサポートされていません。 文字列パラメーターに何の値も渡さないと、PowerShell はそれを Null として解釈します。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell コマンドレットを使用して公開済み Runbook を起動し、パラメーターを割り当てる

* **Azure Resource Manager コマンドレット:** [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) を使用して、リソース グループ内に作成された Automation Runbook を起動できます。

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure クラシック デプロイ モデルのコマンドレット:** [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook) を使用して、既定のリソース グループ内に作成された Automation Runbook を起動できます。
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> PowerShell コマンドレットを使用して Runbook を起動すると、既定のパラメーター `MicrosoftApplicationManagementStartedBy` が値 `PowerShell` で作成されます。 このパラメーターは [ジョブの詳細] ペインで表示できます。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>SDK を使用して Runbook を起動し、パラメーターを割り当てる

* **Azure Resource Manager メソッド:** :プログラミング言語の SDK を使用して Runbook を起動できます。 以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。 完全なコードは、 [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)にあります。

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure クラシック デプロイ モデル メソッド:** プログラミング言語の SDK を利用して Runbook を起動できます。 以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。 完全なコードは、 [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)にあります。

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   このメソッドを開始するには、Runbook パラメーターの `VMName` および `resourceGroupName` とそれらの値を保存するディクショナリを作成します。 次に、Runbook を起動します。 以下は、上で定義したメソッドを呼び出す C# コード スニペットです。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>REST API を使用して Runbook を起動し、パラメーターを割り当てる

`PUT` メソッドと次の要求 URI を使用することで、Azure Automation REST API で Runbook ジョブを作成し、起動できます。`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

要求 URI で、次のパラメーターを置き換えます。

* `subscriptionId`:お使いの Azure サブスクリプション ID。  
* `resourceGroupName`:Automation アカウントのリソース グループの名前。
* `automationAccountName`:指定したクラウド サービス内でホストされている Automation アカウントの名前。  
* `jobName`:ジョブの GUID。 PowerShell の GUID は `[GUID]::NewGuid().ToString()*` を使用して作成できます。

Runbook ジョブにパラメーターを渡すには、要求本文を使用します。 これは、JSON 形式で提供される次の情報を受け取ります。

* Runbook 名:必須。 開始するジョブの Runbook の名前。  
* Runbook のパラメーター:省略可能。 (名前、値) 形式のパラメーター リストのディクショナリ。名前は文字列型にする必要があります。値には任意の有効な JSON 値を指定できます。

`VMName` と `resourceGroupName` をパラメーターとする作成済みの **Get-AzureVMTextual** Runbook を起動するには、要求本文に次の JSON 形式を使用します。

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

ジョブが正常に作成されると、HTTP 状態コード 201 が返されます。 応答ヘッダーと応答本文の詳細については、[REST API を使用した Runbook ジョブの作成](/rest/api/automation/job/create)に関する記事を参照してください。

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook をテストし、パラメーターを割り当てる

テスト オプションを使用して、[ご自分の Runbook のドラフト バージョンをテスト](./manage-runbooks.md)する場合、[テスト] ページが開きます。 このページを使用して、作成したパラメーターの値を構成します。

![Test and assign parameters](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>スケジュールを Runbook にリンクし、パラメーターを割り当てる

Runbook を特定の時刻に開始するために、Runbook に[スケジュールをリンク](./shared-resources/schedules.md)できます。 スケジュールを作成するときに、入力パラメーターを割り当てます。スケジュールで Runbook が起動されるときに、それらの値が使用されます。 必須のパラメーター値をすべて指定しないと、スケジュールは保存できません。

![Schedule and assign parameters](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook の Webhook を作成し、パラメーターを割り当てる

Runbook の [Webhook](automation-webhooks.md) を作成し、Runbook 入力パラメーターを構成できます。 必須のパラメーター値をすべて指定しないと、Webhook は保存できません。

![Create webhook and assign parameters](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Webhook を使用して Runbook を実行すると、定義済みの入力パラメーター `[WebhookData](automation-webhooks.md)` が、定義した入力パラメーターと共に送信されます。 

![WebhookData parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Runbook に JSON オブジェクトを渡す

Runbook に渡すデータを JSON ファイルに格納すると便利な場合があります。 たとえば、Runbook に渡すすべてのパラメーターを含む JSON ファイルを作成できます。 これを行うには、JSON コードを文字列に変換し、その文字列を PowerShell オブジェクトに変換してから、それを Runbook に渡す必要があります。

このセクションでは、 PowerShell スクリプトで [Start-AzAutomationRunbookk](/powershell/module/az.automation/start-azautomationrunbook) を呼び出して PowerShell Runbook を起動し、JSON ファイルの内容を Runbook に渡す例を使用します。 PowerShell Runbook は、JSON オブジェクトから VM のパラメーターを取得することで、Azure VM を起動します。

### <a name="create-the-json-file"></a>JSON ファイルの作成

テキスト ファイルに次のコードを入力し、それをご自分のローカル コンピューター上のどこかに **test.json** として保存します。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Runbook の作成

Azure Automation で **Test-Json** という名前の新しい PowerShell Runbook を作成します。 「[初めての PowerShell Runbook](./learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。

JSON データを受け入れるには、Runbook は、入力パラメーターとしてオブジェクトを受け取る必要があります。 これにより、Runbook は JSON ファイルで定義されたプロパティを使用できるようになります。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Automation アカウントでこの Runbook を保存して公開します。

### <a name="call-the-runbook-from-powershell"></a>PowerShell からの Runbook の呼び出し

Azure PowerShell を使用して、ローカル コンピューターから Runbook を呼び出すことができるようになりました。 

1. 示されているように Azure にサインインします。 その後、Azure 資格情報を入力するよう求められます。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらの別名は、グラフィカル Runbook では使用できないことに注意してください。 グラフィカルな Runbook で使用できるのは、`Connect-AzAccount` 自体のみです。

1. 保存された JSON ファイルの内容を取得して文字列に変換します。 `JsonPath` は、JSON ファイルを保存した場所へのパスです。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. `$json` の文字列の内容を PowerShell オブジェクトに変換します。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. `Start-AzAutomationRunbook` のパラメーターのハッシュテーブルを作成します。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   JSON ファイルの値を含む PowerShell オブジェクトに `Parameters` の値を設定していることに注意してください。
1. Runbook を開始します。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>次のステップ

* テキスト形式の Runbook の準備については、「[Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
* グラフィカル Runbook の準備については、[Azure Automation でのグラフィカル Runbook の作成](automation-graphical-authoring-intro.md)に関する記事を参照してください。
