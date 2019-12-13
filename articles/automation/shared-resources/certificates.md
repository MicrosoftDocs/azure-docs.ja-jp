---
title: Azure Automation の証明書資産
description: 証明書を Azure Automation に安全に保存し、Runbook または DSC 構成でアクセスして Azure およびサードパーティのリソースで認証できます。  この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849481"
---
# <a name="certificate-assets-in-azure-automation"></a>Azure Automation の証明書資産

証明書を Azure Automation に安全に保存し、Azure Resource Manager リソースの **Get-AzureRmAutomationCertificate** アクティビティを使用して、Runbook または DSC 構成でアクセスできます。 この機能により、認証に証明書を使用する Runbook および DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは、システムで管理されたキー コンテナーに格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。 このプロセスは、Azure Automation によって管理されます。

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell コマンドレット

AzureRM の場合、Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [AzureRM.Automation モジュール](/powershell/azure/overview)に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Runbook または DSC 構成で使用する証明書についての情報を取得します。 Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|新しい証明書を Azure Automation に作成します。|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|証明書を Azure Automation から削除します。|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|指定されたクラウド サービスのサービス証明書をアップロードします。|

## <a name="activities"></a>Activities

Runbook および DSC 構成で証明書にアクセスするために、次の表のアクティビティが使用されます。

| Activities | 説明 |
|:---|:---|
|Get-AutomationCertificate|Runbook または DSC 構成で使用する証明書を取得します。 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) オブジェクトを返します。|

> [!NOTE] 
> Runbook または DSC 構成内で **Get-AutomationCertificate** の –Name パラメーターに変数を使用すると、設計時に Runbook または DSC 構成と Automation 変数の間の依存関係の検出が複雑になるため、使用しないようにする必要があります。

## <a name="python2-functions"></a>Python2 関数

次の表の関数を使用して、Python2 Runbook の証明書にアクセスします。

| Function | 説明 |
|:---|:---|
| automationassets.get_automation_certificate | 証明書の資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の最初の **automationassets** モジュールをインポートする必要があります。

## <a name="creating-a-new-certificate"></a>新しい証明書の作成

新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。 証明書をエクスポート可能とマークすると、Azure Automation の証明書ストアから転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。 Azure Automation では、証明書のプロバイダーが **Microsoft Enhanced RSA and AES Cryptographic Provider** である必要があります。

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure ポータルで新しい証明書を作成するには

1. Automation アカウントから、 **[資産]** タイルをクリックして **[資産]** ページを開きます。
2. **[証明書]** タイルをクリックして **[証明書]** ページを開きます。
3. ページ上部の **[証明書の追加]** をクリックします。
4. Type a name for the certificate in the **Name** box.
5. .cer または .pfx ファイルを参照するには、 **[Upload a certificate file]\(証明書ファイルのアップロード\)** の **[ファイルの選択]** をクリックします。 .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
6. **[作成]** をクリックして、新しい証明書資産を保存します。

### <a name="to-create-a-new-certificate-with-powershell"></a>PowerShell で新しい証明書を作成するには

次の例では、新しい Automation 証明書を作成してエクスポート可能に指定する方法について説明します。 これは既存の .pfx ファイルをインポートします。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Resource Manager テンプレートで新しい証明書を作成する

次の例では、PowerShell で Resource Manager テンプレートを使用して Automation アカウントに証明書をデプロイする方法を示します。

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>証明書の使用

証明書を使用するには、**Get-AutomationCertificate** アクティビティを使用する必要があります。 [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル

次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>グラフィカルな Runbook のサンプル

[ライブラリ] ウィンドウで証明書を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationCertificate** をグラフィカルな Runbook に追加します。

![証明書をキャンバスに追加します](../media/certificates/automation-certificate-add-to-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。 これは、テキストの Runbook からクラウド サービスに証明書を追加する方法を示した前の例と同じです。

![グラフィカル作成の例](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 サンプル

次の例では、Python2 Runbook の証明書にアクセスする方法を示します。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>次の手順

- リンクを使用して、Runbook で実行するように設計されているアクティビティの論理フローを制御する方法については、「[グラフィカル作成でのリンク](../automation-graphical-authoring-intro.md#links-and-workflow)」をご覧ください。 
