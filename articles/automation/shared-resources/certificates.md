---
title: Azure Automation で証明書を管理する
description: この記事では、Runbook および DSC 構成によるアクセスに必要な証明書を操作する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734812"
---
# <a name="manage-certificates-in-azure-automation"></a>Azure Automation で証明書を管理する

Azure Automation では証明書を安全に保存し、Azure Resource Manager リソースの [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) コマンドレットを使用して、Runbook および DSC 構成でアクセスできるようにします。 証明書を安全に保存できることで、認証に証明書を使用する Runbook および DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Automation に保存されます。 Automation では、キーはシステムによって管理される Key Vault サービスに格納されます。 セキュリティで保護された資産を保存する前に、Automation によって Key Vault からキーが読み込まれ、それを使用して資産が暗号化されます。

## <a name="powershell-cmdlets-to-access-certificates"></a>証明書にアクセスするための PowerShell コマンドレット

PowerShell を使用して Automation 証明書を作成および管理するためのコマンドレットを次の表に示します。 これらは、[Az モジュール](modules.md#az-modules)に付属しています。

|コマンドレット |説明|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Runbook または DSC 構成で使用する証明書についての情報を取得します。 内部 `Get-AutomationCertificate` コマンドレットを使用して取得できるのは、証明書自体のみです。|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|新しい証明書を Automation に作成します。|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|証明書を Automation から削除します。|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|証明書ファイルのアップロードや **.pfx** ファイルのパスワードの設定など、既存の証明書のプロパティを設定します。|

[Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) コマンドレットを使用して、指定したクラウド サービスのサービス証明書をアップロードすることもできます。

## <a name="internal-cmdlets-to-access-certificates"></a>証明書にアクセスするための内部コマンドレット

次の表の内部コマンドレットを使用すると、Runbook の証明書にアクセスできます。 このコマンドレットには、グローバル モジュール `Orchestrator.AssetManagement.Cmdlets` が付属しています。 詳細については、「[内部コマンドレット](modules.md#internal-cmdlets)」を参照してください。

| 内部コマンドレット | 説明 |
|:---|:---|
|`Get-AutomationCertificate`|Runbook または DSC 構成で使用する証明書を取得します。 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) オブジェクトを返します。|

> [!NOTE]
> Runbook または DSC 構成で、`Get-AutomationCertificate` の `Name` パラメーターに変数を使用することは避けてください。 このような変数を使用すると、デザイン時に、Runbook または DSC 構成と Automation 変数との間の依存関係の検出が複雑になる可能性があります。

## <a name="python-functions-to-access-certificates"></a>証明書にアクセスするための Python 関数

次の表の関数を使用して、Python 2 および 3 Runbook の証明書にアクセスします。 Python 3 Runbook は現在プレビュー段階です。

| 機能 | 説明 |
|:---|:---|
| `automationassets.get_automation_certificate` | 証明書の資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の冒頭で `automationassets` モジュールをインポートする必要があります。

## <a name="create-a-new-certificate"></a>新しい証明書を作成する

新しい証明書を作成するときは、.cer ファイルか .pfx ファイルを Automation にアップロードします。 証明書をエクスポート可能とマークすると、Automation の証明書ストアから証明書を転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。 Automation では、証明書のプロバイダーが **Microsoft Enhanced RSA and AES Cryptographic Provider** である必要があります。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Azure portal で新しい証明書を作成する

1. [Automation アカウント] から、左側のウィンドウで、 **[共有リソース]** の **[証明書]** を選択します。
1. **[証明書]** ページで **[証明書の追加]** を選択します。
1. **[名前]** フィールドに、証明書の名前を入力します。
1. **.cer** ファイルまたは **.pfx** ファイルを参照するには、 **[Upload a certificate file]\(証明書ファイルのアップロード\)** の **[ファイルの選択]** を選択します。 **.pfx** ファイルを選択する場合は、パスワードを指定し、エクスポートを許可するかどうかを指定します。
1. **[作成]** を選択して、新しい証明書資産を保存します。

### <a name="create-a-new-certificate-with-powershell"></a>PowerShell で新しい証明書を作成する

次の例では、新しい Automation 証明書を作成してエクスポート可能に指定する方法について説明します。 この例では既存の **.pfx** ファイルをインポートします。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Resource Manager テンプレートで新しい証明書を作成する

次の例では、PowerShell で Resource Manager テンプレートを使用して Automation アカウントに証明書をデプロイする方法を示します。

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>証明書を取得する

証明書を取得するには、内部 `Get-AutomationCertificate` コマンドレットを使用します。 [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### <a name="textual-runbook-examples"></a>テキスト形式の Runbook の例

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の例では、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

次の例では、Python 2 Runbook の証明書にアクセスする方法を示します。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

次の例では、Python 3 Runbook (プレビュー) の証明書にアクセスする方法を示します。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>グラフィカルな Runbook の例

[ライブラリ] ペインで証明書を右クリックして **[キャンバスに追加]** を選択することにより、内部 `Get-AutomationCertificate` コマンドレットのアクティビティをグラフィカルな Runbook に追加します。

![証明書をキャンバスに追加する操作のスクリーンショット](../media/certificates/automation-certificate-add-to-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。

![グラフィカル作成の例のスクリーンショット](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>次のステップ

* 証明書へのアクセスに使用するコマンドレットの詳細については、「[Azure Automation でモジュールを管理する](modules.md)」を参照してください。
* Runbook の一般的な情報については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」を参照してください。
* DSC 構成の詳細については、「[Azure Automation State Configuration の概要](../automation-dsc-overview.md)」を参照してください。
