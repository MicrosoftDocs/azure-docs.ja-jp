---
title: Azure Automation State Configuration で DSC 構成をコンパイルする
description: この記事では、Azure Automation の Desired State Configuration (DSC) 構成をコンパイルする方法について説明します。
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b45aad71e04418c7c7dda4fc3f0c84a5fe99ecdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896344"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation State Configuration で DSC 構成をコンパイルする

Azure Automation State Configuration では、以下の方法で Desired State Configuration (DSC) 構成をコンパイルできます。

- Azure State Configuration コンパイル サービス
  - 対話型ユーザー インターフェイスを使用する初心者向けの方法
   - ジョブの状態を簡単に追跡できる

- Windows PowerShell
  - ローカルのワークステーションまたはビルド サービスでの Windows PowerShell からの呼び出し
  - 開発テスト パイプラインとの統合
  - 複雑なパラメーター値の提供
  - 大規模なノード データと非ノード データの操作
  - パフォーマンスの大幅な向上

Azure Resource Manager テンプレートと Azure Desired State Configuration (DSC) 拡張機能を使用して、構成を Azure VM にプッシュすることもできます。 Azure DSC 拡張機能は、Azure VM エージェント フレームワークを使用して、Azure VM で実行される DSC 構成の配布、適用、およびレポート作成を行います。 Azure Resource Manager テンプレートを使用するコンパイルについて詳しくは、「[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](../virtual-machines/extensions/dsc-template.md#details)」をご覧ください。 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Azure State Configuration での DSC 構成のコンパイル

### <a name="portal"></a>ポータル

1. Automation アカウントで、 **[状態の構成 (DSC)]** をクリックします。
1. **[構成]** タブをクリックして、コンパイルする構成の名前をクリックします。
1. **[コンパイル]** をクリックします。
1. 構成にパラメーターが含まれていない場合は、構成をコンパイルするかどうかを確認するメッセージが表示されます。 構成にパラメーターが含まれている場合は、 **[構成のコンパイル]** ブレードが開くので、パラメーター値を入力できます。
1. コンパイル ジョブの状態を追跡できるように [コンパイル ジョブ] ページが表示されます。 このページを使用して、Azure Automation State Configuration プル サーバーに配置されたノード構成 (MOF 構成ドキュメント) を追跡することもできます。

### <a name="azure-powershell"></a>Azure PowerShell

[Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) を使用して、Windows PowerShell を使用したコンパイルを開始できます。 次のサンプル コードでは、**SampleConfig** という名前の DSC 構成のコンパイルを開始します。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` からは、ジョブの状態を追跡するのに使用できるコンパイル ジョブ オブジェクトが返されます。 このコンパイル ジョブ オブジェクトは、[Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) と共に使用してコンパイル ジョブの状態を確認すること、および [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) と共に使用してそのストリーム (出力) を表示することが可能です。 次のサンプルでは、SampleConfig 構成のコンパイルを開始し、コンパイルが完了するまで待機した後、ストリームを表示します。

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>基本パラメーターの宣言

DSC 構成のパラメーターの宣言 (パラメーターの種類、プロパティなど) は、Azure Automation Runbook と同じように動作します。 Runbook のパラメーターの詳細については、「 [Azure Automation での Runbook を開始する](./start-runbooks.md) 」を参照してください。

次の例では、`FeatureName` および `IsPresent` パラメーターを使用して、コンパイル時に生成された **ParametersExample.sample** ノード構成のプロパティの値を特定します。

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

PowerShell には、[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)形式のパラメーターが必要です。この形式では、キーはパラメーター名と一致し、値はパラメーター値と一致します。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

`PSCredential` オブジェクトをパラメーターとして渡す方法の詳細については、「[資格情報資産](#credential-assets)」を参照してください。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure Automation で複合リソースを含む構成をコンパイルする

**複合リソース** 機能によって、構成の内側で、入れ子のリソースとして DSC 構成を使用できます。 この機能により、1 つのリソースに複数の構成を適用できます。 「[Composite resources: Using a DSC configuration as a resource](/powershell/scripting/dsc/resources/authoringresourcecomposite)」 (複合リソース: リソースとしての DSC 構成の使用) を参照して、複合リソースの詳細について確認してください。

> [!NOTE]
> 複合リソースを含む構成が正しくコンパイルされるように、その複合リソースが依存するすべての DSC リソースを、まず Azure Automation にインポートする必要があります。 DSC 複合リソースの追加は、Azure Automation に任意の PowerShell モジュールを追加するのと同じです。 このプロセスは、「[Azure Automation でモジュールを管理する](./shared-resources/modules.md)」に記載されています。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure Automation で構成をコンパイルするときに ConfigurationData を管理する

組み込みの DSC パラメーターである `ConfigurationData` を使用すると、PowerShell DSC を使用しているときに、環境固有の構成から構造上の構成を分離することができます。 詳細については、「[PowerShell DSC で "環境" から "構造" を分離する](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)」を参照してください。

> [!NOTE]
> Azure Automation State Configuration でコンパイルするとき、`ConfigurationData` を Azure PowerShell で使用することはできますが、Azure portal では使用できません。

次の DSC 構成の例では、`$ConfigurationData` キーワードと `$AllNodes` キーワードによって `ConfigurationData` を使用しています。 この例には [xWebAdministration モジュール](https://www.powershellgallery.com/packages/xWebAdministration/)も必要です。

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

上記の DSC 構成は、Windows PowerShell を使用してコンパイルできます。 以下のスクリプトでは、2 つのノード構成が Azure Automation State Configuration プル サービスに追加されます。**ConfigurationDataSample.MyVM1** と **ConfigurationDataSample.MyVM3**。

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>コンパイル時に Azure Automation の資産を操作する

資産の参照方法は、Azure Automation State Configuration と Runbook の両方で同じです。 詳細については、「

- [証明書](./shared-resources/certificates.md)
- [接続](automation-connections.md)
- [資格情報](./shared-resources/credentials.md)
- [変数](./shared-resources/variables.md)

#### <a name="credential-assets"></a>資格情報資産

Azure Automation の DSC 構成は、`Get-AutomationPSCredential` コマンドレットを使用して Automation の資格情報資産を参照できます。 構成に `PSCredential` オブジェクトを指定するパラメーターがある場合は、Azure Automation の資格情報資産の文字列名をコマンドレットに渡して資格情報を取得することで、`Get-AutomationPSCredential` を使用します。 その後、`PSCredential` オブジェクトを必要とするパラメーターに対して、そのオブジェクトを使用します。 バックグラウンドで、この名前の Azure Automation 資格情報資産が取得され、構成に渡されます。 下の例に、このシナリオの動作を示します。

ノード構成 (MOF 構成ドキュメント) で資格情報を安全に保持するには、ノード構成 MOF ファイルで資格情報を暗号化する必要があります。 現在のところ、PowerShell DSC アクセス許可を指定し、ノード構成 MOF の生成時にプレーン テキストで資格情報を出力する必要があります。 PowerShell DSC では、コンパイル ジョブによる生成後に、Azure Automation によって MOF ファイル全体が暗号化されることが認識されません。

構成データを使用すると、生成されたノード構成 MOF に資格情報をプレーン テキストで出力することの許可を PowerShell DSC に通知できます。 DSC 構成に含まれていて、資格情報を使用するノード ブロック名ごとに、`ConfigurationData` を介して `PSDscAllowPlainTextPassword = $true` を渡す必要があります。

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

PowerShell を使用して上記の DSC 構成をコンパイルできます。 以下の PowerShell コードでは、2 つのノード構成が Azure Automation State Configuration プル サーバーに追加されます。(**CredentialSample.MyVM1** と **CredentialSample.MyVM2**)。

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> コンパイルが完了すると、`The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` というエラー メッセージが表示される場合があります。このメッセージは無視してかまいません。

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Windows PowerShell で DSC 構成をコンパイルする

Windows PowerShell に DSC 構成をコンパイルする手順は、PowerShell DSC のドキュメント、「[構成の作成、コンパイル、適用](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)」に記述されています。
このプロセスは、開発者のワークステーションから実行することも、[Azure DevOps](https://dev.azure.com) などのビルド サービス内で実行することもできます。 その後、構成のコンパイルによって生成された MOF ファイルを、Azure State Configuration サービスにインポートできます。

Windows PowerShell でコンパイルする場合には、構成コンテンツに署名するオプションもあります。 署名されたノード構成は、DSC エージェントにより、管理対象ノードでローカルに検証されます。 検証によって、ノードに適用される構成が、承認済みソースからのものであることが保証されます。

Azure の外部でコンパイルしたノード構成 (MOF ファイル) をインポートすることもできます。 このインポートには、開発者のワークステーションからのコンパイルや [Azure DevOps](https://dev.azure.com) などのサービスでのコンパイルも含まれます。 このアプローチには、パフォーマンスや信頼性を含め、多数の利点があります。

> [!NOTE]
> Azure Automation でノード構成ファイルをインポートできるためには、ファイルのサイズが 1 MB 以下である必要があります。

ノード構成の署名の詳細については、[WMF 5.1 での機能強化の、構成とモジュールに署名する方法](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)に関するページを参照してください。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure portal でノード構成をインポートする

1. お使いの Automation アカウントで、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックして、 **[追加]** をクリックします。
1. [インポート] ページで、 **[ノード構成ファイル]** フィールドの横にあるフォルダー アイコンをクリックして、ローカル コンピューター上のノード構成の MOF ファイルを参照します。

   ![ローカル ファイルの参照](./media/automation-dsc-compile/import-browse.png)

1. **[構成名]** フィールドに名前を入力します。 この名前は、ノード構成のコンパイル元の構成の名前と一致する必要があります。
1. **[OK]** をクリックします。

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell でノード構成をインポートする

[Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) コマンドレットを使用して、ノード構成を Automation アカウントにインポートできます。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用を開始する](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てる方法の詳細については、「[Azure Automation State Configuration で DSC 構成をコンパイルする](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで State Configuration を使う例については、「[Chocolatey を使用して継続的配置を設定する](automation-dsc-cd-chocolatey.md)」をご覧ください。
