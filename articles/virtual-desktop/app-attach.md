---
title: Windows Virtual Desktop の MSIX アプリのアタッチ PowerShell スクリプトを構成する - Azure
description: Windows Virtual Desktop の MSIX アプリのアタッチ用の PowerShell スクリプトを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185769"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>MSIX アプリのアタッチ用の PowerShell スクリプトを作成する (プレビュー)

> [!IMPORTANT]
> 現在、MSIX アプリのアタッチはパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このトピックでは、MSIX アプリのアタッチ用の PowerShell スクリプトを設定する方法について説明します。

>[!IMPORTANT]
>作業を開始する前に、必ず[こちらのフォーム](https://aka.ms/enablemsixappattach)に記入して送信し、サブスクリプションで MSIX アプリのアタッチを有効にしてください。 承認されたリクエストがない場合、MSIX アプリ アタッチは機能しません。 リクエストの承認には、営業日に最大で 24 時間かかる可能性があります。 要求が受諾されて完了すると、電子メールが届きます。

## <a name="install-certificates"></a>証明書をインストールする

MSIX アプリのアタッチ パッケージからのアプリをホストするホスト プールのすべてのセッション ホストに証明書をインストールする必要があります。

アプリが、公的に信頼されていない証明書、または自己署名された証明書を使用している場合は、次のようにインストールします。

1. パッケージを右クリックし、 **[プロパティ]** を選択します。
2. 表示されるウィンドウで、 **[デジタル署名]** タブを選択します。次の図に示すように、タブの一覧には項目が 1 つだけ表示されます。 その項目を選択して強調表示した後、 **[削除]** を選択します。
3. デジタル署名の詳細ウィンドウが表示されたら、 **[全般]** タブを選択し、 **[証明書の表示]** を選択して、 **[証明書のインストール]** を選択します。
4. インストーラーが開いたら、保存場所として **[ローカル コンピューター]** を選択し、 **[次へ]** を選択します。
5. アプリがデバイスに変更を加えることを許可するかどうかを確認するメッセージが表示されたら、 **[はい]** を選択します。
6. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** を選択します。
7. 証明書ストアの選択ウィンドウで、 **[信頼されたユーザー]** を選択して、 **[OK]** をクリックします。
8. **[次へ]** 、 **[完了]** の順に選択します。

## <a name="enable-microsoft-hyper-v"></a>Microsoft Hyper-V を有効にする

ステージングするには `Mount-VHD` コマンドが必要であり、ステージングを解除するには `Dismount-VHD` が必要であるため、Microsoft Hyper-V を有効にする必要があります。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>この変更には、仮想マシンの再起動が必要です。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX アプリのアタッチ用の PowerShell スクリプトを準備する

MSIX アプリのアタッチには、次の順序で実行する必要がある 4 つの異なるフェーズがあります。

1. 段階
2. [登録]
3. 登録解除
4. ステージング解除

各フェーズでは、PowerShell スクリプトを作成します。 各フェーズのサンプル スクリプトについては、[こちら](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)を参照してください。

### <a name="stage-powershell-script"></a>PowerShell スクリプトのステージング

PowerShell スクリプトを更新する前に、VHD にボリュームのボリューム GUID があることを確認してください。 ボリューム GUID を取得するには、次の手順に従います。

1.  スクリプトを実行する VM 内に VHD が配置されているネットワーク共有を開きます。

2.  VHD を右クリックし、 **[マウント]** を選択します。 これにより、VHD がドライブ文字にマウントされます。

3.  VHD をマウントすると、**エクスプローラー** ウィンドウが開きます。 親フォルダーをキャプチャし、**parentFolder** 変数を更新します

    >[!NOTE]
    >親フォルダーが表示されない場合は、MSIX が適切に展開されていないことを意味します。 前のセクションを再実行して、もう一度やり直してください。

4.  親フォルダーを開きます。 正しく展開されている場合は、パッケージと同じ名前のフォルダーが表示されます。 このフォルダーの名前と一致するように **$packageName** 変数を更新します。

    たとえば、「 `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 」のように入力します。

5.  コマンド プロンプトを開き、**mountvol** と入力します。 このコマンドにより、ボリュームとその GUID の一覧が表示されます。 ドライブ文字が手順 2 で VHD をマウントしたドライブと一致するボリュームの GUID をコピーします。

    たとえば、mountvol コマンドのこの出力例では、VHD をドライブ C にマウントした場合、`C:\` の上の値をコピーする必要があります。

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  **$volumeGuid** 変数を、今コピーしたボリューム GUID に更新します。

7. 管理者の PowerShell プロンプトを開き、環境に適用される変数を使用して、次の PowerShell スクリプトを更新します。

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell スクリプトの登録

登録スクリプトを実行するには、プレースホルダーの値を環境に適用される値に置き換えて、次の PowerShell コマンドレットを実行します。

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell スクリプトの登録解除

このスクリプトでは、 **$packageName** のプレースホルダーを、テスト対象のパッケージの名前に置き換えます。

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell スクリプトのステージング解除

このスクリプトでは、 **$packageName** のプレースホルダーを、テスト対象のパッケージの名前に置き換えます。 運用デプロイでは、シャットダウン時にこれを実行することが推奨されます。

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX アプリのアタッチ エージェントのシミュレーション スクリプトを設定する

作成したスクリプトは、手動で実行するか、起動、ログオン、ログオフ、およびシャットダウン スクリプトとして自動的に実行するように設定することができます。 これらの種類のスクリプトの詳細については、[グループ ポリシーでの起動、シャットダウン、ログオン、およびログオフのスクリプトの使用](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)に関するページを参照してください。

以下の各自動スクリプトはそれぞれ、アプリのアタッチ スクリプトの 1 つのフェーズを実行します。

- 起動スクリプトによって、ステージ スクリプトが実行されます。
- ログオン スクリプトによって、登録スクリプトが実行されます。
- ログオフ スクリプトによって、登録解除スクリプトが実行されます。
- シャットダウン スクリプトによって、ステージング解除スクリプトが実行されます。

## <a name="use-packages-offline"></a>パッケージをオフラインで使用する

ネットワーク内、またはインターネットに接続されていないデバイス上の[ビジネス向け Microsoft Store](https://businessstore.microsoft.com/) または [教育機関向け Microsoft Store](https://educationstore.microsoft.com/) のパッケージを使用する場合、アプリを正常に実行するには、Microsoft Store からパッケージ ライセンスを取得して、デバイス上にインストールする必要があります。 デバイスがオンラインであり、ビジネス向け Microsoft Store に接続できる場合は、必要なライセンスが自動的にダウンロードされますが、オフラインになっている場合はライセンスを手動で設定する必要があります。

ライセンス ファイルをインストールするには、WMI ブリッジプロバイダーで MDM_EnterpriseModernAppManagement_StoreLicenses02_01 クラスを呼び出す PowerShell スクリプトを使用する必要があります。

オフラインで使用するためにライセンスを設定するには、次の手順を実行します。

1. アプリ パッケージ、ライセンス、および必要なフレームワークをビジネス向け Microsoft Store からダウンロードします。 エンコードされたライセンス ファイルとエンコードされていないライセンス ファイルの両方が必要です。 ダウンロードの詳細な手順については、[こちら](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)を確認してください。
2. 手順 3 のスクリプトで次の変数を更新します。
      1. `$contentID` は、エンコードされていないライセンス ファイル (.xml) の ContentID 値です。 ライセンス ファイルは任意のテキスト エディターで開くことができます。
      2. `$licenseBlob` は、エンコードされたライセンス ファイル (.bin) のライセンス BLOB の文字列全体です。 エンコードされたライセンス ファイルは、任意のテキスト エディターで開くことができます。
3. 管理者 PowerShell プロンプトから次のスクリプトを実行します。 ライセンスのインストールを実行するのに適した場所は、[ステージング スクリプト](#stage-powershell-script)の最後です。これも管理者プロンプトから実行する必要があります。

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>次のステップ

この機能は現在サポートされていませんが、[Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) でコミュニティに質問することができます。

また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。
