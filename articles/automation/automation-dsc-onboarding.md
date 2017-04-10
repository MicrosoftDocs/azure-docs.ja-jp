---
title: "Azure Automation DSC による管理のためのマシンのオンボード | Microsoft Docs"
description: "Azure Automation DSC による管理のためのマシンの設定方法"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
ms.assetid: da13e1f5-2a1c-443b-8e3b-9f0d6f9e4810
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 12/13/2016
ms.author: eslesar
translationtype: Human Translation
ms.sourcegitcommit: e2257730f0c62dbc0313ce7953fc5f953dae8ac3
ms.openlocfilehash: f81536322ad1bb16e4af326e0b053da47690619c
ms.lasthandoff: 02/15/2017


---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Azure Automation DSC による管理のためのマシンのオンボード

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Azure Automation DSC でマシンを管理する理由

[PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx)と同様、Azure Automation Desired State Configuration は、すべてのクラウドまたはオンプレミス データセンターの DSC ノード (物理および仮想マシン) のシンプルでありながら強力な構成管理サービスです。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。 Azure Automation DSC 管理レイヤーは、Azure Automation 管理レイヤーが PowerShell スクリプトに対応する DSC に対応します。 つまり、Azure Automation を使用して、PowerShell スクリプトを管理するのと同様に DSC 構成も管理できます。 Azure Automation DSC を使用するメリットの詳細については、「 [Azure Automation DSC の概要](automation-dsc-overview.md)」をご覧ください。

以下のさまざまなマシンを管理する際に、Azure Automation DSC を使用できます。

* Azure Virtual Machines (クラシック)
* Azure Virtual Machines
* Amazon Web Services (AWS) 仮想マシン
* オンプレミス、または Azure/AWS 以外のクラウド内の物理/仮想 Windows マシン
* オンプレミス、Azure、または Azure 以外のクラウド内の物理/仮想 Linux マシン

また、クラウドからコンピューター構成を管理する準備ができていない場合は、Azure Automation DSC をレポートのみのエンドポイントとして使用することもできます。 この場合、オンプレミスの DSC で目的の構成を設定 (プッシュ) し、Azure Automation の目的の状態へのノード コンプライアンスに関する詳細なレポートを表示できます。

次のセクションでは、各種類のマシンを Azure Automation DSC にオンボードできる方法の概要を示します。

## <a name="azure-virtual-machines-classic"></a>Azure Virtual Machines (クラシック)

Azure Automation DSC では、Azure ポータルまたは PowerShell を使用して、構成管理用に Azure Virtual Machines (クラシック) を簡単にオンボードすることができます。 Azure VM Desired State Configuration 拡張機能を使用することで、管理者が VM にリモート接続しなくても、内部で VM を Azure Automation DSC に登録できます。 Azure VM Desired State Configuration 拡張機能は非同期に実行されるため、その進行状況の追跡またはトラブルシューティングの手順については、後述の「 [**Azure 仮想マシンのオンボードに関するトラブルシューティング**](#troubleshooting-azure-virtual-machine-onboarding) 」を参照してください。

### <a name="azure-portal"></a>Azure ポータル

[Azure Portal](http://portal.azure.com/) で、**[参照]**  ->  **[仮想マシン (クラシック)]** の順にクリックします。 オンボードする Windows VM を選択します。 仮想マシンのダッシュボード ブレードで、**[すべての設定]**  ->  **[拡張機能]**  ->  **[追加]**  ->  **[Azure Automation DSC]**  ->  **[作成]** の順にクリックします。 ユース ケースに必要な [PowerShell DSC Local Configuration Manager の値](https://msdn.microsoft.com/powershell/dsc/metaconfig4)、Automation アカウントの登録キーと登録 URL、また必要に応じて、VM に割り当てるノード構成を入力します。

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

マシンをオンボードするための Automation アカウントの登録 URL とキーを見つける場合は、後述の「 [**セキュリティで保護された登録**](#secure-registration) 」を参照してください。

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Automation DSC では、Azure ポータル、Azure リソース マネージャーのテンプレート、または PowerShell を使用して、構成管理用に Azure Virtual Machines (クラシック) を簡単にオンボードすることができます。 Azure VM Desired State Configuration 拡張機能を使用することで、管理者が VM にリモート接続しなくても、内部で VM を Azure Automation DSC に登録できます。 Azure VM Desired State Configuration 拡張機能は非同期に実行されるため、その進行状況の追跡またはトラブルシューティングの手順については、後述の「 [**Azure 仮想マシンのオンボードに関するトラブルシューティング**](#troubleshooting-azure-virtual-machine-onboarding) 」を参照してください。

### <a name="azure-portal"></a>Azure ポータル

[Azure ポータル](https://portal.azure.com/)で、仮想マシンをオンボードする Azure Automation アカウントに移動します。 Automation アカウントのダッシュボードで、**[DSC ノード]**  ->  **[Azure VM の追加]** の順にクリックします。

**[オンボードする仮想マシンの選択]**で、オンボードする&1; つ以上の Azure Virtual Machines を選択します。

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)

**[登録データの構成]**で、ユース ケースに必要な [PowerShell DSC Local Configuration Manager の値](https://msdn.microsoft.com/powershell/dsc/metaconfig4) 、および必要に応じて VM に割り当てるノード構成を入力します。

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

### <a name="azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレート

Azure Virtual Machines は、Azure リソース マネージャーのテンプレートを使用して Azure Automation DSC にデプロイおよびオンボードできます。 Azure Automation DSC に既存の VM をオンボードする例のテンプレートについては、「 [DSC 拡張機能と Azure Automation DSC を使用して VM を構成する](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) 」を参照してください。 このテンプレートに入力する際に使用する登録キーと登録 URL を見つける場合は、後述の「 [**セキュリティで保護された登録**](#secure-registration) 」を参照してください。

### <a name="powershell"></a>PowerShell

[Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) コマンドレットを使用して、Azure ポータルで PowerShell を介して仮想マシンをオンボードすることができます。

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) 仮想マシン

AWS DSC Toolkit を使用して Azure Automation DSC による構成管理のための Amazon Web Services 仮想マシンを簡単にオンボードできます。 ツールキットの詳細については、 [こちら](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)を参照してください。

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>オンプレミス、または Azure/AWS 以外のクラウド内の物理/仮想 Windows マシン

オンプレミスの Windows マシンと (Amazon Web Services などの) Azure 以外のクラウド内の Windows マシンも、インターネットへの発信アクセスが可能な限り、いくつかの簡単な手順で Azure Automation DSC にオンボードすることができます。

1. Azure Automation DSC にオンボードするマシンに最新バージョンの [WMF 5](http://aka.ms/wmf5latest) がインストールされていることを確認します。
2. 以下の「 [**DSC メタ構成の生成**](#generating-dsc-metaconfigurations) 」のセクションの指示に従って、必要な DSC メタ構成が含まれるフォルダーを生成します。
3. 以下を使用して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。 **このコマンドを実行するマシンには最新バージョンの [WMF 5](http://aka.ms/wmf5latest) がインストールされている必要があります。**

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. PowerShell DSC のメタ構成をリモートで適用できない場合は、手順 2. のメタ構成フォルダーを、オンボードする各マシンにコピーします。 次に、オンボードする各マシンで **Set-DscLocalConfigurationManager** をローカルで呼び出します。
5. Azure ポータルまたはコマンドレットを使用して、オンボードするマシンがこの時点で Azure Automation アカウントに登録されている DSC ノードとして示されていることを確認します。

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>オンプレミス、Azure、または Azure 以外のクラウド内の物理/仮想 Linux マシン

オンプレミスの Linux マシン、Azure の Linux マシン、Azure 以外のクラウド内の Linux マシンも、インターネットへの発信アクセスが可能な限り、いくつかの簡単な手順で Azure Automation DSC にオンボードすることができます。

1. Azure Automation DSC にオンボードするマシンに最新バージョンの [DSC Linux エージェント](http://www.microsoft.com/download/details.aspx?id=49150) がインストールされていることを確認します。
2. [PowerShell DSC Local Configuration Manager の既定値](https://msdn.microsoft.com/powershell/dsc/metaconfig4) がユース ケースに適しており、 **両方とも** Azure Automation DSC とデータをやり取りするマシンをオンボードするには、次のようにします。

   + Azure Automation DSC にオンボードする各 Linux マシンで、Register.py で PowerShell DSC Local Configuration Manager の既定値を使用してオンボードします。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Automation アカウントの登録キーと登録 URL を見つける場合は、後述の「 [**セキュリティで保護された登録**](#secure-registration) 」を参照してください。

     PowerShell DSC Local Configuration Manager の既定値がユース ケースに**適して****いない**、または Azure Automation DSC にのみデータを送信するマシンをオンボードするものの、Azure Automation DSC から構成および PowerShell のモジュールを取得しない場合は、手順 3 ～ 6 に従います。 それ以外の場合は、手順 6 に直接進みます。

3. 以下の「 [**DSC メタ構成の生成**](#generating-dsc-metaconfigurations) 」セクションの指示に従って、必要な DSC メタ構成が含まれるフォルダーを生成します。
4. 以下を使用して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

このコマンドを実行するマシンには最新バージョンの [WMF 5](http://aka.ms/wmf5latest) がインストールされている必要があります。

1. PowerShell DSC メタ構成をリモートで適用できない場合は、オンボードする各 Linux マシンについて、手順 5. のフォルダーからそのマシンに対応するメタ構成を Linux マシンにコピーします。 次に、以下を使用して、Azure Automation DSC にオンボードする各 Linux マシンで `SetDscLocalConfigurationManager.py` をローカルで呼び出します。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Azure ポータルまたはコマンドレットを使用して、オンボードするマシンがこの時点で Azure Automation アカウントに登録されている DSC ノードとして示されていることを確認します。

## <a name="generating-dsc-metaconfigurations"></a>DSC メタ構成の生成

マシンを包括的に Azure Automation DSC にオンボードするために、[DSC メタ構成](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig)を生成できます。これを適用すると、Azure Automation DSC とデータをやり取りするようにマシン上の DSC エージェントに指示します。 Azure Automation DSC の DSC メタ構成は、PowerShell DSC 構成または Azure Automation PowerShell コマンドレットのいずれかを使用して生成できます。

> [!NOTE]
> DSC メタ構成には Automation アカウントにマシンをオンボードするために必要な管理用の機密データが含まれています。 作成した DSC メタ構成は適切に保護し、使用後は削除してください。

### <a name="using-a-dsc-configuration"></a>DSC 構成の使用

1. ローカル環境のマシンで、管理者として PowerShell ISE を開きます。 マシンには最新バージョンの [WMF 5](http://aka.ms/wmf5latest) がインストールされている必要があります。
2. 次のスクリプトをローカルにコピーします。 このスクリプトには、メタ構成を作成するための PowerShell DSC 構成と、メタ構成の作成を開始するためのコマンドが含まれています。

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. お使いの Automation アカウントの登録キーと URL のほか、オンボードするマシンの名前を入力します。 その他のパラメーターはすべて省略可能です。 Automation アカウントの登録キーと登録 URL を見つける場合は、後述の「 [**セキュリティで保護された登録**](#secure-registration) 」を参照してください。
4. マシンが Azure Automation DSC に DSC のステータス情報を送信する一方で、構成や PowerShell モジュールを取得しないようにするには、 **ReportOnly** パラメーターを true に設定します。
5. スクリプトを実行します。 作業ディレクトリに、(管理者として) マシンをオンボードするための PowerShell DSC メタ構成が含まれる **DscMetaConfigs** という名前のフォルダーが作成されます。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Azure Automation コマンドレットの使用

PowerShell DSC Local Configuration Manager の既定値がユース ケースに適しており、Azure Automation DSC とデータをやり取りするマシンをオンボードする場合、Azure Automation コマンドレットが必要な DSC メタ構成を生成するための簡略化された手法を提供します。

1. ローカル環境のマシンで、管理者として PowerShell コンソールまたは PowerShell ISE を開きます。
2. **Add-AzureRmAccount**
3. ノードをオンボードする Automation アカウントから、オンボードするマシンの PowerShell DSC メタ構成をダウンロードします。

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. (管理者として) マシンをオンボードするための PowerShell DSC メタ構成が含まれる ***DscMetaConfigs*** という名前のフォルダーが作成されます。
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>セキュリティで保護された登録

マシンは WMF 5 DSC 登録プロトコルを介して Azure Automation アカウントに安全にオンボードすることができます。これにより、DSC ノードを PowerShell DSC V2 のプルまたはレポート サーバー (Azure Automation DSC を含む) に対して認証することができます。 ノードは**登録 URL** でサーバーに登録し、**登録キー**を使用して認証します。 登録時に、DSC ノードと DSC プル/レポート サーバーは、登録後のサーバーに対する認証に使用するこのノードの一意の証明書をネゴシエートします。 このプロセスは、ノードが侵害され、悪意のある動作を行っている場合などに、オンボードされたノードが別のノードの代わりに動作しないようにするためのものです。 登録後、登録キーは再度認証に使用されず、ノードから削除されます。

DSC 登録プロトコルに必要な情報は、Azure プレビュー ポータルの **[キーの管理]** ブレードから取得できます。 Automation アカウントの **[要点]** パネルにあるキー アイコンをクリックして、このブレードを開きます。

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* 登録 URL は、[キーの管理] ブレードの [URL] フィールドです。
* 登録キーは、[キーの管理] ブレードのプライマリ アクセス キーまたはセカンダリ アクセス キーです。 いずれかのキーを使用できます。

セキュリティ強化のため、Automation アカウントのプライマリおよびセカンダリ アクセス キーを ( **[キーの管理]** ブレードで) いつでも再生成して、以前のキーを使用して今後ノードが登録されないようにすることができます。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 仮想マシンのオンボードに関するトラブルシューティング

Azure Automation DSC を使用すると、構成管理のための Azure Windows VM を簡単にオンボードできます。 内部で、Azure VM Desired State Configuration 拡張機能を使用して、VM を Azure Automation DSC に登録します。 Azure VM Desired State Configuration 拡張機能は非同期に実行されるため、その進行状況の追跡とその実行に関するトラブルシューティングが重要な場合があります。

> [!NOTE]
> Azure VM Desired State Configuration 拡張機能を使用する Azure Automation DSC への Azure Windows VM のオンボード方法では、ノードが Azure Automation に登録済みと表示されるまでに最大で&1; 時間かかる場合があります。 これは、VM を Azure Automation DSC にオンボードするために必要な Windows Management Framework 5.0 を、Azure VM DSC 拡張機能が VM にインストールするためです。

トラブルシューティングを行う場合や、Azure VM Desired State Configuration 拡張機能の状態を表示する場合は、Azure Portal でオンボードされている VM に移動し、**[すべての設定]**  ->  **[拡張機能]**  ->  **[DSC]** の順にクリックします。 詳細は、 **[詳細な状態の表示]**をクリックして表示できます。

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## <a name="certificate-expiration-and-reregistration"></a>証明書の有効期限と再登録

Azure Automation DSC に DSC ノードとしてマシンを登録した後も、さまざまな理由で、そのノードを再登録する必要があります。

* 登録すると、各ノードは、1 年後に有効期限が切れる認証用の一意の証明書を自動的にネゴシエートします。 現時点では、PowerShell DSC 登録プロトコルは、有効期限が近づいたときに証明書を自動的に更新することはできないため、1 年後にノードを再登録する必要があります。 再登録する前に、各ノードで Windows Management Framework 5.0 RTM が実行されていることを確認します。 ノードの認証証明書の有効期限が切れるときにノードが再登録されない場合、ノードは Azure Automation と通信できなくなり、[反応なし] とマークされます。 証明書の有効期限が切れる 90 日以内または証明書の有効期限が切れた後で再登録を実行すると、新しい証明書が生成されて使用されます。
* ConfigurationMode など、ノードの最初の登録時に設定した [PowerShell DSC Configuration Manager 値](https://msdn.microsoft.com/powershell/dsc/metaconfig4)を変更する場合。 現在、DSC エージェント値を変更するには、再登録を使用する必要があります。 1 つの例外は、ノードに割り当てられたノード構成です。この場合、Azure Automation DSC で直接変更できます。

再登録は、ノードを最初に登録する場合と同様です。このドキュメントで説明しているオンボード方法のいずれかを使用します。 ノードを再登録する前に、Azure Automation DSC のノードの登録を解除する必要はありません。

## <a name="related-articles"></a>関連記事

* [Azure Automation DSC の概要](automation-dsc-overview.md)
* [Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure Automation DSC cmdlets (Azure Automation DSC の価格)](https://azure.microsoft.com/pricing/details/automation/)

