---
title: Azure Automation での資格情報資産
description: Azure Automation の資格情報資産には、Runbook または DSC 構成によってアクセスされるリソースの認証に使用できるセキュリティ資格情報が含まれます。 この記事では、資格情報資産を作成し、Runbook または DSC 構成でそれを使用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 582645919825c308fce4fe3211fa601955aaf37d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850178"
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation での資格情報資産

Automation 資格情報資産は、ユーザー名とパスワードなどのセキュリティ資格情報を含むオブジェクトを保持しています。 Runbook および DSC 構成では、認証に PSCredential オブジェクトを受け付けるコマンドレットを使用したり、PSCredential オブジェクトのユーザー名とパスワードを抽出して認証を必要とするアプリケーションやサービスに提供したりできます。 資格情報のプロパティは、Azure Automation に安全に格納されており、Runbook または DSC 構成で [Get-AutomationPSCredential](#activities) アクティビティを使用してアクセスできます。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。

## <a name="azure-classic-powershell-cmdlets"></a>Azure クラシック PowerShell コマンドレット

Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。  これらは、Automation Runbook および DSC 構成で使用できる [Azure PowerShell モジュール](/powershell/azure/overview) に付属しています。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |資格情報資産に関する情報を取得します。 **Get-AutomationPSCredential** アクティビティから取得できるのは、資格情報自体のみです。 |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |新しい Automation 資格情報を作成します。 |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Automation 資格情報を削除します。 |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |既存の Automation 資格情報のプロパティを設定します。 |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell コマンドレット

AzureRM の場合、Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。  これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [AzureRM.Automation モジュール](/powershell/azure/overview)に付属しています。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |資格情報資産に関する情報を取得します。 これにより、PSCredential オブジェクトは返されません。  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |新しい Automation 資格情報を作成します。 |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Automation 資格情報を削除します。 |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |既存の Automation 資格情報のプロパティを設定します。 |

## <a name="activities"></a>Activities

次の表のアクティビティは、Runbook および DSC 構成で資格情報にアクセスするために使用されます。

| Activities | 説明 |
|:--- |:--- |
| Get-AutomationPSCredential |Runbook または DSC 構成で使用する資格情報を取得します。 [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) オブジェクトを返します。 |

> [!NOTE]
> Get-AutomationPSCredential の –Name パラメーターを使用すると、設計時に Runbook または DSC 構成と資格情報資産の間の依存関係の検出が複雑になる可能性があるため、使用しないようにする必要があります。

## <a name="python2-functions"></a>Python2 関数

次の表の関数を使用して、Python2 Runbook の資格情報にアクセスします。

| Function | 説明 |
|:---|:---|
| automationassets.get_automation_credential | 資格情報資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある "automationassets" モジュールをインポートする必要があります。

## <a name="creating-a-new-credential-asset"></a>新しい資格情報資産の作成

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Azure ポータルで新しい資格情報資産を作成するには

1. Automation アカウントから、 **[共有リソース]** の下の **[資格証明]** を選択します。
1. **[+ 資格情報の追加]** をクリックします。
1. フォームに入力し、 **[作成]** をクリックして新しい資格情報を保存します。

> [!NOTE]
> 多要素認証を使用するユーザー アカウントは、Azure Automation での使用がサポートされていません。

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell で新しい資格情報資産を作成するには

次のサンプル コマンドでは、新しい Automation 資格情報を作成する方法について説明します。 最初に名前とパスワードで PSCredential オブジェクトを作成し、それを使用して資格情報資産を作成します。 または、 **Get-Credential** コマンドレットを使用し、プロンプトに従って名前とパスワードを入力することもできます。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 資格情報の使用

Runbook または DSC 構成で資格情報資産を取得するには、**Get-AutomationPSCredential** アクティビティを使用します。 このアクティビティが返す [PSCredential オブジェクト](/dotnet/api/system.management.automation.pscredential)を、PSCredential パラメーターを必要とするアクティビティまたはコマンドレットで使用できます。 また、資格情報オブジェクトのプロパティを取得して個別に使用することもできます。 オブジェクトにはユーザー名と安全なパスワードのプロパティがあります。または、**GetNetworkCredential** メソッドを使用して、安全ではないバージョンのパスワードを提供する [NetworkCredential](/dotnet/api/system.net.networkcredential) オブジェクトを取得することもできます。

> [!NOTE]
> **Get-AzureRmAutomationCredential** では、認証に使用できる **PSCredential** は返されません。 資格情報に関する情報が提供されるだけです。 Runbook で資格情報を使用する必要がある場合は、**Get-AutomationPSCredential** を使用して **PSCredential** オブジェクトを取得する必要があります。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル

次のサンプル コマンドでは、Runbook で PowerShell 資格情報を使用する方法を示します。 この例では、資格情報を取得し、そのユーザー名とパスワードを変数に代入します。

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

資格情報を使用して [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) で Azure を認証することもできます。 ほとんどの環境下で、[アカウントとして実行](../manage-runas-account.md) を使用して、それを [Get-AutomationConnection](../automation-connections.md) で取得する必要があります。

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで資格情報を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationPSCredential** アクティビティをグラフィカルな Runbook に追加します。

![キャンバスに資格情報を追加する](../media/credentials/credential-add-canvas.png)

次の図は、グラフィカルな Runbook で資格情報を使用する例を示したものです。  この例では、「 [Authenticate Runbooks with Azure AD User account (Azure AD ユーザー アカウントでの Runbook の認証)](../automation-create-aduser-account.md)」で説明されているように、Runbook 用の認証を Azure リソースに提供するために使用されています。  最初のアクティビティは、Azure サブスクリプションへのアクセス権を持つ資格情報を取得します。  その後、 **Add-AzureAccount** アクティビティはこの資格情報を使用して、その後にあるすべてのアクティビティに認証を提供します。  [Get-AutomationPSCredential](../automation-graphical-authoring-intro.md#links-and-workflow) は 1 つのオブジェクトを受け取るので、ここでは **パイプライン リンク** を使用します。  

![キャンバスに資格情報を追加する](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>DSC での PowerShell 資格情報の使用

Azure Automation の DSC 構成では **Get-AutomationPSCredential** を使用して資格情報資産を参照できますが、必要に応じて、パラメーターを使用して資格情報資産を渡すこともできます。 詳細については、「 [Azure Automation DSC での構成のコンパイル](../automation-dsc-compile.md#credential-assets)」を参照してください。

## <a name="using-credentials-in-python2"></a>Python2 の資格情報の使用

次のサンプルでは、Python2 Runbook で資格情報にアクセスする例を示します。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>次の手順

* グラフィカル作成でのリンクの詳細については、「 [グラフィカル作成でのリンク](../automation-graphical-authoring-intro.md#links-and-workflow)
* Automation のさまざまな認証方法を理解するには、「 [Azure Automation のセキュリティ](../automation-security-overview.md)
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](../automation-first-runbook-graphical.md)
* PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](../automation-first-runbook-textual.md)」を参照してください。
* Python2 Runbook の使用を開始するには、[初めての Python2 Runbook](../automation-first-runbook-textual-python2.md) に関するページをご覧ください 
