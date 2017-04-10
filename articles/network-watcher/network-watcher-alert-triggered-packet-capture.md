---
title: "パケット キャプチャを使用して Azure Functions によるプロアクティブなネットワーク監視を実行する | Microsoft Docs"
description: "この記事では、Azure Network Watcher を使用して、アラートでトリガーされるパケット キャプチャを作成する方法について説明します"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>パケット キャプチャを使用して Azure Functions によるプロアクティブなネットワーク監視を実行する

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 キャプチャ ファイルには、監視したいトラフィックのみを追跡するために定義したフィルターを設定できます。 このデータは、ストレージ BLOB に格納されるか、ローカルのゲスト マシンに格納されます。 この機能は、Azure Functions と同様に他の自動化のシナリオからリモートで開始できます。 パケット キャプチャを使用すると、定義したネットワークの異常に基づいて、プロアクティブなキャプチャを実行できます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報の取得などがあり、クライアントとサーバーの間で行われる通信のデバッグなどに役立ちます。

Azure でデプロイされたリソースは、24 時間 365 日実行されています。 その間、お客様とそのスタッフがすべてのリソースの状態を常に監視し続けることはできません。 もし午前 2 時に問題が発生したら、どのように対応すればよいでしょうか。

Azure エコシステム内部から Network Watcher、アラート、関数を使用すれば、問題を解決するためのデータとツールを使ってネットワークに存在する問題にプロアクティブに対応できます。

## <a name="before-you-begin"></a>開始する前に

この例では、VM が送信している TCP セグメントが普段よりも多い場合にアラートが届くようにします。 TCP セグメントが例として使用されていますが、任意のアラート条件を使用することができます。 アラートを受け取ったら、マシンを通常の通信状態に戻すための措置を取れるように、パケット レベルのデータを取得して通信量が増加した理由を把握する必要があります。
このシナリオでは、Network Watcher の既存のインスタンスがあり、有効な仮想マシンが含まれたリソース グループが使用されることを前提としています。

## <a name="scenario"></a>シナリオ

このプロセスを自動化するには、問題が発生した際にトリガーされる VM のアラートと、Network Watcher を呼び出す Azure 関数を作成して接続します。

このシナリオは次のとおりです。

* パケット キャプチャを開始する Azure 関数を作成する。
* 仮想マシンのアラート ルールを作成する。
* Azure 関数を呼び出すアラート ルールを構成する。

## <a name="creating-an-azure-function-and-overview"></a>Azure 関数の作成と概要

最初の手順では、アラートを処理してパケット キャプチャを作成する Azure 関数を作成します。 

次の一覧は、実行されるワークフローの概要です。

1. アラートが VM でトリガーされる。
1. アラートが webhook 経由で Azure 関数を呼び出す。
1. Azure 関数がアラートを処理し、Network Watcher のパケット キャプチャ セッションを開始する。
1. パケット キャプチャが VM で実行され、トラフィックが収集される。 
1. キャプチャ ファイルが確認と診断のためにストレージ アカウントにアップロードされる。 

Azure 関数の作成は、「[初めての Azure 関数の作成](../azure-functions/functions-create-first-azure-function.md)」の手順に従ってポータルで実行できます。 この例では、HttpTrigger-PowerShell 型の関数を使用しました。 この例で必要となるカスタマイズについては、次の手順で説明します。

![関数の例][functions1]

> [!NOTE]
> PowerShell テンプレートは試験段階であり、まだ完全にはサポートされていません。

## <a name="adding-modules"></a>モジュールの追加

Network Watcher PowerShell コマンドレットを使用するには、最新の PowerShell モジュールを Function App にアップロードする必要があります。

1. 最新の Azure PowerShell モジュールがインストールされているローカル コンピューターで、次の PowerShell コマンドを実行します。

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    これにより、Azure PowerShell モジュールのローカル パスを取得します。 これらのフォルダーは、後の手順で使用されます。 このシナリオで使用されているモジュールは次のとおりです。

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![PowerShell フォルダー][functions5]

1. **[Function App の設定]** > **[App Service エディターに移動]** に移動します。

    ![関数と Kudu][functions2]

1. AlertPacketCapturePowershell フォルダーを右クリックし、**azuremodules** という名前のフォルダーを作成します。 続いて、必要な各モジュールのサブ フォルダーを作成します。

    ![関数と Kudu][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. **AzureRM.Network** サブ フォルダーを右クリックし、**[ファイルのアップロード]** をクリックします。 Azure モジュールがインストールされている場所に移動し、AzureRM.Network フォルダーにあるすべてのファイルを選択して **[OK]** をクリックします。  AzureRM.Profile と AzureRM.Resources についても、同様の手順を繰り返します。

    ![[ファイルのアップロード]][functions6]

1. 完了すると、ローカル コンピューターからアップロードされた PowerShell モジュールが各フォルダーに表示されているはずです。

    ![PowerShell ファイル][functions7]

## <a name="authentication"></a>認証

PowerShell コマンドレットを使用するには、認証する必要があります。 認証は、Function App で構成されている必要があります。 Function App で構成するには、環境変数が構成されており、暗号化されたキー ファイルが Function App にアップロードされている必要があります。

> [!note]
> このシナリオでは、Azure Functions を使用した認証の実装方法を一例として取り上げます。この他の実装方法もあります。

### <a name="encrypted-credentials"></a>暗号化された資格情報

次の PowerShell スクリプトでは、**PassEncryptKey.key** という名前のキー ファイルが作成され、暗号化されたパスワードを取得します。  このパスワードは、認証に使用される Azure AD アプリケーション用に定義されたパスワードと同じものです。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Function App の App Service Editor で、**AlertPacketCapturePowerShell** 下に **keys** という名前のフォルダーを作成し、上記の PowerShell サンプルで作成した **PassEncryptKey.key** ファイルをアップロードします。

![関数とキー][functions8]

### <a name="retrieving-values-for-environment-variables"></a>環境変数の値の取得

最終的な構成では、認証用の値にアクセスするために必要となる環境変数を設定する必要があります。 作成される環境変数の一覧を次に示します。

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

このクライアント ID は、Azure Active Directory 内のアプリケーションのアプリケーション ID です。

1. 使用するアプリケーションがない場合は、アプリケーションを作成する次の例を実行します。

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > アプリケーション作成時に使用するパスワードは、キー ファイルを保存する前に作成したパスワードと同じものである必要があります。

1. Azure Portal で、**[サブスクリプション]** > 使用するサブスクリプションを選択 > **[アクセス制御 (IAM)]** に移動します。

    ![関数と IAM][functions9]

1. 使用するアカウントを選択し、[プロパティ] をクリックします。 アプリケーション ID をコピーします。

    ![関数とアプリケーション ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

テナント ID は、次の PowerShell サンプルを実行すると取得できます。

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword 環境変数の値は、次の PowerShell サンプルを実行して得られる値です。 これは、上記の**暗号化された資格情報**セクションで説明した例と同じものです。 必要な値は、`$Encryptedpassword` 変数の出力です。  これは、PowerShell スクリプトを使用して暗号化した、サービス プリンシパル パスワードです。

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>環境変数の格納

1. Function App に移動し、**[Function App の設定]** > **[アプリケーション設定の構成]** をクリックします。

    ![アプリケーション設定の構成][functions11]

1. 環境変数とその値をアプリケーション設定に追加し、**[保存]** をクリックします。

    ![アプリケーション設定][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>アラートの処理とパケット キャプチャ セッションの開始

次に、Azure 関数の内部から Network Watcher を呼び出します。 この関数の実装は要件によって異なりますが、 コードのフローは一般的に次のようになります。

1. 入力パラメーターを処理する
2. 既存のパケット キャプチャの有無を照会し、制限を確認して、名前の競合を解決する
3. 適切なパラメーターを設定してパケット キャプチャを作成する
4. 完了するまでパケット キャプチャを定期的にポーリングする
5. パケット キャプチャ セッションの完了をユーザーに通知する

次の例は、Azure 関数で使用できる PowerShell です。 SubscriptionId、resourceGroupName、storageAccountName の値は置き換える必要があります。

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

関数を作成したら、その関数に関連付けられた URL を呼び出すようにアラートを構成する必要があります。 この値を取得するには、**[</> 関数の URL の取得]** をクリックします。 

![関数の URL の検索 1][functions13]

Function App の関数の URL をコピーします。

![関数の URL の検索 2][2]

webhook POST 要求のペイロードでカスタム プロパティが必要な場合は、「[Azure メトリック アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)」を参照してください。

## <a name="configure-an-alert-on-a-vm"></a>VM のアラートの構成

アラートは、特定のメトリックが割り当て済みのしきい値を超えた場合に個々のユーザーに通知が届くように、構成することができます。 この例のアラートは、送信された TCP セグメントに基づいていますが、多数の他のメトリックを使用してトリガーできます。 この例では、webhook を呼び出して関数を呼び出すようにアラートを構成します。

### <a name="create-the-alert-rule"></a>アラート ルールの作成

既存の仮想マシンに移動してアラート ルールを追加します。 アラートの構成に関する詳細なドキュメントは、[Azure Portal を使用した Azure サービス用のアラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)に関するページにあります。 

![仮想マシンへの VM アラート ルールの追加][1]

> [!NOTE]
> 既定では、一部のメトリックは有効になっていません。 追加のメトリックを有効にする方法の詳細については、「[監視と診断の有効化](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)」を参照してください。

最後に、前の手順で取得した URL をアラートの [webhook] ボックスに貼り付けます。 **[OK]** をクリックして、アラート ルールを保存します。

![アラート ルールへの URL の貼り付け][3]

## <a name="downloading-and-viewing-the-capture-file"></a>キャプチャ ファイルのダウンロードと表示

キャプチャをストレージ アカウントに保存したら、ポータルまたはプログラムを使用してキャプチャ ファイルをダウンロードできます。 キャプチャ ファイルがローカルに格納されている場合は、仮想マシンにログインして取得します。 

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure BLOB ストレージを使用する](../storage/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他に Storage Explorer があります。 ストレージ エクスプローラーの詳細については、[ストレージ エクスプローラー](http://storageexplorer.com/)に関するページを参照してください。

ダウンロードしたキャプチャは、**.cap** ファイルを読み取れる任意のツールを使って表示できます。 そのようなツールを 2 つ、次のリンクで紹介します。

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[Wireshark](https://www.wireshark.org/)  

## <a name="next-steps"></a>次のステップ

パケット キャプチャを表示する方法については、[Wireshark によるパケット キャプチャ分析](network-watcher-alert-triggered-packet-capture.md)に関するページを参照してください。

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
