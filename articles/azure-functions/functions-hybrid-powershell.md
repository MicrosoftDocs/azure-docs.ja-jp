---
title: PowerShell 関数を使用してリモートのオンプレミス リソースを管理する
description: PowerShell 関数アプリをオンプレミス リソースに接続するように Azure Relay のハイブリッド接続を構成し、それを使用してリモートからオンプレミス リソースを管理する方法について説明します。
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122274"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Azure Functions の PowerShell と App Service ハイブリッド接続を使用してハイブリッド環境を管理する

Azure App Service ハイブリッド接続機能を使用すると、他のネットワーク内のリソースにアクセスすることができます。 この機能の詳細については、[ハイブリッド接続](../app-service/app-service-hybrid-connections.md)のドキュメントを参照してください。 この記事では、オンプレミス サーバーを対象に、この機能を使用して PowerShell 関数を実行する方法について説明します。 そのサーバーを使用すれば、オンプレミス環境内のすべてのリソースを Azure PowerShell 関数から管理することができます。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>オンプレミス サーバーを PowerShell リモート処理用に構成する

以下のスクリプトは、PowerShell リモート処理を有効にして、新しいファイアウォール規則と WinRM の HTTPS リスナーを作成します。 テスト目的のため、自己署名証明書を使用します。 運用環境では、署名証明書を使用することをお勧めします。

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>ポータルで PowerShell 関数アプリを作成する

App Service ハイブリッド接続機能は、Basic、Standard、Isolated の価格プランでのみ利用できます。 PowerShell で関数アプリを作成する際は、このいずれかのプランを作成または選択してください。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[計算]** 、 >  **[関数アプリ]** の順に選択します。

1. **[基本]** ページで、下の表で指定されている関数アプリの設定を使用します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Function App を作成するための新しいリソース グループの名前。 |
    | **関数アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。  |
    |**発行**| コード | コード ファイルまたは Docker コンテナーの発行オプション。 |
    | **ランタイム スタック** | 優先言語 | [PowerShell Core] を選択します。 |
    |**Version**| バージョン番号 | インストールされているランタイムのバージョンを選択します。  |
    |**リージョン**| 優先リージョン | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="関数アプリ - 基本を作成します。" border="true":::

1. **[次へ :ホスティング]** を選択します。 **[ホスティング]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[ストレージ アカウント](../storage/common/storage-account-create.md)** |  グローバルに一意の名前 |  Function App で使用されるストレージ アカウントを作成します。 ストレージ アカウント名は、3 文字から 24 文字までの長さにし、数字と小文字のみを使用する必要があります。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](../azure-functions/functions-scale.md#storage-account-requirements)を満たしている必要があります。 |
    |**オペレーティング システム**| 優先オペレーティング システム | オペレーティング システムは、ランタイム スタックの選択に基づいてあらかじめ選択されますが、必要に応じて設定を変更できます。 |
    | **[プランの種類](../azure-functions/functions-scale.md)** | **App Service プラン** | **App Service プラン**を選択します。 App Service プランで実行する場合は、[関数アプリのスケーリング](../azure-functions/functions-scale.md)を管理する必要があります。  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="関数アプリ プロジェクト - ホスティングを作成します。" border="true":::

1. **[次へ :監視]** を選択します。 **[監視]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Default | 最も近いサポートされているリージョン内に同じ*アプリ名*の Application Insights リソースを作成します。 この設定を展開するか、 **[新規作成]** を選択することによって、Application Insights 名を変更するか、データを格納する [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)内の別のリージョンを選択することができます。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="関数アプリ プロジェクト - 監視を作成します。" border="true":::

1. **[確認および作成]** を選択して、アプリ構成の選択内容を確認します。

1. **[確認および作成]** ページで設定を確認して、 **[作成]** を選択し、関数アプリをプロビジョニングしてデプロイします。

1. ポータルの右上隅の **[通知]** アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

1. **[リソースに移動]** を選択して、新しい Function App を確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>関数アプリ用のハイブリッド接続を作成する

ハイブリッド接続の構成は、関数アプリの [ネットワーク] セクションから行います。

1. 先ほど作成した関数アプリの **[設定]** で、 **[ネットワーク]** を選択します。 
1. **[Configure your hybrid connections endpoints]\(ハイブリッド接続のエンドポイントを構成する\)** を選択します。
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="ハイブリッド接続エンドポイントを構成します。" border="true":::

1. **[ハイブリッド接続の追加]** を選択します。
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="ハイブリッド接続を追加します。" border="true":::

1. ハイブリッド接続に関する情報を入力します。次のスクリーンショットのすぐ後に記載した情報を参照してください。 **[エンドポイント ホスト]** の設定は、後でリモート コマンドを実行する際に思い出しやすいよう、オンプレミス サーバーのホスト名と同じでもかまいません。 ポートは、先ほどサーバーで定義した Windows リモート管理サービスの既定のポートと対応します。
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="[ハイブリッド接続の追加]。" border="true":::

    | 設定      | 推奨値  |
    | ------------ | ---------------- |
    | **ハイブリッド接続の名前** | ContosoHybridOnPremisesServer |
    | **エンドポイント ホスト** | finance1 |
    | **エンドポイント ポート** | 5986 |
    | **Servicebus 名前空間** | Create New |
    | **場所** | 使用可能な場所を選択 |
    | **名前** | contosopowershellhybrid | 

1. **[OK]** を選択してハイブリッド接続を作成します。

## <a name="download-and-install-the-hybrid-connection"></a>ハイブリッド接続をダウンロードしてインストールする

1. **[接続マネージャーのダウンロード]** を選択して、 *.msi* ファイルをローカル コンピューターに保存します。

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="インストーラーをダウンロードします。" border="true":::

1. ローカル コンピューターからオンプレミス サーバーに *.msi* ファイルをコピーします。
1. ハイブリッド接続マネージャーのインストーラーを実行して、オンプレミス サーバーにサービスをインストールします。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="ハイブリッド接続をインストールします。" border="true":::

1. ポータルからハイブリッド接続を開き、ゲートウェイ接続文字列をクリップボードにコピーします。

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="ハイブリッド接続文字列をコピーします。" border="true":::

1. オンプレミス サーバー上のハイブリッド接続マネージャーの UI を開きます。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="ハイブリッド接続 UI を開きます。" border="true":::

1. **[手動で入力]** を選択して、クリップボードから接続文字列を貼り付けます。

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="ハイブリッド接続を貼り付けます。" border="true":::

1. ハイブリッド接続マネージャーが接続状態になっていない場合は、PowerShell からハイブリッド接続マネージャーを再起動します。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>管理者アカウントのパスワードに使用するアプリ設定を作成する

1. 関数アプリの **[設定]** で、 **[構成]** を選択します。 
1. **[+ 新しいアプリケーション設定]** を選択します。

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="管理者アカウントのパスワードを構成します。" border="true":::

1. この設定に **ContosoUserPassword** という名前を付けてパスワードを入力します。 **[OK]** を選択します。
1. **[保存]** を選択して、パスワードを関数アプリケーションに保存します。

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="管理者アカウントのパスワードを保存します。" border="true":::

## <a name="create-a-function-http-trigger"></a>HTTP トリガー関数を作成する

1. 関数アプリで、 **[関数]** を選択し、 **[+ 追加]** を選択します。

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="新しい HTTP トリガーを作成します。" border="true":::

1. **HTTP トリガー** テンプレートを選択します。

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="HTTP トリガー テンプレートを選択します。" border="true":::

1. 新しい関数に名前を付けて、 **[関数の作成]** を選択します。

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="名前を指定して、新しい HTTP トリガー関数を作成します。" border="true":::

## <a name="test-the-function"></a>関数をテストする

1. 新しい関数で、 **[Code + Test]\(コード + テスト\)** を選択します。 テンプレートの PowerShell コードを次のコードに置き換えます。

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
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

1. **[保存]** を選択します。

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="PowerShell コードを変更して、HTTP トリガー関数を保存します。" border="true":::

 1. **[テスト]** を選択し、 **[実行]** を選択して、関数をテストします。 ログを確認して、テストが成功したことを確認します。

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="HTTP トリガー関数をテストします。" border="true":::

## <a name="managing-other-systems-on-premises"></a>オンプレミスにある他のシステムを管理する

接続先のオンプレミス サーバーを使用して、ローカル環境内の他のサーバーや管理システムに接続することができます。 これにより、データ センターの運用を Azure から PowerShell 関数を使用して管理することができます。 以下に示したのは、指定された資格情報の下で動作する PowerShell 構成セッションを登録するスクリプトです。 これらは、リモート サーバー上の管理者の資格情報であることが必要です。 この構成を使用して、ローカル サーバーやデータ センター内の他のエンドポイントにアクセスすることができます。

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

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

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
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

このスクリプトに使用されている次の変数は、実際の環境の適切な値に置き換えてください。
* $HybridEndpoint
* $RemoteServer

前出の 2 つのシナリオでは、Azure Functions の PowerShell とハイブリッド接続を使用してオンプレミス環境に接続し、その環境を管理することができます。 [関数における PowerShell](./functions-reference-powershell.md) と[ハイブリッド接続](../app-service/app-service-hybrid-connections.md)についての理解を深めるようお勧めします。

Azure [仮想ネットワーク](./functions-create-vnet.md)と Azure Functions を使用してオンプレミス環境に接続することもできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [PowerShell 関数の使用についての詳細情報](functions-reference-powershell.md)
