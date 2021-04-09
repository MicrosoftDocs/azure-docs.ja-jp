---
title: パケット キャプチャを使用してアラートと Azure Functions によるプロアクティブなネットワーク監視を実行する - Azure Functions
titleSuffix: Azure Network Watcher
description: この記事では、Azure Network Watcher を使用して、アラートでトリガーされるパケット キャプチャを作成する方法について説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 8e2af73be0fc887b132f523133159472ce1d1f98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570981"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>パケット キャプチャを使用してアラートと Azure Functions によるプロアクティブなネットワーク監視を実行する

Network Watcher のパケット キャプチャを使うと、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 キャプチャ ファイルには、監視したいトラフィックのみを追跡するために定義したフィルターを設定できます。 このデータは、ストレージ BLOB に格納されるか、ローカルのゲスト マシンに格納されます。

この機能は、Azure Functions などの他の自動化シナリオからリモートで開始できます。 パケット キャプチャを使うと、定義したネットワークの異常に基づいて、プロアクティブなキャプチャを実行できます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。

Azure でデプロイされたリソースは、24 時間 365 日実行されています。 その間、お客様とそのスタッフがすべてのリソースの状態を常に監視し続けることはできません。 たとえば、午前 2 時に問題が発生したら、どのように対応すればよいでしょうか。

Azure エコシステム内部から Network Watcher、アラート、関数を使うことにより、データとツールでプロアクティブに対応してネットワークの問題を解決できます。

![図は、仮想マシンの Network Watcher 拡張機能を示しています。これは、T C P Segments sent > 100 へ、次に Azure Functions へ、さらに Network Watcher へとフローし、そこから Network Watcher 拡張機能に戻っています。][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Azure PowerShell](/powershell/azure/install-Az-ps)。
* Network Watcher の既存インスタンス。 まだない場合は、[Network Watcher のインスタンスを作成](network-watcher-create.md)します。
* [Windows 拡張機能](../virtual-machines/extensions/network-watcher-windows.md)または [Linux 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-linux.md)を備えた、Network Watcher と同じリージョン内の既存の仮想マシン。

## <a name="scenario"></a>シナリオ

この例では、VM が送信している TCP セグメントが普段よりも多い場合にアラートが届くようにします。 ここで使う TCP セグメントはあくまでも例であり、任意のアラート条件を使うことができます。

アラートでは、通信が増えた理由を理解するためにパケット レベルのデータを受け取る必要があります。 そうすれば、仮想マシンを通常の通信状態に戻すための手順を実行できます。

このシナリオでは、Network Watcher の既存のインスタンスと、有効な仮想マシンが含まれるリソース グループがあることを前提としています。

次の一覧は、実行されるワークフローの概要です。

1. アラートが VM でトリガーされる。
1. アラートが webhook 経由で Azure 関数を呼び出す。
1. Azure 関数がアラートを処理し、Network Watcher のパケット キャプチャ セッションを開始する。
1. パケット キャプチャが VM で実行され、トラフィックが収集される。
1. パケット キャプチャ ファイルが確認と診断のためにストレージ アカウントにアップロードされる。

このプロセスを自動化するには、インシデント発生時にトリガーされるアラートを VM に作成して接続します。 また、Network Watcher を呼び出す関数を作成します。

このシナリオでは、次の処理が実行されます。

* パケット キャプチャを開始する Azure 関数を作成する。
* 仮想マシンでアラート ルールを作成し、Azure 関数を呼び出すようにアラート ルールを構成する。

## <a name="create-an-azure-function"></a>Azure 関数の作成

最初の手順では、アラートを処理してパケット キャプチャを作成する Azure 関数を作成します。

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[コンピューティング]**  >  **[Function App]** の順に選択します。

    ![Function App を作成する][1-1]

2. **[Function App]** ブレードで、次の値を入力して **[OK]** を選び、アプリを作成します。

    |**設定** | **Value** | **詳細** |
    |---|---|---|
    |**アプリ名**|PacketCaptureExample|Function App の名前。|
    |**サブスクリプション**|[お使いのサブスクリプション] Function App を作成するサブスクリプション。||
    |**リソース グループ**|PacketCaptureRG|Function App を格納するリソース グループ。|
    |**ホスティング プラン**|従量課金プラン| Function App で使うプランの種類。 オプションは、[従量課金プラン] と [Azure App Service プラン] です。 |
    |**Location**|米国中部| Function App を作成するリージョン。|
    |**ストレージ アカウント**|{自動生成}| 一般的な記憶のために Azure Functions が必要とするストレージ アカウント。|

3. **PacketCaptureExample Function App** ブレードで、 **[関数]**  >  **[カスタム関数]**  > **[+]** の順に選びます。

4. **[HttpTrigger-Powershell]** を選び、残りの情報を入力します。 最後に、関数を作成するには、 **[作成]** を選びます。

    |**設定** | **Value** | **詳細** |
    |---|---|---|
    |**シナリオ**|Experimental|シナリオの種類|
    |**関数名の指定**|AlertPacketCapturePowerShell|関数の名前|
    |**承認レベル**|Function|関数の承認レベル|

![関数の例][functions1]

> [!NOTE]
> PowerShell テンプレートは試験段階であり、まだ完全にはサポートされていません。

この例で必要となるカスタマイズについては、次の手順で説明します。

### <a name="add-modules"></a>モジュールを追加する

Network Watcher PowerShell コマンドレットを使うには、最新の PowerShell モジュールを Function App にアップロードします。

1. 最新の Azure PowerShell モジュールがインストールされているローカル コンピューターで、次の PowerShell コマンドを実行します。

    ```powershell
    (Get-Module Az.Network).Path
    ```

    この例では、Azure PowerShell モジュールのローカル パスを取得しています。 これらのフォルダーは、後の手順で使用されます。 このシナリオで使われているモジュールは次のとおりです。

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell フォルダー][functions5]

1. **[Function App の設定]**  >  **[App Service エディターに移動]** の順に選びます。

    ![Function App の設定][functions2]

1. **AlertPacketCapturePowershell** フォルダーを右クリックし、**azuremodules** という名前のフォルダーを作成します。 

4. 必要なモジュールごとにサブフォルダーを作成します。

    ![フォルダーとサブフォルダー][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. **Az.Network** サブフォルダーを右クリックし、 **[ファイルのアップロード]** を選びます。 

6. Azure モジュールに移動します。 ローカルの **Az.Network** フォルダーで、フォルダー内のすべてのファイルを選びます。 **[OK]** をクリックします。 

7. **Az.Accounts** と **Az.Resources** にこれらの手順を繰り返します。

    ![ファイルをアップロードする][functions6]

1. 完了すると、各フォルダーにローカル コンピューターから PowerShell モジュールがアップロードされています。

    ![PowerShell ファイル][functions7]

### <a name="authentication"></a>認証

PowerShell コマンドレットを使用するには、認証する必要があります。 Function App で認証を構成します。 認証を構成するには、環境変数を構成して、暗号化されたキー ファイルを Function App にアップロードする必要があります。

> [!NOTE]
> このシナリオでは、Azure Functions を使った認証の実装方法を一例として取り上げます。 この他の実装方法もあります。

#### <a name="encrypted-credentials"></a>暗号化された資格情報

次の PowerShell スクリプトは、**PassEncryptKey.key** という名前のキー ファイルを作成します。 また、指定されているパスワードの暗号化バージョンも提供します。 このパスワードは、認証に使われる Azure Active Directory アプリケーション用に定義されたパスワードと同じものです。

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Function App の App Service エディターで、**AlertPacketCapturePowerShell** の下に **keys** という名前のフォルダーを作成します。 そして、前の PowerShell サンプルで作成した **PassEncryptKey.key** ファイルをアップロードします。

![関数キー][functions8]

### <a name="retrieve-values-for-environment-variables"></a>環境変数の値を取得する

最終的な要件は、認証用の値にアクセスするために必要な環境変数を設定することです。 次に示すのは、作成される環境変数の一覧です。

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

このクライアント ID は、Azure Active Directory 内のアプリケーションのアプリケーション ID です。

1. 使うアプリケーションがまだない場合は、次の例を実行してアプリケーションを作成します。

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > アプリケーション作成時に使うパスワードは、前にキー ファイルを保存するときに作成したものと同じパスワードにする必要があります。

1. Azure Portal で、 **[サブスクリプション]** を選びます。 使うサブスクリプションを選び、 **[アクセス制御 (IAM)]** を選びます。

    ![関数の IAM][functions9]

1. 使うアカウントを選び、 **[プロパティ]** を選びます。 アプリケーション ID をコピーします。

    ![関数のアプリケーション ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

次の PowerShell サンプルを実行して、テナント ID を取得します。

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword 環境変数の値は、次の PowerShell サンプルを実行して得られる値です。 この例は、前の「**暗号化された資格情報**」セクションで示したものと同じです。 必要な値は、`$Encryptedpassword` 変数の出力です。  これは、PowerShell スクリプトを使って暗号化したサービス プリンシパル パスワードです。

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>環境変数を格納する

1. Function App に移動します。 **[Function App の設定]**  >  **[アプリケーション設定の構成]** の順に選びます。

    ![アプリケーションの設定の構成][functions11]

1. 環境変数とその値をアプリの設定に追加し、 **[保存]** を選びます。

    ![アプリケーション設定][functions12]

### <a name="add-powershell-to-the-function"></a>関数への PowerShell の追加

次に、Azure 関数の内部から Network Watcher を呼び出します。 この関数の実装は、要件によって異なる場合があります。 ただし、コードの一般的なフローは次のようになります。

1. 入力パラメーターを処理します。
2. 既存のパケット キャプチャの有無を照会し、制限を確認して、名前の競合を解決します。
3. 適切なパラメーターを設定してパケット キャプチャを作成します。
4. 完了するまでパケット キャプチャを定期的にポーリングします。
5. パケット キャプチャ セッションの完了をユーザーに通知します。

関数で使うことができる PowerShell コードの例を次に示します。 **SubscriptionId**、**resourceGroupName**、**storageAccountName** の値は置き換える必要があります。

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
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
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>関数の URL を取得する 
1. 関数を作成した後は、その関数に関連付けられた URL を呼び出すようにアラートを構成します。 この値を取得するには、Function App から関数の URL をコピーします。

    ![関数の URL の検索][functions13]

2. Function App の関数の URL をコピーします。

    ![関数の URL のコピー][2]

webhook POST 要求のペイロードでカスタム プロパティが必要な場合は、「[Azure メトリック アラートでの webhook の構成](../azure-monitor/alerts/alerts-webhooks.md)」をご覧ください。

## <a name="configure-an-alert-on-a-vm"></a>VM のアラートの構成

アラートは、特定のメトリックが割り当て済みのしきい値を超えた場合に個々のユーザーに通知が届くように、構成することができます。 この例では、アラートは送信される TCP セグメントに関するものですが、他の多くのメトリックに対してアラートをトリガーできます。 この例では、webhook を呼び出して関数を呼び出すようにアラートを構成します。

### <a name="create-the-alert-rule"></a>アラート ルールの作成

既存の仮想マシンに移動してアラート ルールを追加します。 アラートの構成について詳しくは、「[Azure Monitorでの Azure サービス アラートの作成 - Azure Portal](../azure-monitor/alerts/alerts-classic-portal.md)」をご覧ください。 **[アラート ルール]** ブレードに次の値を入力して、 **[OK]** を選びます。

  |**設定** | **Value** | **詳細** |
  |---|---|---|
  |**Name**|TCP_Segments_Sent_Exceeded|アラート ルールの名前。|
  |**説明**|送信された TCP セグメント数がしきい値を超えました|アラート ルールの説明。|
  |**メトリック**|送信した TCP セグメント数| アラートのトリガーに使うメトリック。 |
  |**Condition**|より大きい| メトリックを評価するときに使う条件。|
  |**しきい値**|100| アラートをトリガーするメトリックの値。 この値は、環境で有効な値に設定する必要があります。|
  |**期間**|過去 5 分間| メトリックでしきい値を調べる期間を決定します。|
  |**webhook**|[Function App の webhook URL]| 前の手順で作成した Function App の webhook URL。|

> [!NOTE]
> TCP セグメントのメトリックは、既定では無効です。 追加のメトリックを有効にする方法について詳しくは、「[監視と診断の有効化](../azure-monitor/overview.md)」をご覧ください。

## <a name="review-the-results"></a>結果の確認

アラートの条件がトリガーされた後、パケット キャプチャが作成されます。 Network Watcher に移動し、 **[パケット キャプチャ]** を選びます。 このページでは、パケット キャプチャ ファイルのリンクを選んでパケット キャプチャをダウンロードできます。

![パケット キャプチャを表示する][functions14]

キャプチャ ファイルがローカルに格納されている場合は、仮想マシンにサインインすることで取得できます。

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure Blob Storage を使用する](../storage/blobs/storage-quickstart-blobs-dotnet.md)」をご覧ください。 他のツールとしては、[ストレージ エクスプローラー](https://storageexplorer.com/)を使うことができます。

ダウンロードしたキャプチャは、 **.cap** ファイルを読み取ることができる任意のツールを使って表示できます。 次に示すのはそのような 2 つのツールへのリンクです。

- [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>次のステップ

パケット キャプチャを表示する方法については、[Wireshark によるパケット キャプチャ分析](network-watcher-deep-packet-inspection.md)に関するページをご覧ください。


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
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
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png