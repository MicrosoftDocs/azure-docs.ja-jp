---
title: Windows Virtual Desktop の MSIX アプリのアタッチ - Azure
description: Windows Virtual Desktop の MSIX アプリのアタッチを設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: helohr
ms.openlocfilehash: fde3ddf052e47e7550d15aba4ff26d32c91e34b9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972390"
---
# <a name="set-up-msix-app-attach"></a>MSIX アプリのアタッチを設定する

> [!IMPORTANT]
> 現在、MSIX アプリのアタッチはパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このトピックでは、Windows Virtual Desktop 環境で MSIX アプリのアタッチを設定する方法について説明します。

## <a name="requirements"></a>必要条件

開始する前に、MSIX アプリのアタッチを構成するために必要な項目を示します。

- Windows Insider ポータルにアクセスして、MSIX アプリのアタッチ API をサポートする Windows 10 のバージョンを取得する。
- 機能する Windows Virtual Desktop のデプロイ。 詳細については、「[Windows Virtual Desktop でテナントを作成する](tenant-setup-azure-active-directory.md)」を参照してください。
- MSIX パッケージ作成ツール
- MSIX パッケージが保存される Windows Virtual Desktop のデプロイのネットワーク共有

## <a name="get-the-os-image"></a>OS ディスク名を取得する

まず、MSIX アプリに使用する OS イメージを取得する必要があります。 OS ディスク名を取得するには、次の手順に従います。

1. [Windows Insider ポータル](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0)を開き、サインインします。

     >[!NOTE]
     >Windows Insider ポータルにアクセスするには、Windows Insider Program のメンバーである必要があります。 Windows Insider Program の詳細については、[Windows Insider のドキュメント](https://docs.microsoft.com/windows-insider/at-home/)を参照してください。

2. **[Select edition]\(エディションの選択\)** セクションまで下にスクロールし、 **[Windows 10 Insider Preview Enterprise (FAST) – ビルド XXXXX]** を選択します。

3. **[確認]** を選択し、使用する言語を選択してから、 **[確認]** をもう一度選択します。
    
     >[!NOTE]
     >現時点では、この機能でテストされた言語は英語のみです。 他の言語は、選択はできますが、意図したとおりに表示されない場合があります。
    
4. ダウンロード リンクが生成されたら、 **[64 ビットのダウンロード]** を選択し、ローカル ハード ディスクに保存します。

## <a name="prepare-the-vhd-image-for-azure"></a>Azure の VHD イメージを準備する 

手順を開始する前に、マスター VHD イメージを作成する必要があります。 マスター VHD イメージをまだ作成していない場合は、「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md)」に進んで、記載されている手順に従ってください。 

マスター VHD イメージを作成した後は、MSIX アプリのアタッチ アプリケーションの自動更新を無効にする必要があります。 自動更新を無効にするには、管理者特権でのコマンド プロンプトで次のコマンドを実行する必要があります。

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

次に、Azure 用の VM VHD を準備し、結果の VHD ディスクを Azure にアップロードします。 詳細については、「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md)」を参照してください。

VHD を Azure にアップロードしたら、チュートリアル「[Azure Marketplace を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」の手順に従って、この新しいイメージに基づくホスト プールを作成します。

## <a name="prepare-the-application-for-msix-app-attach"></a>MSIX アプリのアタッチ用のアプリケーションを準備する 

既に MSIX パッケージがある場合は、「[Windows Virtual Desktop インフラストラクチャの構成](#configure-windows-virtual-desktop-infrastructure)」に進んでください。 レガシ アプリケーションをテストする場合は、「[VM 上でデスクトップ インストーラーからの MSIX パッケージを作成する](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm)」の手順に従って、レガシ アプリケーションを MSIX パッケージに変換します。

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>MSIX 用の VHD または VHDX パッケージを生成する

パッケージは、パフォーマンスを最適化するために VHD または VHDX 形式です。 MSIX が正常に機能するには、VHD または VHDX パッケージが必要です。

MSIX 用の VHD または VHDX パッケージを生成するには、次の手順に従います。

1. [msixmgr ツール](https://aka.ms/msixmgr)をダウンロードし、.zip フォルダーをセッション ホスト VM 内のフォルダーに保存します。

2. msixmgr ツールの .zip フォルダーを解凍します。

3. msixmgr ツールを解凍したフォルダーに、ソース MSIX パッケージを配置します。

4. PowerShell で次のコマンドレットを実行して、VHD を作成します。

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >VHD のサイズが展開された MSIX.* を保持するのに十分な大きさであることを確認します。

5. 次のコマンドレットを実行して、新しく作成した VHD をマウントします。

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 次のコマンドレットを実行して、VHD を初期化します。

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 次のコマンドレットを実行して、新しいパーティションを作成します。

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 次のコマンドレットを実行して、パーティションをフォーマットします。

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. マウントされた VHD に親フォルダーを作成します。 MSIX アプリのアタッチには親フォルダーが必要であるため、この手順は必須です。 親フォルダーには好きな名前を付けることができます。

### <a name="expand-msix"></a>MSIX を展開する

その後、MSIX イメージをアンパックして "展開" する必要があります。 MSIX イメージをアンパックするには、次のようにします。

1. 管理者としてコマンドプロンプトを開き、msixmgr ツールをダウンロードして解凍したフォルダーに移動します。

2. 次のコマンドレットを実行して、前のセクションで作成およびマウントした VHD に MSIX をアンパックします。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    アンパックが完了すると、次のメッセージが表示されます。

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > ネットワーク内、またはインターネットに接続されていないデバイス上のビジネス向け (または教育機関向け) Microsoft Store のパッケージを使用する場合、アプリを正常に実行するには、Store からパッケージ ライセンスを取得してインストールする必要があります。 「[パッケージをオフラインで使用する](#use-packages-offline)」を参照してください。

3. マウントされている VHD に移動し、アプリ フォルダーを開いて、パッケージ コンテンツが存在することを確認します。

4. VHD のマウントを解除します。

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Windows Virtual Desktop インフラストラクチャの構成

仕様により、VHD は読み取り専用モードでアタッチされているため、1 つの MSIX 展開パッケージ (前のセクションで作成した VHD) を複数のセッション ホスト VM 間で共有できます。

開始する前に、ネットワーク共有が次の要件を満たしていることを確認してください。

- 共有は SMB と互換性があります。
- セッション ホスト プールの一部である VM には、共有に対する NTFS アクセス許可があります。

### <a name="set-up-an-msix-app-attach-share"></a>MSIX アプリのアタッチの共有を設定する 

Windows Virtual Desktop 環境で、ネットワーク共有を作成し、パッケージをそこに移動します。

>[!NOTE]
> MSIX ネットワーク共有を作成する場合のベスト プラクティスは、NTFS 読み取り専用アクセス許可を使用してネットワーク共有を設定することです。

## <a name="install-certificates"></a>証明書をインストールする

アプリが、公的に信頼されていない証明書、または自己署名された証明書を使用している場合は、次のようにインストールします。

1. パッケージを右クリックし、 **[プロパティ]** を選択します。
2. 表示されるウィンドウで、 **[デジタル署名]** タブを選択します。次の図に示すように、タブの一覧には項目が 1 つだけ表示されます。 その項目を選択して項目を強調表示し、[**De] を選択します
3. デジタル信号の詳細ウィンドウが表示されたら、 **[全般]** タブを選択し、 **[証明書のインストール]** を選択します。
4. インストーラーが開いたら、保存場所として **[ローカル コンピューター]** を選択し、 **[次へ]** を選択します。
5. アプリがデバイスに変更を加えることを許可するかどうかを確認するメッセージが表示されたら、 **[はい]** を選択します。
6. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** を選択します。
7. 証明書ストアの選択ウィンドウで、 **[信頼されたユーザー]** を選択して、 **[OK]** をクリックします。
8. **[完了]** を選択します。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX アプリのアタッチ用の PowerShell スクリプトを準備する

MSIX アプリのアタッチには、次の順序で実行する必要がある 4 つの異なるフェーズがあります。

1. 段階
2. Register
3. 登録解除
4. ステージング解除

各フェーズでは、PowerShell スクリプトを作成します。 各フェーズのサンプル スクリプトについては、[こちら](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)を参照してください。

### <a name="stage-the-powershell-script"></a>PowerShell スクリプトをステージングする

PowerShell スクリプトを更新する前に、VHD にボリュームのボリューム GUID があることを確認してください。 ボリューム GUID を取得するには、次の手順に従います。

1.  スクリプトを実行する VM 内に VHD が配置されているネットワーク共有を開きます。

2.  VHD を右クリックし、 **[マウント]** を選択します。 これにより、VHD がドライブ文字にマウントされます。

3.  VHD をマウントすると、**エクスプローラー** ウィンドウが開きます。 親フォルダーをキャプチャし、 **\$parentFolder** 変数を更新します

    >[!NOTE]
    >親フォルダーが表示されない場合は、MSIX が適切に展開されていないことを意味します。 前のセクションを再実行して、もう一度やり直してください。

4.  親フォルダーを開きます。 正しく展開されている場合は、パッケージと同じ名前のフォルダーが表示されます。 このフォルダーの名前と一致するように **\$packageName** 変数を更新します。

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


6.  今コピーしたボリューム GUID を使用して、 **\$volumeGuid** 変数を更新します。

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

    Mount-Diskimage -ImagePath \$vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + \$vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + \$vhdSrc + " has failed!") -BackgroundColor Red

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

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task\`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress\`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

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

このスクリプトでは、 **\$packageName** のプレースホルダーを、テスト対象のパッケージの名前に置き換えます。

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

このスクリプトでは、 **\$packageName** のプレースホルダーを、テスト対象のパッケージの名前に置き換えます。

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX アプリのアタッチ エージェントのシミュレーション スクリプトを設定する

作成したスクリプトは、手動で実行するか、起動、ログオン、ログオフ、およびシャットダウン スクリプトとして自動的に実行するように設定することができます。 これらの種類のスクリプトの詳細については、[グループ ポリシーでの起動、シャットダウン、ログオン、およびログオフのスクリプトの使用](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11))に関するページを参照してください。

以下の各自動スクリプトはそれぞれ、アプリのアタッチ スクリプトの 1 つのフェーズを実行します。

- 起動スクリプトによって、ステージ スクリプトが実行されます。
- ログオン スクリプトによって、登録スクリプトが実行されます。
- ログオフ スクリプトによって、登録解除スクリプトが実行されます。
- シャットダウン スクリプトによって、ステージング解除スクリプトが実行されます。

## <a name="use-packages-offline"></a>パッケージをオフラインで使用する

ネットワーク内、またはインターネットに接続されていないデバイス上の[ビジネス向け Microsoft Store](https://businessstore.microsoft.com/) または [教育機関向け Microsoft Store](https://educationstore.microsoft.com/) のパッケージを使用する場合、アプリを正常に実行するには、Microsoft Store からパッケージ ライセンスを取得して、デバイス上にインストールする必要があります。 デバイスがオンラインであり、ビジネス向け Microsoft Store に接続できる場合は、必要なライセンスが自動的にダウンロードされますが、オフラインになっている場合はライセンスを手動で設定する必要があります。 

ライセンス ファイルをインストールするには、WMI ブリッジプロバイダーで MDM_EnterpriseModernAppManagement_StoreLicenses02_01 クラスを呼び出す PowerShell スクリプトを使用する必要があります。  

オフラインで使用するためにライセンスを設定するには、次の手順を実行します。 

1. アプリ パッケージ、ライセンス、および必要なフレームワークをビジネス向け Microsoft Store からダウンロードします。 エンコードされたライセンス ファイルとエンコードされていないライセンス ファイルの両方が必要です。 ダウンロードの詳細な手順については、[こちら](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)を確認してください。
2. 手順 3 のスクリプトで次の変数を更新します。
      1. `$contentID` は、エンコードされていないライセンス ファイル (.xml) の ContentID 値です。 ライセンス ファイルは任意のテキスト エディターで開くことができます。
      2. `$licenseBlob` は、エンコードされたライセンス ファイル (.bin) のライセンス BLOB の文字列全体です。 エンコードされたライセンス ファイルは、任意のテキスト エディターで開くことができます。 
3. 管理者 PowerShell プロンプトから次のスクリプトを実行します。 ライセンスのインストールを実行するのに適した場所は、[ステージング スクリプト](#stage-the-powershell-script)の最後です。これも管理者プロンプトから実行する必要があります。

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

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

## <a name="next-steps"></a>次の手順

この機能は現在サポートされていませんが、[Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) でコミュニティに質問することができます。

[Windows Virtual Desktop フィードバック ハブ](https://aka.ms/MRSFeedbackHub)で Windows Virtual Desktop に関するフィードバックを残すことも、[MSIX アプリのアタッチのフィードバック ハブ](https://aka.ms/msixappattachfeedback)および [MSIX パッケージ作成ツールのフィードバック ハブ](https://aka.ms/msixtoolfeedback)で MSIX アプリとパッケージ作成ツールに関するフィードバックを残すこともできます。
