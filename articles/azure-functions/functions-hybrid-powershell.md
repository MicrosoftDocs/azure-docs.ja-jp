---
title: PowerShell Azure Functions を使用してリモートのオンプレミス リソースを管理する
description: PowerShell 関数アプリをオンプレミス リソースに接続するように Azure Relay のハイブリッド接続を構成し、それを使用してリモートからオンプレミス リソースを管理する方法について説明します。
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775095"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Azure Functions の PowerShell と App Service ハイブリッド接続を使用してハイブリッド環境を管理する

Azure App Service ハイブリッド接続を使用すると、他のネットワーク内のリソースにアクセスすることができます。 この機能の詳細については、[ハイブリッド接続](../app-service/app-service-hybrid-connections.md)のドキュメントを参照してください。 以降の情報では、オンプレミス サーバーを対象に、この機能を使用して PowerShell 関数を実行する方法について説明します。 そのサーバーを使用すれば、オンプレミス環境内のすべてのリソースを Azure PowerShell 関数から管理することができます。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>オンプレミス サーバーを PowerShell リモート処理用に構成する

以下のスクリプトは、PowerShell リモート処理を有効にして、新しいファイアウォール規則と WinRM の HTTPS リスナーを作成します。 テスト目的のため、自己署名証明書を使用します。 運用環境では署名証明書を使用することをお勧めします。

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>ポータルで PowerShell 関数アプリを作成する

App Service ハイブリッド接続は、Basic、Standard、Isolated の価格プランでのみ利用できます。 PowerShell で関数アプリを作成する際は、このいずれかのプランを作成または選択してください。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. 左側にある **[+ リソースの作成]** を選択し、 **[関数アプリ]** を選択します。

1. **[ホスティング プラン]** で、 **[App Service プラン]** を選択し、 **[App Service プラン/場所]** を選択します。

1. **[新規作成]** を選択し、 **[App Service プラン]** に名前を入力し、[[リージョン]](https://azure.microsoft.com/regions/) で、自分の近くか使用する関数がアクセスする他のサービスの近くの**場所**を選択し、 **[価格レベル]** を選択します。

1. S1 Standard プランを選択し、 **[適用]** を選択します。

1. **[OK]** を選択してプランを作成し、残りの関数アプリの設定を、図の下の表に示すように指定します。 

    ![PowerShell Core 関数アプリ](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Setting      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **リソース グループ** |  myResourceGroup | Function App を作成するための新しいリソース グループの名前。 推奨値を使用することもできます。 |
    | **OS** | 推奨 OS | [Windows] を選択します。 |
    | **ランタイム スタック** | 優先言語 | [PowerShell Core] を選択します。 |
    | **Storage** |  グローバルに一意の名前 |  関数アプリで使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできます。
    | **Application Insights** | Default | 最も近いサポートされているリージョン内に同じ*アプリ名*の Application Insights リソースを作成します。 この設定を展開することによって、 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。 |

1. 設定が検証されたら、 **[作成]** を選択します。

1. ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

1. **[リソースに移動]** を選択して、新しい関数アプリを確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>関数アプリ用のハイブリッド接続を作成する

ハイブリッド接続の構成は、関数アプリの [ネットワーク] セクションから行います。

1. 関数アプリのプラットフォーム タブを選択し、[ネットワーク] を選択します。
![プラットフォーム ネットワークのアプリの概要](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. [Configure your hybrid connections endpoints]\(ハイブリッド接続のエンドポイントを構成する\) を選択します。
![ネットワーク](./media/functions-hybrid-powershell/select-network-feature.png)  
1. [ハイブリッド接続の追加] を選択します。
![ハイブリッド接続](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. ハイブリッド接続についての情報を以下のように入力します。 [エンドポイント ホスト] は、後でリモート コマンドを実行する際に思い出しやすいよう、オンプレミス サーバーのホスト名と同じでもかまいません。 ポートは、先ほどサーバーで定義した Windows リモート管理サービスの既定のポートと対応します。
![ハイブリッド接続の追加](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **[ハイブリッド接続の名前]** ContosoHybridOnPremisesServer
    
    **[エンドポイント ホスト]** finance1
    
    **[エンドポイント ポート]** 5986
    
    **[サービス バス名前空間]** 新たに作成します。
    
    **[場所]** 使用可能な場所を選択します。
    
    **[名前]** contosopowershellhybrid

5. [OK] をクリックしてハイブリッド接続を作成します。

## <a name="download-and-install-the-hybrid-connection"></a>ハイブリッド接続をダウンロードしてインストールする

1. [接続マネージャーのダウンロード] アイコンを選択して .msi ファイルをローカル コンピューターに保存します。
![インストーラーのダウンロード](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. ローカル コンピューターからオンプレミス サーバーに .msi をコピーします。
1. ハイブリッド接続のインストーラーを実行して、オンプレミス サーバーにサービスをインストールします。
![ハイブリッド接続のインストール](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. ポータルからハイブリッド接続を開き、ゲートウェイ接続文字列をクリップボードにコピーします。
![ハイブリッド接続文字列をコピーする](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. オンプレミス サーバー上のハイブリッド接続マネージャーの UI を開きます。
![ハイブリッド接続の UI を開く](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. [手動で入力] ボタンを選択して、クリップボードから接続文字列を貼り付けします。
![接続文字列を貼り付ける](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. ハイブリッド接続マネージャーが接続状態になっていない場合は、PowerShell からハイブリッド接続マネージャーを再起動します。
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>管理者アカウントのパスワードに使用するアプリ設定を作成する

1. 関数アプリケーションからプラットフォーム タブを選択します。
1. [全般設定] セクションの [構成] を選択します。![プラットフォームの構成を選択](./media/functions-hybrid-powershell/select-configuration.png)  
1. [新しいアプリケーション設定] を選択して、パスワード用の新しい設定を作成します。
1. この設定に ContosoUserPassword という名前を付けてパスワードを入力します。
1. [OK]、[保存] の順に選択して、関数アプリケーションにパスワードを保存します。![パスワードのアプリ設定を追加する](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>HTTP トリガー関数を作成してテストする

1. 関数アプリから新しい HTTP トリガー関数を作成します。![新しい HTTP トリガーを作成する](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. テンプレートの PowerShell コードを次のコードに置き換えます。

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. [保存]、[実行] の順にクリックして関数をテストします。![関数アプリをテストする](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>オンプレミスにある他のシステムを管理する

接続先のオンプレミス サーバーを使用して、ローカル環境内の他のサーバーや管理システムに接続することができます。 これにより、データ センターの運用を Azure から PowerShell 関数を使用して管理することができます。 以下に示したのは、指定された資格情報の下で動作する PowerShell 構成セッションを登録するスクリプトです。 これらの資格情報は、リモート サーバー上の管理者であることが必要です。 この構成を使用して、ローカル サーバーやデータ センター内の他のエンドポイントにアクセスすることができます。

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

上のスクリプトに使用されている各変数は、実際の環境の値に置き換えてください。
* $HybridEndpoint
* $RemoteServer

前出の 2 つのシナリオでは、Azure Functions の PowerShell とハイブリッド接続を使用してオンプレミス環境に接続し、その環境を管理することができます。 詳しい情報は、[ハイブリッド接続](../app-service/app-service-hybrid-connections.md)と [Functions の PowerShell](./functions-reference-powershell.md) についてのドキュメントでご覧いただけます。

Azure [仮想ネットワーク](./functions-create-vnet.md)と Azure Functions を使用してオンプレミス環境に接続することもできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [PowerShell 関数の使用についての詳細情報](functions-reference-powershell.md)
