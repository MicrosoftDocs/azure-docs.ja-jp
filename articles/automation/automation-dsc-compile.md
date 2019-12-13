---
title: Azure Automation State Configuration での構成のコンパイル
description: この記事では、Azure Automation の Desired State Configuration (DSC) 構成をコンパイルする方法について説明します。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fdea8ed9a9e59a169a6ffb525ed286eb7d1ada53
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850909"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation State Configuration での DSC 構成のコンパイル

Azure Automation State Configuration を使用して、Desired State Configuration (DSC) 構成をコンパイルするには、Azure portal で行う方法と、Windows PowerShell を使用する 2 通りの方法があります。 次の表は、各方法の特徴に基づいて、どちらの方法をどのような場合に使用するか決定するのに役立ちます。

- Azure State Configuration コンパイル サービス
  - 対話型ユーザー インターフェイスを使用する初心者向けの方法
   - ジョブの状態を簡単に追跡できる

- Windows PowerShell
  - ローカルのワークステーションまたはビルド サービスでの Windows PowerShell からの呼び出し
  - 開発テスト パイプラインとの統合
  - 複雑なパラメーター値の提供
  - 大規模なノード データと非ノード データの操作
  - パフォーマンスの大幅な向上

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure State Configuration での DSC 構成のコンパイル

### <a name="portal"></a>ポータル

1. Automation アカウントから、 **[状態の構成 (DSC)]** をクリックします。
1. **[構成]** タブをクリックして、コンパイルする構成の名前をクリックします。
1. **[コンパイル]** をクリックします。
1. 構成にパラメーターが含まれていない場合、コンパイルの実行を確認するメッセージが表示されます。 構成にパラメーターが含まれている場合は、 **[構成のコンパイル]** ブレードが開き、パラメーター値を入力できます。 パラメーターの詳細については、次の「[**基本パラメーター**](#basic-parameters)」セクションを参照してください。
1. **[コンパイル ジョブ]** ページが開き、コンパイル ジョブの状態を追跡できます。また、Azure Automation State Configuration プル サーバーにノード構成 (MOF 構成ドキュメント) が配置されます。

### <a name="azure-powershell"></a>Azure PowerShell

[`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) を使用して、Windows PowerShell を使用したコンパイルを開始できます。 次のサンプル コードは、 **SampleConfig**という名前の DSC 構成のコンパイルを開始します。

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` は、ジョブの状態を追跡するのに使用できるコンパイル ジョブ オブジェクトを返します。 このコンパイル ジョブ オブジェクトを、[`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
と共に使用してコンパイル ジョブの状態を確認したり、[`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
と共に使用してストリーム (出力) を表示したりすることができます。 次のサンプル コードでは、 **SampleConfig** 構成のコンパイルを開始し、コンパイルが完了するまで待機した後、ストリームを表示します。

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>基本パラメーター

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

#### <a name="portal"></a>ポータル

ポータルで、 **[コンパイル]** をクリックすると、パラメーター値を入力できます。

![構成のコンパイル パラメーター](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell には、[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)形式のパラメーターが必要です。キーはパラメーター名と一致し、値はパラメーター値と一致します。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials をパラメーターとして渡す方法の詳細については、以下の「[資格情報資産](#credential-assets)」を参照してください。

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>複合リソースを含む Azure Automation での構成のコンパイル

**複合リソース**の利点は、構成の内側に入れ子のリソースとして DSC 構成を使用できることです。 これにより、1 つのリソースに複数の構成を適用することができます。 「[Composite resources: Using a DSC configuration as a resource (複合リソース: DSC 構成をリソースとして使用する)](/powershell/scripting/dsc/resources/authoringresourcecomposite)」を参照して、**複合リソース**の詳細について確認してください。

> [!NOTE]
> **複合リソース**を含む構成を正しくコンパイルするには、まず、その複合リソースが依存する DSC リソースを先に Azure Automation に確実にインポートする必要があります。

**複合リソース**の DSC への追加は、Azure Automation に任意の PowerShell モジュールを追加するのと同じです。
この手順の順を追った説明については、「[Azure Automation でモジュールを管理する](/azure/automation/shared-resources/modules)」を参照してください。

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Azure Automation で構成をコンパイルするときの ConfigurationData の管理

**ConfigurationData** によって、PowerShell DSC の使用中に環境固有の構成と構造上の構成を分離することができます。 [ConfigurationData](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) の詳細については、 **PowerShell DSC で "環境" から "物" を分離する**ことに関する記事を参照してください。

> [!NOTE]
> Azure Automation State Configuration でコンパイルする場合の **ConfigurationData** の使用は、Azure PowerShell を使用した場合は可能ですが Azure portal では可能ではありません。

次の DSC 構成の例では、 **$ConfigurationData** および **$AllNodes** キーワードを介して **ConfigurationData** を使用します。 この例では、[**xWebAdministration** モジュール](https://www.powershellgallery.com/packages/xWebAdministration/)も必要になります。

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

上記の DSC 構成は、Windows PowerShell を使用してコンパイルできます。 以下のスクリプトでは、2 つのノード構成が Azure Automation State Configuration プル サービスに追加されます。(**ConfigurationDataSample.MyVM1** と **ConfigurationDataSample.MyVM3**)。

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

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Azure Automation でのコンパイル時の資産の操作

資産の参照方法は、Azure Automation State Configuration や Runbook と同じです。 詳細については、「

- [証明書](automation-certificates.md)
- [接続](automation-connections.md)
- [資格情報](automation-credentials.md)
- [変数](automation-variables.md)

#### <a name="credential-assets"></a>資格情報資産

Azure Automation の DSC 構成は、`Get-AutomationPSCredential` コマンドレットを使用して Automation の資格情報資産を参照できます。 構成に **PSCredential** 型のパラメーターがある場合、Azure Automation の資格情報資産の文字列名をコマンドレットに渡して資格情報を取得することで、`Get-AutomationPSCredential` コマンドレットを使用できます。 次に、**PSCredential** オブジェクトを必要とするパラメーターに、そのオブジェクトを使用できます。 バックグラウンドで、この名前の Azure Automation 資格情報資産が取得され、構成に渡されます。 実際の動作例を次に示します。

ノード構成 (MOF 構成ドキュメント) で資格情報を安全に保持するには、ノード構成 MOF ファイルで資格情報を暗号化する必要があります。 ただし、現時点では、ノード構成 MOF 作成時に資格情報をプレーンテキストで出力することを許可するように PowerShell DSC に指定する必要があります。PowerShell DSC は、コンパイル ジョブによって生成された MOF ファイル全体を Azure Automation が暗号化することを認識していないためです。

構成データを使用すると、生成されたノード構成 MOF に資格情報をプレーン テキストで出力することの許可を PowerShell DSC に通知できます。 DSC 構成に表示され、資格情報を使用する各ノード ブロックの名前に対して、**ConfigurationData** を使用して `PSDscAllowPlainTextPassword = $true` を渡す必要があります。

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
> コンパイルが完了すると、次のようなエラーが表示される可能性があります。" **"Microsoft.PowerShell.Management" スナップインが既にインポートされているため、"Microsoft.PowerShell.Management" モジュールがインポートされませんでした。** " この警告は無視してかまいません。

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Windows PowerShell での構成のコンパイルおよび Azure Automation への発行

Azure の外部でコンパイルしたノード構成 (MOF) をインポートすることもできます。
これには、開発者のワークステーションからのコンパイル、または [Azure DevOps](https://dev.azure.com) などのサービスでのコンパイルも含みます。
このアプローチには、パフォーマンスと信頼性を含む利点が複数あります。
Windows PowerShell でコンパイルする場合には、構成コンテンツに署名するオプションもあります。
署名済みのノード構成は DSC エージェントの管理ノードでローカルに検証され、ノードに適用されている構成が承認済みのソースから取得されたことを確認します。

> [!NOTE]
> ノード構成ファイルを Azure Automation にインポートするには、ファイル サイズを 1 MB 以下にする必要があります。

ノード構成に署名する方法の詳細については、「[Improvements in WMF 5.1 - How to sign configuration and module](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)」(WMF 5.1 の機能強化 - 構成とモジュールに署名する方法) を参照してください。

### <a name="compiling-a-configuration-in-windows-powershell"></a>Windows PowerShell での構成のコンパイル

Windows PowerShell に DSC 構成をコンパイルする手順は、PowerShell DSC のドキュメント、「[構成の作成、コンパイル、適用](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)」に記述されています。
これは、開発者のワークステーションから、または [Azure DevOps](https://dev.azure.com) などのビルド サービスから実行できます。

その後、構成のコンパイルによって生成された MOF ファイルは、Azure State Configuration サービスに直接インポートできます。

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure Portal でのノード構成のインポート

1. [Automation アカウント] から、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. **[状態の構成 (DSC)]** ページで **[構成]** タブをクリックし、 **[+ 追加]** をクリックします。
1. **[インポート]** ページで、 **[Node Configuration File (ノード構成ファイル)]** テキスト ボックスの横にあるフォルダー アイコンをクリックして、ローカル コンピューター上のノード構成ファイル (MOF) を参照します。

   ![ローカル ファイルの参照](./media/automation-dsc-compile/import-browse.png)

1. **[構成名]** テキスト ボックスに名前を入力します。 この名前は、ノード構成のコンパイル元の構成の名前と一致する必要があります。
1. Click **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Azure PowerShell でのノード構成のインポート

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
