---
title: Azure Automation の接続を管理する
description: この記事では、外部サービスまたはアプリケーションへの Azure Automation 接続を管理する方法と、Runbook でこれらの接続を使用する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054265"
---
# <a name="manage-connections-in-azure-automation"></a>Azure Automation の接続を管理する

Azure Automation の接続資産には、以下に示す情報が含まれています。 この情報は、Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要です。 

* ユーザー名やパスワードなどの認証に必要な情報
* URL やポートなどの接続情報

接続資産では、特定のアプリケーションに接続するためのすべてのプロパティがまとめて保持されるため、複数の変数を作成する必要がありません。 1 つの場所で接続のための値を編集でき、1 つのパラメーターで Runbook または DSC 構成に接続の名前を渡すことができます。 Runbook または構成は、内部の `Get-AutomationConnection` コマンドレットを使用して接続のプロパティにアクセスします。

接続を作成するときは、接続の種類を指定する必要があります。 接続の種類は、一連のプロパティを定義しているテンプレートです。 統合モジュールとメタデータ ファイルを使用して Azure Automation に接続の種類を追加できます。 統合モジュールに接続の種類が含まれており、Automation アカウントにインポートされる場合、[Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) を使用して接続の種類を作成することもできます。 

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 Azure Automation では、キーはシステムによって管理される Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、Automation によって Key Vault からキーが読み込まれ、それを使用して資産が暗号化されます。 

## <a name="connection-types"></a>接続の種類

Azure Automation によって、次の組み込みの接続の種類を使用できるようになります。

* `Azure`Azure - クラシック リソースを管理するために使用される接続を表します。
* `AzureServicePrincipal` - Azure 実行アカウントによって使用される接続を表します。
* `AzureClassicCertificate` - クラシック Azure 実行アカウントによって使用される接続を表します。

ほとんどの場合、[実行アカウント](automation-security-overview.md)の作成時に作成されるので、接続リソースを作成する必要はありません。

## <a name="powershell-cmdlets-to-access-connections"></a>接続にアクセスするための PowerShell コマンドレット

PowerShell を使用して Automation 接続を作成および管理するためのコマンドレットを次の表に示します。 これらは、[Az モジュール](shared-resources/modules.md#az-modules)の一部として出荷されます。

|コマンドレット|説明|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|接続に関する情報を取得します。|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|新しい接続を作成します。|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|既存の接続を削除します。|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|既存の接続の特定のフィールドの値を設定します。|

## <a name="internal-cmdlets-to-access-connections"></a>接続にアクセスするための内部コマンドレット

Runbook および DSC 構成内で接続にアクセスするための内部コマンドレットを次の表に示します。 このコマンドレットには、グローバル モジュール `Orchestrator.AssetManagement.Cmdlets` が付属しています。 詳細については、「[内部コマンドレット](shared-resources/modules.md#internal-cmdlets)」を参照してください。

|内部コマンドレット|説明|
|---|---|
|`Get-AutomationConnection` | 接続のさまざまなフィールドの値を取得し、それらを[ハッシュ テーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)として返します。 その後、このハッシュ テーブルを、Runbook または DSC 構成の適切なコマンドで使用できます。|

>[!NOTE]
>`Get-AutomationConnection` の `Name` パラメーターには変数を使用しないようにしてください。 この場合に変数を使用すると、デザイン時に、Runbook または DSC 構成と接続資産間の依存関係の検出が複雑になる可能性があります。

## <a name="python-functions-to-access-connections"></a>接続にアクセスするための Python 関数

次の表の関数は、Python 2 および 3 の Runbook の接続へのアクセスに使用します。 Python 3 Runbook は現在プレビュー段階です。

| 機能 | 説明 |
|:---|:---|
| `automationassets.get_automation_connection` | 接続を取得します。 接続のプロパティでディクショナリを返します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある `automationassets` モジュールをインポートする必要があります。

## <a name="create-a-new-connection"></a>新しい接続を作成する

### <a name="create-a-new-connection-with-the-azure-portal"></a>Azure portal で新しい接続を作成する

Azure portal で新しい接続を作成するには:

1. Automation アカウントから、 **[共有リソース]** の **[接続]** をクリックします。
2. [接続] ページで **[+ 接続の追加]** をクリックします。
4. [新しい接続] ウィンドウの **[種類]** フィールドで、作成する接続の種類を選択します。 選択できるのは、`Azure`、`AzureServicePrincipal`、および `AzureClassicCertificate` です。 
5. フォームには、選択した接続の種類のプロパティが表示されます。 フォームに入力し、 **[作成]** をクリックして新しい接続を保存します。

### <a name="create-a-new-connection-with-windows-powershell"></a>Windows PowerShell で新しい接続を作成する

Windows PowerShell で新しい接続を作成するには、`New-AzAutomationConnection` コマンドレットを使用します。 このコマンドレットには、接続の種類で定義されている各プロパティの値を定義しているハッシュ テーブルを受け取る `ConnectionFieldValues` パラメーターがあります。

新しい接続資産を作成するには、ポータルで実行アカウントを作成する代わりに、次のコマンド例を使用できます。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Automation アカウントを作成すると、`AzureRunAsConnection` 接続資産を作成する接続の種類である `AzureServicePrincipal` と一緒に、さまざまなグローバル モジュールが既定で含まれます。 別の認証方法を使用してサービスまたはアプリケーションに接続する新しい接続資産を作成しようとすると、接続の種類が Automation アカウントに定義されていないという理由で操作が失敗します。 カスタム モジュール用に独自の接続の種類を作成する方法については、「[接続の種類を追加する](#add-a-connection-type)」をご覧ください。

## <a name="add-a-connection-type"></a>接続の種類を追加する

Runbook または DSC 構成を外部サービスに接続する場合は、統合モジュールと呼ばれる[カスタム モジュール](shared-resources/modules.md#custom-modules)で接続の種類を定義する必要があります。 このモジュールは接続の種類のプロパティを指定するメタデータ ファイルを含み、名前は **&lt;ModuleName&gt;-Automation.json** となり、圧縮された **.zip** ファイルのモジュール フォルダー内に配置されます。 このファイルには、モジュールが示すシステムまたはサービスに接続するために必要な接続のフィールドが含まれています。 このファイルを使用して、接続の種類のフィールド名、データ型、暗号化の状態、および任意指定の状態を設定できます。 

次の例は、`MyModuleConnection` という名前のカスタム接続のユーザー名とパスワードのプロパティを定義する **.json** ファイル形式のテンプレートです。

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook または DSC 構成で接続を取得する

内部 `Get-AutomationConnection` コマンドレットを使用して、Runbook または DSC 構成で接続を取得します。 このコマンドレットは、接続に関する情報ではなく接続値を取得するため、`Get-AzAutomationConnection` コマンドレットよりも推奨されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の例は、実行アカウントを使用して Runbook 内で Azure Resource Manager リソースを認証する方法を示しています。 実行アカウントを表す接続資産を使用し、証明書ベースのサービス プリンシパルを参照します。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

次の例では、Python 2 および 3 の Runbook の実行接続を使用して認証する方法を示します。

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
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

---

### <a name="graphical-runbook-examples"></a>グラフィカルな Runbook の例

グラフィカルな Runbook に、内部コマンドレット `Get-AutomationConnection` のアクティビティを追加できます。 グラフィカル エディターの [ライブラリ] ウィンドウで接続を右クリックし、 **[キャンバスに追加]** を選択します。

![キャンバスに追加](media/automation-connections/connection-add-canvas.png)

次の図は、グラフィカルな Runbook で接続オブジェクトを使用する例を示したものです。 この例では、`Get RunAs Connection` アクティビティ用の `Constant value` データ セットを使用し、認証に接続オブジェクトを使用します。 `ServicePrincipalCertificate` パラメーターは、オブジェクトを 1 つ受け取るので、ここでは[パイプライン リンク](automation-graphical-authoring-intro.md#use-links-for-workflow)を使用します。

![接続の取得](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>次のステップ

* 接続にアクセスするためのコマンドレットの詳細については、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。
* Runbook の一般的な情報については、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。
* DSC 構成の詳細については、[State Configuration の概要](automation-dsc-overview.md)に関する記事を参照してください。