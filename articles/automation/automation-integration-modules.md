--- 
title: "Azure Automation 統合モジュールの作成 | Microsoft Docs"
description: "Azure Automation の統合モジュールの作成、テスト、使用例をわかりやすく説明しています。Azure Automation の統合モジュールの作成、テスト、使用例をわかりやすく説明しています。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 27798efb-08b9-45d9-9b41-5ad91a3df41e
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: ea2ad80f348c80856c525218aad45a8fcf47bcb8
ms.openlocfilehash: aeb06276a52e5472667ae0a741fb3007a91910fe
ms.lasthandoff: 01/14/2017

---

# <a name="azure-automation-integration-modules"></a>Azure Automation 統合モジュール
PowerShell は、Azure Automation の基盤となるテクノロジです。 Azure Automation は PowerShell を基盤として構築されているため、PowerShell モジュールが Azure Automation の拡張性の鍵となります。 この記事では、"統合モジュール" と呼ばれる Azure Automation での PowerShell モジュールの使用の詳細と、独自の PowerShell モジュールを作成して、Azure Automation 内で統合モジュールとして確実に動作させるためのベスト プラクティスを紹介します。 

## <a name="what-is-a-powershell-module"></a>PowerShell モジュールとは
PowerShell モジュールは、**Get-Date** や **Copy-Item** などの PowerShell コマンドレットの集まりで、PowerShell コンソール、スクリプト、ワークフロー、Runbook、PowerShell DSC リソース (PowerShell DSC 構成から使用できる WindowsFeature や File など) から使用できます。 PowerShell のすべての機能は、コマンドレットと DSC リソースによって公開されています。すべてのコマンドレットと DSC リソースは PowerShell モジュールでサポートされており、その多くが PowerShell 自体に付属しています。 たとえば、**Get-Date** コマンドレットは Microsoft.PowerShell.Utility PowerShell モジュールに、**Copy-Item** コマンドレットは Microsoft.PowerShell.Management PowerShell モジュールに、Package DSC リソースは PSDesiredStateConfiguration PowerShell モジュールに含まれています。 これらのモジュールはどれも PowerShell に付属しています。 ただし、多くの PowerShell モジュールは PowerShell の一部として出荷されません。代わりに、System Center 2012 Configuration Manager などの Microsoft やサードパーティの製品と一緒に配布されたり、PowerShell ギャラリーなどの大規模な PowerShell コミュニティから配布されたりします。  モジュールは、カプセル化された機能によって複雑なタスクを簡素化するため、便利です。  PowerShell モジュールの詳細については、[MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx) を参照してください。 

## <a name="what-is-an-azure-automation-integration-module"></a>Azure Automation 統合モジュールとは
統合モジュールは、PowerShell モジュールとあまり変わりません。 必要に応じて 1 つのファイル (Runbook 内のモジュールのコマンドレットで使用する Azure Automation の接続の種類を指定するメタデータ ファイル) を追加できる単なる PowerShell モジュールです。 そのファイルの有無に関係なく、これらの PowerShell モジュールを Azure Automation にインポートして、そのコマンドレットを Runbook 内で使用できるようにすることや、その DSC リソースを DSC 構成内で使用できるようにすることができます。 Azure Automation は、バックグラウンドでこれらのモジュールを格納し、Runbook ジョブと DSC コンパイル ジョブの実行時に Azure Automation のサンドボックスに読み込みます。そこで、Runbook が実行され、DSC 構成がコンパイルされます。  また、モジュール内のすべての DSC リソースは、Automation DSC プル サーバーに自動的に配置されます。そのため、DSC 構成の適用を試みるコンピューターがそれらのリソースをプルできます。  

Azure Automation には、Azure の管理の自動化をすぐに開始できるように追加設定なしで使用できる Azure PowerShell モジュールが数多く用意されていますが、統合するシステム、サービス、ツールがどのようなものでもその PowerShell モジュールをインポートすることができます。 

> [!NOTE]
> Automation サービスには、特定のモジュールが "グローバル モジュール" として付属しています。 これらのグローバル モジュールは、Automation アカウントを作成すると使用できます。ときどき更新され、自動的に Automation アカウントにプッシュされます。 自動的に更新されないようにするには、常に自分で同じモジュールをインポートしてください。そのモジュールの方が、サービスに付属するモジュールのグローバル モジュール バージョンよりも優先されます。 

インポートする統合モジュール パッケージは、モジュールと同じ名前の圧縮ファイルで、.zip 拡張子が付きます。 パッケージには、Windows PowerShell モジュールと、マニフェスト ファイル (.psd1) などのサポート ファイル (モジュールに存在する場合) が含まれています。

モジュールに Azure Automation の接続の種類が含まれている場合は、接続の種類のプロパティを指定する `<ModuleName>-Automation.json` という名前のファイルも必ず含まれています。 この json ファイルは、圧縮ファイル (.zip) のモジュール フォルダー内に配置されており、モジュールが表すシステムやサービスに接続するために必要な "接続" のフィールドが含まれています。 これにより、最終的に Azure Automation の接続の種類が作成されます。 このファイルを使用すると、モジュールの接続の種類について、フィールド名、種類、フィールドを暗号化するか省略可能にするかを指定できます。 json ファイル形式のテンプレートを次に示します。

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
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
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Service Management Automation をデプロイし、Automation Runbook の統合モジュール パッケージを作成したことがある場合は、このようなコードは非常に見覚えがあると思います。 

## <a name="authoring-best-practices"></a>作成のベスト プラクティス
統合モジュールは実質的に PowerShell モジュールですが、PowerShell モジュールを Azure Automation での使用に最も適したものにするためには、作成時にお勧めしているさまざまな考慮事項があります。 考慮事項には、Azure Automation に固有のものもあれば、Automation を使用するかどうかに関係なく、モジュールを PowerShell ワークフローで正常に動作させるために役立つものもあります。 

1. モジュール内のすべてのコマンドレットの概要、説明、ヘルプ URI を含める。 PowerShell では、コマンドレットのヘルプ情報を定義できます。定義すると、ユーザーは **Get-Help** コマンドレットを使用して、そのコマンドレットの使用方法に関するヘルプ情報を確認できます。 例として、.psm1 ファイルに記述された PowerShell モジュールの概要とヘルプ URI を定義する方法を次に示します。<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> この情報を指定すると、PowerShell コンソールで **Get-Help** コマンドレットを使用したときにこのヘルプが表示されるだけでなく、Azure Automation 内でもこのヘルプ機能が公開されます。  たとえば、Runbook の作成中にアクティビティを挿入するときです。 [詳細なヘルプの表示] をクリックすると、Azure Automation へのアクセスに使用している Web ブラウザーの別のタブでヘルプ URI が開きます。<br>![Integration Module Help](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. リモート システムに対してモジュールが実行される場合は、

    a. そのリモート システムに接続するために必要な情報 (つまり、接続の種類) を定義する統合モジュール メタデータ ファイルをモジュールに含める。  
    b. モジュール内の各コマンドレットが接続オブジェクト (その接続の種類のインスタンス) をパラメーターとして受け取ることができるようにする。  

    接続の種類のフィールドを含むオブジェクトをパラメーターとしてコマンドレットに渡すことができるようにすると、モジュール内のコマンドレットが Azure Automation で使いやすくなります。 この方法では、ユーザーがコマンドレットを呼び出すときに毎回接続資産のパラメーターをコマンドレットの対応するパラメーターにマッピングする必要がありません。 上記の Runbook の例では、CorpTwilio という Twilio 接続資産を使用して、Twilio にアクセスし、アカウントのすべての電話番号を返します。  接続のフィールドをコマンドレットのパラメーターにマッピングする方法を次に示します。<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    このためのより簡単かつ優れた方法では、接続オブジェクトをコマンドレットに直接渡します。
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    パラメーターの接続フィールドではなく、接続オブジェクトをパラメーターとして直接受け取ることができるようにすると、コマンドレットをこのように動作させることができます。 Azure Automation を使用していないユーザーが、接続オブジェクトとして機能するハッシュテーブルを作成することなく、コマンドレットを呼び出すことができるようにするには、通常、それぞれにパラメーター セットが必要です。 接続フィールドのプロパティを 1 つずつ渡すには、下のパラメーター セット **SpecifyConnectionFields** を使用します。 **UseConnectionObject** を使用すると、接続をそのまま渡すことができます。 ご覧のように、 [Twilio PowerShell モジュール](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) の Send-TwilioSMS コマンドレットでは、どちらの方法でも渡すことができます。 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. モジュール内のすべてのコマンドレットの出力の種類を定義する。 コマンドレットの出力の種類を定義すると、設計時の IntelliSense で、作成時に使用するコマンドレットの出力のプロパティを確認できます。 これは、Automation Runbook のグラフィカル作成時に特に役に立ちます。グラフィカル作成時にユーザーがモジュールを簡単に利用できるようにするには、設計時の情報が重要です。<br><br> ![グラフィカルな Runbook 出力の種類](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> これは、PowerShell ISE のコマンドレットの出力の "先行入力" 機能と似ていますが、実行する必要はありません。<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. モジュール内のコマンドレットでは、複合オブジェクト型をパラメーターとして受け取らない。 PowerShell ワークフローは、複合型が逆シリアル化された形式で格納される点が PowerShell とは異なります。 プリミティブ型はプリミティブのままですが、複合型は逆シリアル化されたバージョン (実質的にはプロパティ バッグ) に変換されます。 たとえば、Runbook (または、PowerShell ワークフロー) で **Get-Process** コマンドレットを使用した場合は、予期される [System.Diagnostic.Process] 型ではなく、[Deserialized.System.Diagnostic.Process] 型のオブジェクトが返されます。 この型には、逆シリアル化されていない型と同じプロパティがすべて揃っていますが、メソッドは 1 つもありません。 さらに、この値をパラメーターとしてコマンドレットに渡そうとすると、コマンドレットではこのパラメーターに対して [System.Diagnostic.Process] 値を予期しているため、次のエラーが表示されます。*パラメーター 'process' の引数変換を処理できません。エラー: "System.Diagnostics.Process (CcmExec)" の値を "Deserialized.System.Diagnostics.Process" 型から "System.Diagnostics.Process" 型に変換できません。*   これは、予期される [System.Diagnostic.Process] 型と渡された [Deserialized.System.Diagnostic.Process] 型で型が一致していないことが原因です。 この問題を回避する方法では、モジュールのコマンドレットでパラメーターに複合型を受け取らないようにします。 不適切な方法を次に示します。
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    適切な方法を次に示します。複合オブジェクトを取得するためにコマンドレットで内部的に使用できるプリミティブを受け取って、それを使用しています。 コマンドレットは、PowerShell ワークフローではなく、PowerShell のコンテキストで実行されるため、コマンドレット内では $process は適切な [System.Diagnostic.Process] 型になります。  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Runbook の接続資産はハッシュテーブルです。これは複合型ですが、これらのハッシュテーブルはキャスト例外なしでコマンドレットの -Connection パラメーターに問題なく渡すことができるようです。 技術的には、PowerShell の一部の型は、シリアル化された形式から逆シリアル化された形式に適切にキャストできます。そのため、逆シリアル化されていない型をコマンドレットにパラメーターとして渡しても受け取ることができます。 ハッシュテーブルは、そうした型の 1 つです。 モジュールの作成者が定義した型を、適切に逆シリアル化できる方法で実装することもできますが、考慮すべきトレードオフがいくつかあります。 型は、既定のコンストラクターと PSTypeConverter を持ち、すべてのプロパティがパブリックである必要があります。 ただし、モジュールの作成者が所有していない既に定義されている型は "修正" する方法がないため、すべてのパラメーターで複合型を回避することをお勧めしています。 Runbook の作成のヒント: 何らかの理由でコマンドレットが複合型のパラメーターを受け取る必要がある場合や、複合型のパラメーターが必要な他のユーザーのモジュールを使用する場合、PowerShell ワークフロー Runbook およびローカル PowerShell の PowerShell ワークフローでは、対処方法として、複合型を生成するコマンドレットと複合型を使用するコマンドレットを同じ InlineScript アクティビティでラップします。 InlineScript では、PowerShell ワークフローではなく、PowerShell としてコンテンツが実行されるため、複合型を生成するコマンドレットでは、逆シリアル化された複合型ではなく、適切な型が生成されます。
5. モジュール内のすべてのコマンドレットをステートレスにする。 PowerShell ワークフローでは、ワークフローの異なるセッションで呼び出されたすべてのコマンドレットが実行されます。 そのため、同じモジュール内の他のコマンドレットによって作成または変更されたセッションの状態に依存するコマンドレットはすべて PowerShell ワークフロー Runbook では動作しません。  悪い例を次に示します。
   
    ```
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
   <br>
6. Xcopy できるパッケージにモジュールを完全に含める。 Azure Automation のモジュールは、Runbook を実行する必要があるときに Automation の Sandbox に配布されます。そのため、実行されているホストから独立して動作する必要があります。 つまり、モジュール パッケージを zip 形式で圧縮し、PowerShell のバージョンが同じかそれ以降の他のホストに移動でき、そのホストの PowerShell 環境にインポートされたときに通常どおり機能する必要があります。 そのためには、モジュールがモジュール フォルダー (Azure Automation にインポートするときに zip 形式で圧縮されるフォルダー) 以外のファイルや、ホストに固有のレジストリ設定 (製品のインストールによって設定される設定など) に依存しないようにする必要があります。 このベスト プラクティスに従わないと、モジュールを Azure Automation で使用できません。  

## <a name="next-steps"></a>次のステップ

* PowerShell Workflow Runbook を初めて利用するときは、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* PowerShell モジュールの作成の詳細については、「 [Writing a Windows PowerShell Module (Windows PowerShell モジュールの作成)](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)


