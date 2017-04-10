---
title: "Runbook の入力パラメーター | Microsoft Docs"
description: "Runbook の入力パラメーターを利用すれば、開始時に Runbook にデータを渡すことができて Runbook の柔軟性が上がります。 この記事では、入力パラメーターを Runbook で使用するさまざまなシナリオについて説明します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 4d3dff2c-1f55-498d-9a0e-eee497e5bedb
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0f8308b73a70fc3758a53063bc69d16480df8f02


---
# <a name="runbook-input-parameters"></a>Runbook の入力パラメーター
Runbook の入力パラメーターを利用すれば、開始時にデータを渡すことができて Runbook の柔軟性が上がります。 パラメーターにより、Runbook アクションの対象を特定のシナリオや環境に設定できます。 この記事では、入力パラメーターを Runbook で使用するさまざまなシナリオを紹介します。

## <a name="configure-input-parameters"></a>入力パラメーターを構成する
入力パラメーターは PowerShell、PowerShell Workflow、グラフィカル Runbook で構成できます。 Runbook にはデータ型の異なる複数のパラメーターを指定することも、パラメーターをまったく指定しないこともできます。 入力パラメーターには必須と任意があります。任意のパラメーターには既定値を割り当てることができます。 Runbook の入力パラメーターには、利用可能ななんらかの方法で Runbook を起動するときに、値を割り当てることができます。 たとえば、ポータルや Web サービスから Runbook を起動することができます。 また、別の Runbook のインラインで呼び出される子 Runbook として Runbook を起動することもできます。

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>PowerShell と PowerShell Workflow の Runbook で入力パラメーターを構成する
Azure Automation の PowerShell Runbook と [PowerShell Workflow Runbook](automation-first-runbook-textual.md) では、次の属性で定義されている入力パラメーターを利用できます。  

| **プロパティ** | **説明** |
|:--- |:--- |
| 型 |必須。 パラメーター値に必要なデータ型。 .NET 型はすべて有効です。 |
| 名前 |必須。 パラメーターの名前。 これは Runbook 内で一意にする必要があります。文字、数字、アンダースコアのみで作成する必要があります。 先頭は文字でなければなりません。 |
| 必須 |省略可能。 パラメーターの値を指定する必要があるかどうかを示します。 これを **$true**に設定した場合、Runbook の起動時に値を指定する必要があります。 これを **$false**に設定した場合、値は任意になります。 |
| 既定値 |省略可能。  Runbook の起動時に値が渡されない場合にパラメーターに使用する値を指定します。 既定値はすべてのパラメーターに設定可能であり、必須設定に関係なく、自動的にパラメーターを任意にします。 |

Windows PowerShell では、検証、エイリアス、パラメーター セットなど、ここに記載されているものより多くの入力パラメーター属性がサポートされています。 しかし、Azure Automation では、現在のところ、上記の入力パラメーターだけがサポートされています。

PowerShell Workflow Runbook のパラメーター定義には次の一般形式があります。複数のパラメーターはコンマで分けられます。

   ```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> パラメーターを定義するとき、 **Mandatory** 属性を指定しない場合、既定でパラメーターは任意と見なされます。 また、PowerShell Workflow Runbook のパラメーターに既定値を設定した場合、 **Mandatory** 属性値に関係なく、PowerShell は任意のパラメーターとして扱います。
> 
> 

例として、PowerShell Workflow Runbook に仮想マシン (1 台の VM またはリソース グループ内の全 VM) の詳細を出力する入力パラメーターを構成します。 下のスクリーンショットでわかるように、この Runbook には 2 つのパラメーターがあります。仮想マシンの名前とリソース グループの名前です。

![Automation PowerShell ワークフロー](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

このパラメーター定義では、**$VMName** と **$resourceGroupName** は文字列型の単純なパラメーターです。 しかし、PowerShell と PowerShell Workflow Runbook では、入力パラメーターとして **object** や **PSCredential** など、すべての単純型と複合型がサポートされています。

Runbook に object 型の入力パラメーターが含まれている場合は、値を渡すために、(name,value) の組み合わせで PowerShell ハッシュテーブルを使用します。 たとえば、Runbook に次のパラメーターがある場合、

     [Parameter (Mandatory = $true)]
     [object] $FullName

パラメーターに次の値を渡すことができます。

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>グラフィカル Runbook の入力パラメーターを構成する
入力パラメーターで [グラフィカル Runbook を構成](automation-first-runbook-graphical.md) するために、仮想マシン (1 台の VM またはリソース グループ内の全 VM) の詳細を出力するグラフィカル Runbook を作成します。 Runbook の構成は、以下で説明するように、2 つの主要なアクティビティから成ります。

[**Azure 実行アカウントを使用した Runbook の認証**](automation-sec-configure-azure-runas-account.md)で、Azure 認証します。

[**Get-AzureRmVm**](https://msdn.microsoft.com/library/mt603718.aspx)で、仮想マシンのプロパティを取得します。

[**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) アクティビティを利用し、仮想マシンの名前を出力できます。 **Get-AzureRmVm** アクティビティは 2 つのパラメーターを受け取ります。**仮想マシンの名前** と **リソース グループの名前**です。 これらのパラメーターは Runbook を起動するたびに異なる値を必要とする可能性があるため、入力パラメーターを Runbook に追加できます。 以下は入力パラメーターを追加する手順です。

1. **[Runbook]** ブレードからグラフィカル Runbook を選択し、[**[編集]**](automation-graphical-authoring-intro.md) します。
2. Runbook エディタで **[入力と出力]** をクリックし、**[入力と出力]** ブレードを開きます。
   
    ![Automation graphical runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. **[入力と出力]** ブレードには、Runbook に定義されている入力パラメーターの一覧が表示されます。 このブレードで、新しい入力パラメーターを追加したり、既存の入力パラメーターの構成を編集したりすることができます。 Runbook に新しいパラメーターを追加するには、**[入力の追加]** をクリックし、**[Runbook 入力パラメーター]** ブレードを開きます。 このブレードで、次のパラメーターを構成できます。
   
   | **プロパティ** | **説明** |
   |:--- |:--- |
   | 名前 |必須。  パラメーターの名前。 これは Runbook 内で一意にする必要があります。文字、数字、アンダースコアのみで作成する必要があります。 先頭は文字でなければなりません。 |
   | Description |省略可能。 入力パラメーターの目的に関する説明。 |
   | 型 |省略可能。 パラメーター値のデータ型。 サポートされているパラメーター型は **String**、**Int32**、**Int64**、**Decimal**、**Boolean**、**DateTime**、**Object**です。 データ型が選択されていない場合、既定値は **String**になります。 |
   | 必須 |省略可能。 パラメーターの値を指定する必要があるかどうかを示します。 **[はい]** を選択した場合、Runbook の起動時に値を指定する必要があります。 **[いいえ]** を選択した場合、Runbook の起動時に値は必要ありません。既定値が設定されます。 |
   | 既定値 |省略可能。 Runbook の起動時に値が渡されない場合にパラメーターに使用する値を指定します。 必須ではないパラメーターに既定値を設定できます。 既定値を設定するには、 **[カスタム]**を選択します。 Runbook の起動時に別の値を指定しない限り、この値が使用されます。 既定値を指定しない場合、 **[なし]** を選択します。 |
   
    ![Add new input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. **Get-AzureRmVm** アクティビティで使用される 2 つのパラメーターを次のプロパティで作成します。
   
   * **パラメーター 1:**
     
     * 名前: VMName
     * 型: 文字列
     * 必須: いいえ
   * **パラメーター 2:**
     
     * 名前: resourceGroupName
     * 型: 文字列
     * 必須: いいえ
     * 既定値: カスタム
     * カスタム既定値: \<仮想マシンを含むリソース グループの名前>
5. パラメーターを追加したら、 **[OK]**をクリックします。  パラメーターが **[入力と出力]**ブレードに表示されます。 **[OK]** をもう一度クリックし、Runbook の **[保存]** と **[発行]** をクリックします。

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Runbook の入力パラメーターに値を割り当てる
次のシナリオでは、Runbook の入力パラメーターに値を割り当てることができます。

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook を起動し、パラメーターを割り当てる
Azure Portal、Webhook、PowerShell コマンドレット、REST API、SDK など、Runbook はさまざまな方法で起動できます。 以下では、Runbook を起動し、パラメーターを割り当てるためのさまざまな方法について説明します。

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Azure ポータルを使用して公開済み Runbook を起動し、パラメーターを割り当てる
[Runbook を起動する](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)と、**[Runbook の開始]** ブレードが開きます。ここで、作成したパラメーターの値を構成できます。

![Start using the portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

入力ボックスの下にあるラベルで、パラメーターに設定された属性を確認できます。 属性は、必須/任意、型、既定値などです。 パラメーター名の横にあるヘルプ バルーンでは、パラメーター入力値を決定するために必要なすべての主要情報を確認できます。 たとえば、パラメーターが必須か任意かを示す情報です。 また、型、既定値 (該当する場合)、その他の役に立つ注記も含まれます。

![Help balloon](media/automation-runbook-input-parameters/automation-05-helpbaloon.png)

> [!NOTE]
> 文字列型のパラメーターの場合、文字列値に **空** を指定できます。  入力パラメーター ボックスに「 **EmptyString** 」と入力すると、空の文字列がパラメーターに渡されます。 また、文字列型パラメーターでは **Null** 値を渡すことができません。 文字列型パラメーターに何の値も渡さないと、PowerShell はそれを Null として解釈します。
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell コマンドレットを利用して公開済み Runbook を起動し、パラメーターを割り当てる
* **Azure Resource Manager コマンドレット:**[Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)を使用して、リソース グループに作成された Automation Runbook を起動できます。
  
  **例:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Azure サービス管理コマンドレット:**[Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx)を使用して、既定のリソース グループに作成された Automation Runbook を起動できます。
  
  **例:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> PowerShell コマンドレットで Runbook を起動すると、既定のパラメーター **MicrosoftApplicationManagementStartedBy** が値 **PowerShell** で作成されます。 このパラメーターは **[ジョブの詳細]** ブレードに表示されます。  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>SDK で Runbook を起動し、パラメーターを割り当てる
* **Azure Resource Manager メソッド:** プログラミング言語の SDK を利用して Runbook を起動できます。 以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。 完全なコードは、 [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)にあります。  
  
  ```
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
* **Azure サービス管理メソッド:** プログラミング言語の SDK を利用して Runbook を起動できます。 以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。 完全なコードは、 [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)にあります。
  
  ```      
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
  
  このメソッドを開始するには、Runbook パラメーターの **VMName** および **resourceGroupName** とそれらの値を保存するディクショナリを作成します。 次に、Runbook を起動します。 以下は、上で定義したメソッドを呼び出す C# コード スニペットです。
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>REST API で Runbook を起動し、パラメーターを割り当てる
**PUT** メソッドと次の要求 URI を利用することで、Azure Automation REST API で Runbook ジョブを作成し、起動できます。

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

要求 URI で、次のパラメーターを置き換えます。

* **subscription-id:** Azure のサブスクリプション ID。  
* **cloud-service-name:** 要求の送信先とするクラウド サービスの名前。  
* **automation-account-name:** 指定したクラウド サービス内でホストされている Automation アカウントの名前。  
* **job-id:** ジョブの GUID。 PowerShell の GUID は **[GUID]::NewGuid().ToString()** コマンドで作成できます。

Runbook ジョブにパラメーターを渡すには、要求本文を使用します。 JSON 形式で指定される、次の 2 つのプロパティを受け取ります。

* **Runbook 名:** 必須。 開始するジョブの Runbook の名前。  
* **Runbook パラメーター:** 任意。 (名前、値) 形式のパラメーターリストのディクショナリ。名前は文字列型にする必要があります。値には有効なあらゆる JSON 値を指定できます。

**VMName** と **resourceGroupName** をパラメーターとして、先に作成した **Get-AzureVMTextual** Runbook を起動するには、要求本文に次の JSON 形式を使用します。

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

ジョブが正常に作成されると、HTTP 状態コード 201 が返されます。 応答ヘッダーと応答本文の詳細については、 [REST API を使用して Runbook ジョブを作成する](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook をテストし、パラメーターを割り当てる
テスト オプションを利用し、[Runbook の草案バージョンをテスト](automation-testing-runbook.md)すると、**[テスト]** ブレードが開きます。このブレードで、先に作成したパラメーターの値を構成できます。

![Test and assign parameters](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>スケジュールを Runbook にリンクし、パラメーターを割り当てる
Runbook を特定の時刻に開始するために、Runbook に[スケジュールをリンク](automation-schedules.md)できます。 スケジュールを作成するときに、入力パラメーターを割り当てます。スケジュールで Runbook が起動されるときに、それらの値が使用されます。 必須のパラメーター値をすべて指定しないと、スケジュールは保存できません。

![Schedule and assign parameters](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook の Webhook を作成し、パラメーターを割り当てる
Runbook の [Webhook](automation-webhooks.md) を作成し、Runbook 入力パラメーターを構成できます。 必須のパラメーター値をすべて指定しないと、Webhook は保存できません。

![Create webhook and assign parameters](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Webhook を利用して Runbook を実行すると、定義した入力パラメーターと共に **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** が送信されます。 **WebhookData** パラメーターの詳細を展開するには、このパラメーターをクリックします。

![WebhookData parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>次のステップ
* Runbook の入力と出力の詳細については、「 [Azure Automation: runbook input, output, and nested runbooks (Azure Automation: Runbook の入力と出力および入れ子になった Runbook)](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)」を参照してください。
* Runbook を起動するさまざまな方法については、「 [Runbook の開始](automation-starting-a-runbook.md)」を参照してください。
* テキスト Runbook を編集する方法については、「 [テキスト Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
* グラフィカル Runbook を編集する方法については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


