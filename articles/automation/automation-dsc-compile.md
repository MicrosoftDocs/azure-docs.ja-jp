---
title: Azure Automation State Configuration での構成のコンパイル
description: この記事では、Azure Automation の Desired State Configuration (DSC) 構成をコンパイルする方法について説明します。
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f2c6f45081b78d330033570ff322f90cd06e50dd
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594272"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation State Configuration での DSC 構成のコンパイル

Azure Automation State Configuration を使用して、Desired State Configuration (DSC) 構成をコンパイルする方法は、2 通りあります。Azure Portal で行う方法と、Windows PowerShell を使用する方法です。 次の表は、各方法の特徴に基づいて、どちらの方法をどのような場合に使用するか決定するのに役立ちます。

**Azure Portal**

- 対話型ユーザー インターフェイスを使用する、最も簡単な方法
- フォームに単純なパラメーター値を入力する
- ジョブの状態を簡単に追跡できる
- Azure ログオンを使用してアクセスを認証する

**Windows PowerShell**

- Windows PowerShell コマンドレットを使用してコマンド ラインから呼び出す
- 複数のステップで自動化されたソリューションに含めることができる
- 単純なパラメーター値と複雑なパラメーター値を指定する
- ジョブの状態を追跡できる
- PowerShell コマンドレットをサポートするクライアントが必要
- ConfigurationData を渡す
- 資格情報を使用する構成をコンパイルする

コンパイルの方法を決定したら、次の手順に従ってコンパイルを開始します。

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Azure プレビューを使用した DSC 構成のコンパイル

1. Automation アカウントから、**[状態の構成 (DSC)]** をクリックします。
1. **[構成]** タブをクリックして、コンパイルする構成の名前をクリックします。
1. **[コンパイル]** をクリックします。
1. 構成にパラメーターが含まれていない場合、コンパイルの実行を確認するメッセージが表示されます。 構成にパラメーターが含まれている場合は、 **[構成のコンパイル]** ブレードが開き、パラメーター値を入力できます。 パラメーターの詳細については、次の「[**基本パラメーター**](#basic-parameters)」セクションを参照してください。
1. **[コンパイル ジョブ]** ページが開き、コンパイル ジョブの状態を追跡できます。また、Azure Automation State Configuration プル サーバーにノード構成 (MOF 構成ドキュメント) が配置されます。

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Windows PowerShell を使用した DSC 構成のコンパイル

[`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) を使用して、Windows PowerShell を使用したコンパイルを開始できます。 次のサンプル コードは、 **SampleConfig**という名前の DSC 構成のコンパイルを開始します。

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` は、ジョブの状態を追跡するのに使用できるコンパイル ジョブ オブジェクトを返します。 このコンパイル ジョブ オブジェクトを、[`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
と共に使用してコンパイル ジョブの状態を確認したり、[`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
と共に使用してストリーム (出力) を表示したりすることができます。 次のサンプル コードでは、 **SampleConfig** 構成のコンパイルを開始し、コンパイルが完了するまで待機した後、ストリームを表示します。

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>基本パラメーター

DSC 構成のパラメーターの宣言 (パラメーターの種類、プロパティなど) は、Azure Automation Runbook と同じように動作します。 Runbook のパラメーターの詳細については、「 [Azure Automation での Runbook を開始する](automation-starting-a-runbook.md) 」を参照してください。

次の例では、コンパイル時に生成される、**ParametersExample.sample** ノード構成のプロパティの値を指定するために、**FeatureName** と **IsPresent** という 2 つのパラメーターを使用します。

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Automation State Configuration ポータルまたは Azure PowerShell で、基本パラメーターを使用する DSC 構成をコンパイルできます。

### <a name="portal"></a>ポータル

ポータルで、 **[コンパイル]** をクリックすると、パラメーター値を入力できます。

![構成のコンパイル パラメーター](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell には、[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)形式のパラメーターが必要です。キーはパラメーター名と一致し、値はパラメーター値と一致します。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials をパラメーターとして渡す方法の詳細については、以下の「[資格情報資産](#credential-assets)」を参照してください。

## <a name="composite-resources"></a>複合リソース

**複合リソース**の利点は、構成の内側に入れ子のリソースとして DSC 構成を使用できることです。 これにより、1 つのリソースに複数の構成を適用することができます。 「[Composite resources: Using a DSC configuration as a resource (複合リソース: DSC 構成をリソースとして使用する)](/powershell/dsc/authoringresourcecomposite)」を参照して、**複合リソース**の詳細について確認してください。

> [!NOTE]
> **複合リソース**を正常にコンパイルするには、まず、その複合リソースが依存する DSC リソースを先に Azure Automation アカウント モジュール リポジトリにインストールしておく必要があります。そうしないとインポートが適切に実行されません。

DSC の**複合リソース**を追加するには、リソース モジュールをアーカイブ (*.zip) に追加する必要があります。 Azure Automation アカウントのモジュール リポジトリに移動します。 次に、[モジュールの追加] ボタンをクリックします。

![モジュールの追加](./media/automation-dsc-compile/add_module.png)

そのアーカイブが置かれているディレクトリに移動します。 アーカイブ ファイルを選択し、[OK] をクリックします。

![モジュールの選択](./media/automation-dsc-compile/select_dscresource.png)

再びモジュール ディレクトリが表示され、アンパックと Azure Automation への登録が実行される間、**複合リソース**の状態を監視できます。

![複合リソースのインポート](./media/automation-dsc-compile/register_composite_resource.png)

登録したモジュールをクリックすると、その**複合リソース**が構成の中で使用できるようになったことを確認できます。

![複合リソースの確認](./media/automation-dsc-compile/validate_composite_resource.png)

その後、実際の構成の中で、次のようにして**複合リソース**を呼び出すことができます。

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** によって、PowerShell DSC の使用中に環境固有の構成と構造上の構成を分離することができます。 [ConfigurationData](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) の詳細については、 **PowerShell DSC で "環境" から "物" を分離する**ことに関する記事を参照してください。

> [!NOTE]
> Azure Portal ではなく、Azure PowerShell を使用して Azure Automation State Configuration でコンパイルする場合に、**ConfigurationData** を使用できます。

次の DSC 構成の例では、**$ConfigurationData** および **$AllNodes** キーワードを介して **ConfigurationData** を使用します。 この例では、[**xWebAdministration** モジュール](https://www.powershellgallery.com/packages/xWebAdministration/)も必要になります。

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

PowerShell を使用して上記の DSC 構成をコンパイルできます。 以下の PowerShell では、2 つのノード構成が Azure Automation State Configuration プル サーバーに追加されます (**ConfigurationDataSample.MyVM1** と **ConfigurationDataSample.MyVM3**)。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>アセット

資産の参照方法は、Azure Automation State Configuration や Runbook と同じです。 詳細については、以下を参照してください。

- [証明書](automation-certificates.md)
- [接続](automation-connections.md)
- [資格情報](automation-credentials.md)
- [変数](automation-variables.md)

### <a name="credential-assets"></a>資格情報資産

Azure Automation の DSC 構成は、`Get-AutomationPSCredential` コマンドレットを使用して Automation の資格情報資産を参照できます。 構成に **PSCredential** 型のパラメーターがある場合、Azure Automation の資格情報資産の文字列名をコマンドレットに渡して資格情報を取得することで、`Get-AutomationPSCredential` コマンドレットを使用できます。 次に、**PSCredential** オブジェクトを必要とするパラメーターに、そのオブジェクトを使用できます。 バックグラウンドで、この名前の Azure Automation 資格情報資産が取得され、構成に渡されます。 実際の動作例を次に示します。

ノード構成 (MOF 構成ドキュメント) で資格情報を安全に保持するには、ノード構成 MOF ファイルで資格情報を暗号化する必要があります。 ただし、現時点では、ノード構成 MOF 作成時に資格情報をプレーンテキストで出力することを許可するように PowerShell DSC に指定する必要があります。PowerShell DSC は、コンパイル ジョブによって生成された MOF ファイル全体を Azure Automation が暗号化することを認識していないためです。

 [**ConfigurationData**](#configurationdata)をクリックします。 DSC 構成に表示され、資格情報を使用する各ノード ブロックの名前に対して、**ConfigurationData** を使用して `PSDscAllowPlainTextPassword = $true` を渡す必要があります。

次の例は、Automation 資格情報資産を使用する DSC 構成の例です。

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

PowerShell を使用して上記の DSC 構成をコンパイルできます。 以下の PowerShell では、2 つのノード構成が Azure Automation State Configuration プル サーバーに追加されます (**CredentialSample.MyVM1** と **CredentialSample.MyVM2**)。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> コンパイルが完了すると、次のようなエラーが表示される可能性があります。"**"Microsoft.PowerShell.Management" スナップインが既にインポートされているため、"Microsoft.PowerShell.Management" モジュールがインポートされませんでした。**" この警告は無視してかまいません。

## <a name="partial-configuration"></a>部分構成

Azure Automation State Configuration では [部分構成](https://docs.microsoft.com/en-us/powershell/dsc/pull-server/partialconfigs) の使用をサポートします。
このシナリオでは、DSC は独立して複数の構成を管理するように構成されており、各構成は Azure Automation から取得されます。
ただし、ノードに割り当てることができる構成はAutomation アカウントあたり 1 つだけです。
つまり、1 つのノードに 2 つの構成を使用している場合、2 つの Automation アカウントが必要になります。
構成をコードとして使用し、チームが連携してサーバーを共同で管理する方法の詳細については、「[Understanding DSC's role in a CI/CD Pipeline (CI/CD パイプラインの DSC のロールを理解する)](https://docs.microsoft.com/en-us/powershell/dsc/overview/authoringadvanced)」を参照してください。

## <a name="importing-node-configurations"></a>ノード構成のインポート

Azure の外部でコンパイルしたノード構成 (MOF) をインポートすることもできます。 この処理の利点の 1 つは、ノード構成に署名できることです。 署名済みのノード構成は DSC エージェントの管理ノードでローカルに検証され、ノードに適用されている構成が承認済みのソースから取得されたことを確認します。

> [!NOTE]
> 署名済みの構成を Azure Automation アカウントにインポートできますが、現時点で、Azure Automation は署名済みの構成のコンパイルをサポートしていません。

> [!NOTE]
> ノード構成ファイルを Azure Automation にインポートするには、ファイル サイズを 1 MB 以下にする必要があります。

ノード構成に署名する方法の詳細については、「[Improvements in WMF 5.1 - How to sign configuration and module](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations)」(WMF 5.1 の機能強化 - 構成とモジュールに署名する方法) を参照してください。

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure Portal でのノード構成のインポート

1. [Automation アカウント] から、**[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで **[構成]** タブをクリックし、**[+ 追加]** をクリックします。
1. **[インポート]** ページで、**[Node Configuration File (ノード構成ファイル)]** テキスト ボックスの横にあるフォルダー アイコンをクリックして、ローカル コンピューター上のノード構成ファイル (MOF) を参照します。

   ![ローカル ファイルの参照](./media/automation-dsc-compile/import-browse.png)

1. **[構成名]** テキスト ボックスに名前を入力します。 この名前は、ノード構成のコンパイル元の構成の名前と一致する必要があります。
1. Click **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>PowerShell でのノード構成のインポート

[Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) コマンドレットを使用して、ノード構成を Automation アカウントにインポートできます。

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>次の手順

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
