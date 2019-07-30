---
title: Azure Automation の実行アカウントを管理する
description: この記事では、PowerShell またはポータルを使用して、実行アカウントを管理する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 49b8554f6064f036d4305cf7a5c1450c2f18c48d
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798472"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation の実行アカウントを管理する

Azure Automation の実行アカウントは、Azure コマンドレットを使用して Azure のリソースを管理するための認証を提供するために使用されます。

実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。 Azure 仮想マシン上で Hybrid Runbook Worker を使用する Runbook の場合は、実行アカウントの代わりに [Azure リソースのマネージド ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を使用して Azure リソースに対して認証できます。

実行アカウントには、次の 2 種類があります。

* **Azure 実行アカウント** - このアカウントは、[Resource Manager デプロイ モデル](../azure-resource-manager/resource-manager-deployment-model.md)のリソースを管理するために使用されます。
  * 自己署名証明書を含む Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの共同作成者ロールが割り当てられます。 この設定は所有者ロールや他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
  * 指定された Automation アカウントに *AzureRunAsCertificate* という名前の Automation 証明書資産を作成します。 この証明書資産には、Azure AD アプリケーションによって使用される証明書の秘密キーが格納されます。
  * 指定された Automation アカウントに *AzureRunAsConnection* という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

* **Azure クラシック実行アカウント** - このアカウントは、[クラシック デプロイ モデル](../azure-resource-manager/resource-manager-deployment-model.md)のリソースを管理するために使用されます。
  * サブスクリプションで管理証明書を作成します
  * 指定された Automation アカウントに *AzureClassicRunAsCertificate* という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。
  * 指定された Automation アカウントに *AzureClassicRunAsConnection* という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、証明書の資産名が格納されます。
  * 作成または更新するには、サブスクリプションの共同管理者である必要があります
  
  > [!NOTE]
  > Azure Cloud Solution Provider (Azure CSP) サブスクリプションは、Azure Resource Manager モデルのみをサポートしているため、Azure Resource Manager サービス以外のサービスはこのプログラムでは利用できません。 CSP サブスクリプションを使用する場合、Azure クラシック実行アカウントは作成されません。 Azure 実行アカウントは引き続き作成されます。 CSP サブスクリプションの詳細については、[CSP サブスクリプションで利用可能なサービス](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)に関するページを参照してください。

  > [!NOTE]
  > 実行アカウントのサービス プリンシパルには、既定で、Azure Active Directory を読み取るためのアクセス許可はありません。 Azure Active Directory の読み取りまたは管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

## <a name="permissions"></a>実行アカウントを構成するためのアクセス許可

実行アカウントを作成または更新するには、特定の特権およびアクセス許可が必要です。 Azure Active Directory の全体管理者と、サブスクリプションの所有者は、すべてのタスクを完了することができます。 職務権限を分離している状況では、タスク、相当するコマンドレット、および必要なアクセス許可の一覧を次の表に示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure Active Directory] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure Active Directory] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[ホーム] > [Azure Active Directory] > [アプリの登録]|
|指定されたプリンシパルの RBAC ロールを割り当てるまたは取得する|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | 次のアクセス許可を持っている必要があります。</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>または、次でなければなりません。</br></br>ユーザー アクセス管理者または所有者        | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<サブスクリプション名\>-アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | リソース グループの共同作成者         |Automation アカウントのリソース グループ|
|Automation の接続を作成または削除する|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|リソース グループの共同作成者 |Automation アカウントのリソース グループ|

<sup>1</sup> **[ユーザー設定]** ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録する](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)ことができます。 [アプリの登録] 設定が **[いいえ]** に設定されている場合、この操作を行うユーザーは、前述の表に定義されているユーザーである必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの**全体管理者**ロールに追加された場合は、ゲストとして追加されます。 このような状況では、`You do not have permissions to create…` 警告が **[Add Automation Account]\(Automation アカウントの追加\)** ページに表示されます。 最初に**全体管理者**ロールに追加されていたユーザーは、サブスクリプションの Active Directory インスタンスから削除してから再追加することで、Active Directory の完全なユーザーにすることができます。 このような状況を検証するには、Azure Portal の **[Azure Active Directory]** ウィンドウで、 **[ユーザーとグループ]** 、 **[すべてのユーザー]** 、特定のユーザー、 **[プロファイル]** の順に選択します。 ユーザーのプロファイルの下部にある **[ユーザー タイプ]** 属性の値は、 **[ゲスト]** と一致しないようにする必要があります。

## <a name="permissions-classic"></a>実行アカウントを構成するためのアクセス許可

従来の実行アカウントを構成または更新するには、サブスクリプション レベルで**共同管理者**ロールが必要です。 従来のアクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

## <a name="create-a-run-as-account-in-the-portal"></a>ポータルで実行アカウントを作成する

このセクションでは、以下の手順に従って、Azure Portal で Azure Automation アカウントを更新します。 実行アカウントとクラシック実行アカウントをそれぞれ作成します。 クラシック リソースを管理する必要がない場合は、Azure 実行アカウントのみを作成できます。  

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。
2. Azure Portal で、 **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Automation**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Automation アカウント]** を選択します。
3. **[Automation アカウント]** ページで、Automation アカウントの一覧からご使用の Automation アカウントを選択します。
4. 左側のウィンドウの **[アカウント設定]** セクションで、 **[実行アカウント]** を選択します。  
5. 必要なアカウントに応じて、 **[Azure 実行アカウント]** または **[Azure クラシック実行アカウント]** を選択します。 **[Azure 実行アカウントを追加する]** または **[Azure クラシック実行アカウントを追加する]** のどちらかを選択すると、ウィンドウが表示されます。概要情報を確認してから、 **[作成]** をクリックして実行アカウントの作成を進めます。  
6. Azure によって実行アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。 アカウントの作成中であることを示すバナーも表示されます。 このプロセスが完了するまでに数分かかることがあります。  

## <a name="create-run-as-account-using-powershell"></a>PowerShell を使用して実行アカウントを作成する

## <a name="prerequisites"></a>前提条件

次の一覧は、PowerShell で実行アカウントを作成するための要件を示しています。

* Azure Resource Manager モジュール 3.4.1 以降がインストールされた Windows 10 または Windows Server 2016。 PowerShell スクリプトは以前のバージョンの Windows をサポートしていません。
* Azure PowerShell 1.0 以降。 PowerShell 1.0 リリースについては、[Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。
* Automation アカウント。このアカウントは、 *–AutomationAccountName* パラメーターと *-ApplicationDisplayName* パラメーターの値として参照されます。
* [実行アカウントを構成するために必要なアクセス許可](#permissions)に記載されているものに相当するアクセス許可

スクリプトの必須パラメーターである *SubscriptionID*、*ResourceGroup*、*AutomationAccountName* の値を取得するには、次の手順を実行します。

1. Azure Portal で、 **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Automation**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Automation アカウント]** を選択します。
1. [Automation アカウント] ページで自分の Automation アカウントを選択し、 **[アカウント設定]** で **[プロパティ]** を選択します。  
1. **[プロパティ]** ページの **[サブスクリプション ID]** 、 **[名前]** 、および **[リソース グループ]** の値をメモします。

   ![Automation アカウントの [プロパティ] ページ](media/manage-runas-account/automation-account-properties.png)

この PowerShell スクリプトでは、以下の構成がサポートされます。

* 自己署名証明書を使用して実行アカウントを作成する。
* 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* エンタープライズ証明機関 (CA) によって発行された証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

>[!NOTE]
> クラシック実行アカウントを作成するオプションを選択した場合、スクリプトの実行後に、Automation アカウントが作成されたサブスクリプションの管理ストアに公開証明書 (.cer ファイル名拡張子) をアップロードしてください。

1. ご使用のコンピューターに次のスクリプトを保存します。 この例では、*New-RunAsAccount.ps1* というファイル名で保存します。

   スクリプトでは、複数の Azure Resource Manager コマンドレットを使用してリソースを作成します。 前の[アクセス許可](#permissions)の表に、コマンドレットとその実行に必要なアクセス許可を示しています。

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントで[モジュールを更新](automation-update-azure-modules.md)できます。

1. コンピューターの**スタート**画面から、昇格されたユーザー権限で **Windows PowerShell** を起動します。
1. 昇格されたコマンドライン シェルから、手順 1. で作成したスクリプトがあるフォルダーに移動します。  
1. 必要な構成に応じたパラメーター値を使ってスクリプトを実行します。

    **自己署名証明書を使用して実行アカウントを作成する**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > スクリプトの実行後、Azure に対する認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインしてください。

スクリプトが正常に実行されたら、次のことを確認してください。

* 自己署名公開証明書 (.cer ファイル) を使ってクラシック実行アカウントを作成した場合、コンピューターの一時ファイル フォルダーに証明書が作成されて保存されます。PowerShell セッションの実行に使用したユーザー プロファイル下の *%USERPROFILE%\AppData\Local\Temp* を探してください。

* エンタープライズ公開証明書 (.cer ファイル) を使ってクラシック実行アカウントを作成した場合は、その証明書を使用します。 [Azure portal への管理 API 証明書のアップロード](../azure-api-management-certs.md)に関するページの手順に従ってください。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除

このセクションでは、実行アカウントまたはクラシック実行アカウントを削除して再作成する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 削除した実行アカウントまたはクラシック実行アカウントは、Azure Portal で再作成できます。

1. Azure Portal で Automation アカウントを開きます。

2. **[Automation アカウント]** ページで **[実行アカウント]** を選択します。

3. **実行アカウント**のプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。 次に、選択したアカウントの **[プロパティ]** ウィンドウで **[削除]** をクリックします。

   ![Azure 実行アカウントを削除する](media/manage-runas-account/automation-account-delete-runas.png)

1. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

1. 削除したアカウントは、**実行アカウント**のプロパティ ページで再作成できます。このとき、作成オプションとして **[Azure 実行アカウント]** を選択します。

   ![Automation 実行アカウントの再作成](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>自己署名証明書の書き換え

証明書は、実行アカウントの有効期限が切れる前のどこかの時点で更新する必要があります。 また、実行アカウントが侵害されたと思われる場合は、実行アカウントを削除して再作成することができます。 このセクションでは、これらの操作を実行する方法について説明します。

実行アカウント用に作成された自己署名証明書は、作成日から 1 年後に有効期限が切れます。 期限切れになる前に、いつでも書き換えることができます。 証明書を書き換えるとき、実行待ちまたは実行中の Runbook が悪影響を受けないようにすると共に、その実行アカウントでの認証に支障をきたさないよう、現行の有効な証明書は保持されます。 証明書は、有効期限日を迎えるまで存在し続けます。

> [!NOTE]
> エンタープライズ証明機関によって発行された証明書を使用するように Automation 実行アカウントを構成している場合、このオプションを使用すると、そのエンタープライズ証明書は自己署名証明書に置き換えられます。

証明書を書き換えるには、次の手順に従います。

1. Azure Portal で Automation アカウントを開きます。

1. **[Account Settings]\(アカウント設定\)** で **[Run As Accounts]\(実行アカウント\)** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/manage-runas-account/automation-account-properties-pane.png)

1. **実行アカウント**のプロパティ ページで、証明書を書き換える実行アカウントまたはクラシック実行アカウントを選択します。

1. 選択したアカウントの **[プロパティ]** ウィンドウで、 **[証明書の書き換え]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="limiting-run-as-account-permissions"></a>実行アカウントのアクセス許可の制限

Azure 内のリソースに対する自動化の対象を制御するために、PowerShell ギャラリーの [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) スクリプトを実行し、既存の実行アカウントのサービス プリンシパルを変更して、カスタム ロールの定義を作成および使用できます。 このロールには、[Key Vault](https://docs.microsoft.com/azure/key-vault/) を除くすべてのリソースへのアクセス許可があります。 

> [!IMPORTANT]
> `Update-AutomationRunAsAccountRoleAssignments.ps1` スクリプトの実行後、実行アカウントを使用して key Vault にアクセスする Runbook は機能しなくします。 Azure key Vault を呼び出すには、アカウント内の Runbook を見直す必要があります。
>
> Azure Automation Runbook から key Vault にアクセスできるようにするには、[Key Vault のアクセス許可に 実行アカウントを追加する](#add-permissions-to-key-vault)必要があります。

実行サービス プリンシパルで実行できる内容をさらに制限する必要がある場合は、カスタム ロール定義の `NotActions` に他のリソースの種類を追加できます。 次の例では、`Microsoft.Compute` へのアクセスが制限されます。 これをロール定義の **NotActions** に追加すると、このロールはどのコンピューティング リソースにもアクセスできなくなります。 ロール定義の詳細については、「[Azure リソースのロール定義の概要](../role-based-access-control/role-definitions.md)」を参照してください。

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

実行アカウントによって使用されるサービス プリンシパルが**共同作成者**に含まれるか、あるいはカスタム ロール定義であるかを確認するには、Automation アカウントに移動し、 **[アカウント設定]** で **[実行アカウント]**  >  **[Azure 実行アカウント]** を選択します。 **[ロール]** の下に、使用されているロール定義が見つかります。 

[![](media/manage-runas-account/verify-role.png "実行アカウントのロールを確認する")](media/manage-runas-account/verify-role-expanded.png#lightbox)

複数のサブスクリプションまたは Automation アカウントの Automation 実行アカウントによって使用されるロール定義を確認するには、PowerShell ギャラリーの [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) スクリプトを使用できます。

### <a name="add-permissions-to-key-vault"></a>Key Vault へのアクセス許可を追加する

Azure Automation で Key Vault を管理できるようにするときに、実行アカウントのサービス プリンシパルでカスタム ロール定義を使用している場合は、この動作が可能になるように追加の手順を実行する必要があります。

* Key Vault へのアクセス許可を付与する
* アクセス ポリシーを設定する

PowerShell ギャラリーの [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) スクリプトを使用して、実行アカウントにKeyVault へのアクセス許可を付与することができます。または、[アプリケーションに対するキー コンテナーへのアクセスの付与](../key-vault/key-vault-group-permissions-for-apps.md)に関するページを参照して、KeyVault へのアクセス許可の設定の詳細をご確認ください。

## <a name="misconfiguration"></a>誤った構成

実行アカウントまたはクラシック実行アカウントが正しく機能するうえで必要な構成項目が削除されたり、初期セットアップ中に適切に作成されなかったりすることがあります。 たとえば、次のような項目です。

* 証明書資産
* 接続資産
* 共同作業者ロールからの実行アカウントの削除
* Azure AD のサービス プリンシパルまたはアプリケーション

このような誤った構成については、Automation アカウントによって変更が検出され、対応する**実行アカウント**のプロパティ ウィンドウに *[不完全]* 状態として表示されます。

![実行アカウントの構成が不完全な状態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

この実行アカウントを選択すると、アカウントの **[プロパティ]** ウィンドウに次のエラー メッセージが表示されます。

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

こうした実行アカウントの問題は、アカウントを削除してから再作成すればすぐに解決できます。

## <a name="next-steps"></a>次の手順

* サービス プリンシパルの詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)に関するページを参照してください。
* 証明書と Azure サービスの詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。
