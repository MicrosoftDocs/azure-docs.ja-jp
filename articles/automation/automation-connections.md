---
title: Azure Automation での接続資産
description: Azure Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。 この記事では、接続の詳細およびテキスト作成とグラフィカル作成の両方で接続を使用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 621441afaa9bef08a8ebf3b0af082c6a17c77b1b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850433"
---
# <a name="connection-assets-in-azure-automation"></a>Azure Automation での接続資産

Automation の接続資産には、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な情報が含まれます。 これには、URL やポートなどの接続情報に加えて、ユーザー名とパスワードなど認証に必要な情報も含まれる場合があります。 接続の値は、複数の変数を作成する場合とは対照的に、1 つの資産内の特定のアプリケーションに接続するためのプロパティをすべて保持します。 ユーザーは、1 つの場所で接続のための値を編集でき、1 つのパラメーターで Runbook または DSC 構成に接続の名前を渡すことができます。 Runbook または DSC 構成では、 **Get-AutomationConnection** アクティビティで接続のプロパティにアクセスできます。

接続を作成するときは、 *接続の種類*を指定する必要があります。 接続の種類は、一連のプロパティを定義しているテンプレートです。 接続では、その接続の種類で定義されている各プロパティの値を定義します。 接続の種類は Azure Automation の統合モジュールに追加されるか、[Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) によって作成され (統合モジュールに接続の種類が含まれていない場合)、Automation アカウントにインポートされます。 それ以外の場合は、Automation の接続の種類を指定するメタデータ ファイルを作成する必要があります。  詳細については、「[Azure Automation 統合モジュール](automation-integration-modules.md)」を参照してください。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは、システムで管理されたキー コンテナーに格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。 このプロセスは、Azure Automation によって管理されます。

## <a name="connection-types"></a>接続の種類

Azure Automation では、3 種類の組み込み接続を使用できます。

* **Azure** - この接続を使用すると、クラシック リソースを管理できます。
* **AzureClassicCertificate** - この接続は、**AzureClassicRunAs** アカウントによって使用されます。
* **AzureServicePrincipal** - この接続は、**AzureRunAs** アカウントによって使用されます。

ほとんどの場合、接続リソースは [RunAs アカウント](manage-runas-account.md)を作成するときに作成されるので、接続リソースを作成する必要はありません。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell コマンドレット

Windows PowerShell で Automation 接続を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Azure PowerShell モジュール](/powershell/azure/overview) に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|接続を取得します。 接続のフィールドの値のハッシュ テーブルが含まれます。|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|新しい接続を作成します。|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|既存の接続を削除します。|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|既存の接続の特定のフィールドの値を設定します。|

## <a name="activities"></a>Activities

次の表のアクティビティは、Runbook または DSC 構成で接続にアクセスするために使用されます。

|Activities|説明|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|使用する接続を取得します。 接続のプロパティのハッシュ テーブルを返します。|

>[!NOTE]
>**Get-AutomationConnection** の –Name パラメーターに変数を使用すると、設計時に Runbook または DSC 構成と接続資産の間の依存関係を検出することが複雑になる場合があるため、この使用は避けるようにしてください。


## <a name="python2-functions"></a>Python2 関数
次の表の関数を使用して、Python2 Runbook の接続にアクセスします。

| Function | 説明 |
|:---|:---|
| automationassets.get_automation_connection | 接続を取得します。 接続のプロパティでディクショナリを返します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある "automationassets" モジュールをインポートする必要があります。

## <a name="creating-a-new-connection"></a>新しい接続の作成

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Azure ポータルで新しい接続を作成するには

1. Automation アカウントから、 **[資産]** 部分をクリックして **[資産]** ブレードを開きます。
2. **[接続]** 部分をクリックして、 **[接続]** ブレードを開きます。
3. ブレード上部の **[接続の追加]** をクリックします。
4. **[種類]** ドロップダウンで、作成する接続の種類を選択します。 フォームにその特定の種類のプロパティが表示されます。
5. フォームに入力し、 **[作成]** をクリックして新しい接続を保存します。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Windows PowerShell で新しい接続を作成するには

Windows PowerShell の [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) コマンドレットを使用して新しい接続を作成します。 このコマンドレットには、接続の種類で定義されている各プロパティの値を定義している **ハッシュ テーブル** を受け取る [ConnectionFieldValues](https://technet.microsoft.com/library/hh847780.aspx) という名前のパラメーターがあります。

Automation の [Run As アカウント](automation-sec-configure-azure-runas-account.md)でサービス プリンシパルを使用した Runbook の認証に慣れている場合は、ポータルから Run As アカウントを作成するための代替方法として用意されている PowerShell スクリプトを使用して、新しい接続資産を作成できます。次のサンプル コマンドを使用できます。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

このスクリプトを使用して接続資産を作成できる理由は、Automation アカウントを作成すると、**AzureRunAsConnection** 接続資産を作成する接続の種類である **AzureServicePrincipal** と一緒に、さまざまなグローバル モジュールが既定で自動的に含まれるためです。  別の認証方法を使用してサービスまたはアプリケーションに接続する新しい接続資産を作成しようとすると、接続の種類が Automation アカウントに定義されていないという理由で操作が失敗するため、このことを覚えておく必要があります。  [PowerShell ギャラリー](https://www.powershellgallery.com)からカスタム モジュール用の独自の接続の種類を作成する方法については、「[Azure Automation 統合モジュール](automation-integration-modules.md)」を参照してください。

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook または DSC 構成での接続の使用

**Get-AutomationConnection** コマンドレットを使用して、Runbook または DSC 構成の接続を取得します。  [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) アクティビティは使用できません。  このアクティビティは、接続のさまざまなフィールド値を取得し、その値を[ハッシュ テーブル](https://go.microsoft.com/fwlink/?LinkID=324844)として返します。このハッシュ テーブルは、Runbook または DSC 構成の適切なコマンドで使用できます。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル

次のサンプル コマンドは、先に述べた Run As アカウントを使用して Runbook 内の Azure Resource Manager リソースを認証する方法を示しています。  Run As アカウントを表す接続資産を使用しし、資格情報ではなく、証明書ベースのサービス プリンシパルを参照します。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントでモジュールを更新できます。

### <a name="graphical-runbook-samples"></a>グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで接続を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationConnection** アクティビティをグラフィカルな Runbook に追加します。

![キャンバスに追加](media/automation-connections/connection-add-canvas.png)

次の図は、グラフィカルな Runbook で接続を使用する例を示したものです。  この例は、Run As アカウントを使用してテキストの Runbook を認証する上の例と同じです。  この例では、認証用の接続オブジェクトを使用する **Get RunAs Connection** アクティビティ用の**定数値**のデータ セットを使用します。  ServicePrincipalCertificate パラメーターが単一のオブジェクトを予期するように設定されているため、ここでは[パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow)が使用されます。

![接続の取得](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 Runbook のサンプル
次のサンプルでは、Python2 Runbook の Run As 接続を使用して認証する方法を示します。

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>次の手順

- [グラフィカル オーサリング内のリンク](automation-graphical-authoring-intro.md#links-and-workflow)に関する記事を参照して、Runbook のロジック フローを制御する方法を理解します。

- Azure Automation での PowerShell モジュールの使用方法と Azure Automation 内で統合モジュールとして動作する独自の PowerShell モジュールを作成するためのベスト プラクティスについては、「[Azure Automation 統合モジュール](automation-integration-modules.md)」を参照してください。

