---
title: Azure Automation で資格情報を管理する
description: この記事では、資格情報資産を作成し、Runbook または DSC 構成でそれを使用する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9b9e42d55a982aeb55d7c9e26f7b1a6cbca32e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052806"
---
# <a name="manage-credentials-in-azure-automation"></a>Azure Automation で資格情報を管理する

Automation 資格情報資産は、ユーザー名とパスワードなどのセキュリティ資格情報を含むオブジェクトを保持しています。 Runbook と DSC 構成では、認証のための [PSCredential](/dotnet/api/system.management.automation.pscredential) オブジェクトを受け入れるコマンドレットを使用します。 または、`PSCredential` オブジェクトのユーザー名とパスワードを抽出して、認証を必要とする一部のアプリケーションまたはサービスに提供することもできます。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 Azure Automation では、キーはシステムによって管理される Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、Automation によって Key Vault からキーが読み込まれ、それを使用して資産が暗号化されます。 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>資格情報へのアクセスに使用する PowerShell コマンドレット

PowerShell を使用して Automation 資格情報を作成および管理するためのコマンドレットを次の表に示します。 これらは、[Az モジュール](modules.md#az-modules)の一部として出荷されます。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |資格情報に関するメタデータを格納している [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) オブジェクトを取得します。 このコマンドレットは `PSCredential` オブジェクト自体を取得しません。  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |新しい Automation 資格情報を作成します。 |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Automation 資格情報を削除します。 |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |既存の Automation 資格情報のプロパティを設定します。 |

## <a name="other-cmdlets-used-to-access-credentials"></a>資格情報へのアクセスに使用するその他のコマンドレット

次の表のコマンドレットは、Runbook および DSC 構成で資格情報にアクセスするために使用されます。 

| コマンドレット | 説明 |
|:--- |:--- |
| `Get-AutomationPSCredential` |Runbook または DSC 構成で使用する `PSCredential` オブジェクトを取得します。 `Get-AzAutomationCredential` コマンドレットは資格情報を取得するだけなので、ほとんどの場合、代わりにこの[内部コマンドレット](modules.md#internal-cmdlets)を使用する必要があります。 通常、この情報は別のコマンドレットに渡す場合には役に立ちません。 |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |ユーザー名とパスワードの入力を求めるプロンプトが表示された資格情報を取得します。 このコマンドレットは既定の Microsoft.PowerShell.Security モジュールの一部です。 「[既定のモジュール](modules.md#default-modules)」を参照してください。|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | 資格情報資産を作成します。 このコマンドレットは、既定の Azure モジュールの一部です。 「[既定のモジュール](modules.md#default-modules)」を参照してください。|

コード内の `PSCredential` オブジェクトを取得するには、`Orchestrator.AssetManagement.Cmdlets` モジュールをインポートする必要があります。 詳細については、「[Azure Automation でモジュールを管理する](modules.md)」をご覧ください。

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> `Get-AutomationPSCredential` の `Name` パラメーターに変数を使用することは避けてください。 この変数を使用すると、デザイン時に、Runbook または DSC 構成と資格情報資産との間の依存関係の検出が複雑になる可能性があります。

## <a name="python-functions-that-access-credentials"></a>資格情報にアクセスする Python 関数

次の表の関数を使用して、Python 2 および 3 の Runbook の資格情報にアクセスします。 Python 3 Runbook は現在プレビュー段階です。

| 機能 | 説明 |
|:---|:---|
| `automationassets.get_automation_credential` | 資格情報資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある `automationassets` モジュールをインポートする必要があります。

## <a name="create-a-new-credential-asset"></a>新しい資格情報資産を作成する

Azure portal または Windows PowerShell を使用して、新しい資格情報資産を作成できます。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Azure portal での新しい資格情報資産の作成

1. [Automation アカウント] から、左側のペインで、 **[共有リソース]** の **[資格情報]** を選択します。
2. **[資格情報]** ページで、 **[資格情報の追加]** を選択します。
3. [新しい資格情報] ウィンドウで、命名規則に従って、適切な資格情報名を入力します。
4. **[ユーザー名]** フィールドにアクセス ID を入力します。
5. 両方のパスワード フィールドに、シークレット アクセス キーを入力します。

    ![新しい資格情報を作成する](../media/credentials/credential-create.png)

6. [多要素認証] チェックボックスがオンになっている場合は、オフにします。
7. **[作成]** をクリックして、新しい資格情報資産を保存します。

> [!NOTE]
> Azure Automation では、多要素認証を使用するユーザー アカウントをサポートしていません。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell で新しい資格情報資産を作成する

次の例では、新しい Automation 資格情報資産を作成する方法について説明します。 最初に名前とパスワードで `PSCredential` オブジェクトを作成し、それを使用して資格情報資産を作成します。 または、`Get-Credential` コマンドレットを使用して、ユーザーに名前とパスワードの入力を求めることもできます。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>資格情報資産を取得する

Runbook または DSC 構成では、内部の `Get-AutomationPSCredential` コマンドレットを使用して、資格情報資産を取得します。 このコマンドレットは、資格情報を必要とするコマンドレットで使用できる `PSCredential` オブジェクトを取得します。 また、資格情報オブジェクトのプロパティを取得して個別に使用することもできます。 このオブジェクトには、ユーザー名とセキュリティで保護されたパスワードのプロパティがあります。 

> [!NOTE]
> `Get-AzAutomationCredential` コマンドレットでは、認証に使用できる `PSCredential` オブジェクトを取得しません。 資格情報に関する情報が提供されるだけです。 Runbook で資格情報を使用する必要がある場合は、`Get-AutomationPSCredential` を使用して `PSCredential` オブジェクトとして取得する必要があります。

または、[GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) メソッドを使用して、安全ではないバージョンのパスワードを表す [NetworkCredential](/dotnet/api/system.net.networkcredential) オブジェクトを取得することもできます。

### <a name="textual-runbook-example"></a>テキスト形式の Runbook の例

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の例では、Runbook で PowerShell 資格情報を使用する方法を示しています。 資格情報を取得して、そのユーザー名とパスワードを変数に割り当てます。

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

資格情報を使用して [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) で Azure を認証することもできます。 ほとんどの状況下では、[[実行アカウント]](../automation-security-overview.md#run-as-accounts) を使用して、[Get-AzAutomationConnection](../automation-connections.md) との接続を取得する必要があります。

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

次の例では、Python 2 Runbook で資格情報にアクセスする例を示しています。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

次の例では、Python 3 Runbook (プレビュー) で資格情報にアクセスする例を示しています。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>グラフィカルな Runbook の例

グラフィカル エディターの [ライブラリ] ペインで資格情報を右クリックし、 **[キャンバスに追加]** を選択することで、内部 `Get-AutomationPSCredential` コマンドレットのアクティビティをグラフィカルな Runbook に追加できます。

![資格情報コマンドレットをキャンバスに追加する](../media/credentials/credential-add-canvas.png)

次の図は、グラフィカルな Runbook で資格情報を使用する例を示したものです。 この例では、「[Azure Automation で Azure AD を使用して Azure に対して認証する](../automation-use-azure-ad.md)」で説明されているように、資格情報によって、Azure リソースに対する認証が Runbook に提供されています。 最初のアクティビティは、Azure サブスクリプションへのアクセス権を持つ資格情報を取得します。 その後、アカウント接続アクティビティは、この資格情報を使用して、その後にあるすべてのアクティビティに認証を提供します。 ここでは、`Get-AutomationPSCredential` が単一オブジェクトを想定しているため、[パイプライン リンク](../automation-graphical-authoring-intro.md#use-links-for-workflow)が使用されています。  

![パイプライン リンク例を伴った資格情報ワークフロー](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>DSC 構成で資格情報を使用する

Azure Automation の DSC 構成では、`Get-AutomationPSCredential` を使用して資格情報資産を操作できますが、パラメーターを使用して資格情報資産を渡すこともできます。 詳細については、「 [Azure Automation DSC での構成のコンパイル](../automation-dsc-compile.md#credential-assets)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 証明書へのアクセスに使用するコマンドレットの詳細については、「[Azure Automation でモジュールを管理する](modules.md)」を参照してください。
* Runbook の一般的な情報については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」を参照してください。
* DSC 構成の詳細については、「[Azure Automation State Configuration の概要](../automation-dsc-overview.md)」を参照してください。