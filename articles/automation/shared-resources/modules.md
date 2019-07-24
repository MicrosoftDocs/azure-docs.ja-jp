---
title: Azure Automation でモジュールを管理する
description: この記事では、Azure Automation でモジュールを管理する方法について説明します
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905359"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation でモジュールを管理する

Azure Automation では、PowerShell ベースの Runbook で使用するために Automation アカウントに PowerShell モジュールをインポートする機能が用意されています。 これらのモジュールは、PowerShell ギャラリーから作成したカスタム モジュールの場合も、Azure 用の AzureRM および Az モジュールの場合もあります。

## <a name="import-modules"></a>モジュールをインポートする

Automation アカウントにモジュールをインポートする方法は複数あります。 以降のセクションで、モジュールをインポートするさまざまな方法を示します。

> [!NOTE]
> Azure Automation で使用されるモジュール内のファイルの最大のパスは 140 文字です。 140 文字を超えるパスはいずれも、`Import-Module` で PowerShell セッションにインポートすることはできません。

### <a name="powershell"></a>PowerShell

[New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) を使用してモジュールを Automation アカウントにインポートできます。 このコマンドレットは、モジュール zip パッケージへの URL を取ります。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure ポータル

Azure portal で、Automation アカウントに移動し、**[共有リソース]** の下で **[モジュール]** を選択します。 **[+ モジュールの追加]** をクリックします。 モジュールを含んだ **.zip** ファイルを選択して、**[OK]** をクリックしてプロセスのインポートを開始します。

### <a name="powershell-gallery"></a>PowerShell ギャラリー

PowerShell ギャラリーからのモジュールは、直接 [PowerShell ギャラリー](https://www.powershellgallery.com)からか、Automation アカウントからインポートできます。

PowerShell ギャラリーからモジュールをインポートするには、 https://www.powershellgallery.com に移動し、インポートするモジュールを検索します。 **[インストール オプション]** の下にある **[Azure Automation]** タブで **[Deploy to Azure Automation]**(Azure Automation にデプロイする) をクリックします。 このアクションにより Azure portal が開きます。 **[インポート]** ページで、Automation アカウントを選択し、**[OK]** をクリックします。

![PowerShell ギャラリーのインポート モジュール](../media/modules/powershell-gallery.png)

Automation アカウントから直接、PowerShell ギャラリーからモジュールをインポートすることもできます。 Automation アカウントで、**[共有リソース]** の下で **[モジュール]** を選択します。 [モジュール] ページで、**[ギャラリーの参照]** をクリックします。 これにより、**[ギャラリーの参照]** ページが開きます。 このページを使用して、PowerShell ギャラリーでモジュールを検索できます。 インポートするモジュールを選択し、**[インポート]** をクリックします。 **[インポート]** ページで **[OK]** をクリックしてインポート プロセスを開始します。

![Azure portal からの PowerShell ギャラリーのインポート](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>内部コマンドレット

以下に、すべての Automation アカウントにインポートされる内部 `Orchestrator.AssetManagement.Cmdlets` モジュール内のコマンドレットの一覧を示します。 これらのコマンドレットは、Runbook および DSC 構成でアクセスでき、Automation アカウント内のアセットを操作できるようにします。 さらに、内部コマンドレットは、暗号化された **[変数]** 値、**[資格情報]**、および暗号化された **[接続]** フィールドからシークレットを取得できるようにします。 Azure PowerShell コマンドレットはこれらのシークレットを取得できません。 これらのコマンドレットでは、使用するときに、暗黙的に Azure に接続する必要はありません。 これは、Azure に対する認証に使用する必要がある実行アカウントなど、接続しているシナリオに役立ちます。

|Name|説明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>接続の種類をモジュールに追加する

省略可能なファイルをモジュールに追加することにより、Automation アカウントで使用するカスタムの[接続の種類](../automation-connections.md)を提供できます。 このファイルは、Automation アカウント内のモジュールのコマンドレットで使用する Azure Automation の接続の種類を指定するメタデータ ファイルです。 これを実現するには、まず、PowerShell モジュールを作成する方法を知る必要があります。 モジュールの作成の詳細については、[PowerShell スクリプト モジュールを記述する方法](/powershell/developer/module/how-to-write-a-powershell-script-module)に関するページを参照してください。

![Azure portal でカスタム接続を使用する](../media/modules/connection-create-new.png)

Azure Automation の接続の種類を追加するには、接続の種類のプロパティを指定する `<ModuleName>-Automation.json` という名前のファイルがモジュールに含まれている必要があります。 これは json ファイルで、圧縮された .zip ファイルのモジュール フォルダー内に配置されています。 このファイルには、モジュールが示すシステムまたはサービスに接続するために必要な接続のフィールドが含まれています。 この構成により、最終的に Azure Automation の接続の種類が作成されます。 このファイルを使用すると、モジュールの接続の種類について、フィールド名、種類、フィールドを暗号化するか省略可能にするかを指定できます。 次の例は、ユーザー名とパスワードのプロパティを定義する JSON ファイル形式のテンプレートです。

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

## <a name="module-best-practices"></a>モジュールのベスト プラクティス

PowerShell モジュールを Azure Automation にインポートして、そのコマンドレットを Runbook 内で使用できるようにすることや、その DSC リソースを DSC 構成内で使用できるようにすることができます。 Azure Automation は、バックグラウンドでこれらのモジュールを格納し、Runbook ジョブと DSC コンパイル ジョブの実行時に Azure Automation のサンドボックスに読み込みます。そこで、Runbook が実行され、DSC 構成がコンパイルされます。 また、モジュール内のすべての DSC リソースは、Automation DSC プル サーバーに自動的に配置されます。 DSC 構成を適用するとき、これらをマシンでプルすることができます。

Azure Automation で使用するための PowerShell モジュールを作成するときには、次の点を考慮することをお勧めします。

* モジュール内のすべてのコマンドレットの概要、説明、ヘルプ URI を含める。 PowerShell では、コマンドレットのヘルプ情報を定義できます。定義すると、ユーザーは **Get-Help** コマンドレットを使用して、そのコマンドレットの使用方法に関するヘルプ情報を確認できます。 次の例では、.psm1 モジュール ファイルで概要およびヘルプ URI を定義する方法を示します。

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  この情報を指定すると、PowerShell コンソールで **Get-Help** コマンドレットを使用したときにこのヘルプが表示されます。 この説明は、Azure portal でも表示されます。

  ![統合モジュールのヘルプ](../media/modules/module-activity-description.png)

* モジュールが外部サービスに接続する場合、[接続の種類](#add-a-connection-type-to-your-module)を含む必要があります。 モジュール内の各コマンドレットが接続オブジェクト (その接続の種類のインスタンス) をパラメーターとして受け取ることができるようにする。 これにより、ユーザーは、コマンドレットを呼び出すたびに、接続資産のパラメーターをコマンドレットの対応するパラメーターにマッピングできます。 上記の Runbook 例に基づいて、これは ContosoConnection と呼ばれる Contoso 接続資産例を使用して、Contoso リソースにアクセスし、外部サービスからのデータを返します。

  次の例では、フィールドが `PSCredential` オブジェクトの UserName および Password のプロパティにマップされ、続いてコマンドレットに渡されます。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  この動作のためのより簡単かつ優れた方法では、接続オブジェクトをコマンドレットに直接渡します。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  パラメーターの接続フィールドではなく、接続オブジェクトをパラメーターとして直接受け取ることができるようにすると、コマンドレットを前の例のように動作させることができます。 Azure Automation を使用していないユーザーが、接続オブジェクトとして機能するハッシュテーブルを作成することなく、コマンドレットを呼び出すことができるようにするには、通常、それぞれにパラメーター セットが必要です。 接続フィールドのプロパティを渡すために、パラメーター セット `UserAccount` が使用されます。 `ConnectionObject` を使用すると、接続をそのまま渡すことができます。

* モジュール内のすべてのコマンドレットの出力の種類を定義します。 コマンドレットの出力の種類を定義すると、設計時の IntelliSense で、作成時に使用するコマンドレットの出力のプロパティを確認できます。 これは、Automation Runbook のグラフィカル作成時に特に役に立ちます。グラフィカル作成時にユーザーがモジュールを簡単に利用できるようにするには、設計時の情報が重要です。

  これは、`[OutputType([<MyOutputType>])]` を追加することよって実現できます。ここで MyOutputType は有効な型です。 OutputType の詳細については、[関数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)に関するページを参照してください。 次のコードは、`OutputType` をコマンドレットに追加する例です。

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Graphical Runbook Output Type](../media/modules/runbook-graphical-module-output-type.png)

  この動作は、PowerShell ISE のコマンドレットの出力の "先行入力" 機能と似ていますが、実行する必要はありません。

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* モジュール内のすべてのコマンドレットをステートレスにする。 複数の Runbook ジョブは、同じ AppDomain および同じプロセスとサンドボックスで同時に実行できます。 これらのレベルで共有された状態がある場合、ジョブが互いに影響し合うことがあります。 この動作は、断続的で診断の難しい問題を招く可能性があります。  悪い例を次に示します。

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* xcopy できるパッケージにモジュールを完全に含める。 Azure Automation のモジュールは、Runbook を実行する必要があるときに Automation の Sandbox に配布されます。 これらのモジュールは、実行元のホストから独立して動作する必要があります。 モジュール パッケージを zip 形式で圧縮して移動できるようにし、別のホストの PowerShell 環境にインポートしたときに通常通りに機能する状態にする必要があります。 これを実現するために、モジュールがモジュール フォルダーの外部のファイルに依存しないようにする必要があります。 このフォルダーは、モジュールを Azure Automation にインポートするときに、zip 形式で圧縮されるフォルダーです。 また、モジュールが、ホストの固有のレジストリ設定 (製品のインストール時に設定された設定など) に依存しないようにする必要があります。 モジュール内のすべてのファイルは、パスを 140 文字以下にする必要があります。 パスが 140 文字を超えた場合、Runbook のインポートで問題が発生します。 このベスト プラクティスに従わないと、モジュールを Azure Automation で使用できません。  

* モジュールで [Azure Powershell Az モジュール](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)を参照する場合、`AzureRM` を参照していないことを確認してください。 `Az` モジュールは、`AzureRM` モジュールと併用できません。 `Az` は、Runbook でサポートされていますが、既定ではインポートされません。 `Az` モジュールおよび考慮事項について詳しくは、[Azure Automation での Az モジュールのサポート](../az-modules.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* PowerShell モジュールの作成の詳細については、「 [Writing a Windows PowerShell Module (Windows PowerShell モジュールの作成)](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)