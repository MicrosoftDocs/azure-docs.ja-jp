---
title: Windows テンプレート マシンの設定に関するガイド | Microsoft Docs
description: Lab Services で Windows テンプレート マシンを準備するための一般的な手順。  これらの手順には、Windows Update スケジュールの設定、OneDrive のインストール、Office のインストールが含まれます。
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647904"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Azure Lab Services での Windows テンプレート マシンの設定に関するガイド

Azure Lab Services 用に Windows 10 テンプレート マシンを設定する場合の考慮すべきベスト プラクティスとヒントをいくつか以下に示します。 以下の構成手順はすべて省略可能です。  しかし、これらの準備手順は、学生が生産性を高め、授業時間の中断を最小限に抑え、学生が確実に最新のテクノロジを使用するようにするのに役立つ場合があります。

>[!IMPORTANT]
>この記事には、マシン テンプレートの変更プロセスを効率化するための PowerShell スニペットが含まれています。  表示されているすべての PowerShell スクリプトについては、Windows PowerShell で管理者特権を使用して実行する必要があります。 Windows 10 でこれを行う簡単な方法は、[スタート] メニューを右クリックし、[Windows PowerShell (管理者)] を選択することです。

## <a name="install-and-configure-onedrive"></a>OneDrive をインストールして構成する

仮想マシンがリセットされた場合に学生のデータが失われるのを防ぐために、学生には各自のデータをクラウドにバックアップすることをお勧めします。  Microsoft OneDrive は、学生が各自のデータを保護するのに役立ちます。  

### <a name="install-onedrive"></a>OneDrive をインストールする

OneDrive を手動でダウンロードしてインストールする場合は、[OneDrive](https://onedrive.live.com/about/download/) または [OneDrive for Business](https://onedrive.live.com/about/business/) のダウンロード ページを参照してください。

次の PowerShell スクリプトを使用することもできます。  これにより、最新バージョンの OneDrive が自動的にダウンロードされ、インストールされます。  OneDrive クライアントがインストールされたら、インストーラーを実行します。  この例では、`/allUsers` スイッチを使用して、マシン上のすべてのユーザー用に OneDrive をインストールします。 また、`/silent` スイッチを使用して、OneDrive をサイレント インストールします。

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive のカスタマイズ

[OneDrive に対して実行できるカスタマイズ](/onedrive/use-group-policy)は多数あります。 より一般的なカスタマイズについて、いくつか説明します。

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>サイレント モードで Windows の既知のフォルダーを OneDrive に移動する

[ドキュメント]、[ダウンロード]、[ピクチャ] などのフォルダーは、学生のファイルを格納するためによく使用されます。 これらのフォルダーが確実に OneDrive にバックアップされるようにするために、これらのフォルダーを OneDrive に移動することをお勧めします。

Active Directory を使用していないマシン上では、ユーザーは、OneDrive に対して認証されると、それらのフォルダーを OneDrive に手動で移動できます。

1. エクスプローラーを開く
2. [ドキュメント]、[ダウンロード]、または [ピクチャ] フォルダーを右クリックします。
3. [プロパティ] > [場所] の順に移動します。  フォルダーを OneDrive ディレクトリの新しいフォルダーに移動します。

仮想マシンが Active Directory に接続されている場合は、既知のフォルダーを OneDrive に移動することを学生に自動的に求めるように、テンプレート マシンを設定できます。  

まず、組織 ID を取得する必要があります。  詳しい手順については、[「Microsoft 365 組織 ID を検索する」](/onedrive/find-your-office-365-tenant-id)を参照してください。  次の PowerShell を使用して、組織 ID を取得することもできます。

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

組織 ID を取得したら、次の PowerShell を使用して、既知のフォルダーを OneDrive に移動することを求めるように OneDrive を設定します。

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>OneDrive ファイルをオンデマンドで使用する

学生のそれぞれの OneDrive アカウント内には、多くのファイルが含まれている場合があります。 マシン上の領域を節約し、ダウンロード時間を短縮するのに役立つように、学生の OneDrive アカウントに格納されているすべてのファイルをオンデマンドにすることをお勧めします。  オンデマンド ファイルは、ユーザーがファイルにアクセスした場合にのみダウンロードされます。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>OneDrive にユーザーをサイレント モードでサインインする

OneDrive は、ログオンしているユーザーの Windows 資格情報で自動的にサインインするように設定できます。  自動サインインは、学生が学校の資格情報でサインインするクラスに便利です。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>OneDrive セットアップの終了時に表示されるチュートリアルを無効にする

この設定を使用すれば、OneDrive セットアップの終了時に Web ブラウザーでチュートリアルを起動できないようにすることができます。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>自動的にダウンロードされるファイルの最大サイズを設定する

この設定は、[OneDrive ファイル オンデマンド] が有効になっていないデバイスで、[Windows 資格情報を使用して OneDrive 同期クライアントにユーザーをサイレント モードでサインインする] と組み合わせて使用されます。 指定されたしきい値 (MB 単位) を超えている OneDrive を持つすべてのユーザーは、OneDrive 同期クライアント (OneDrive.exe) でファイルがダウンロードされる前に同期する必要があるフォルダーを選択するように求められます。  この例では、"1111-2222-3333-4444" は組織 ID で、0005000 では 5 GB のしきい値が設定されます。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Microsoft 365 をインストールして構成する

### <a name="install-microsoft-365"></a>Microsoft 365 をインストールする

お使いのテンプレート マシンで Office が必要な場合は、[Office 展開ツール (ODT)](https://www.microsoft.com/download/details.aspx?id=49117) を使用して、Office をインストールすることをお勧めします。 [Microsoft 365 アプリ管理センター](https://config.office.com/)を使用して、再利用可能な構成ファイルを作成し、アーキテクチャ、必要となる Office の機能、その更新頻度を選択する必要があります。

1. [Microsoft 365 アプリ管理センター](https://config.office.com/)にアクセスし、独自の構成ファイルをダウンロードします。
2. [Office 展開ツール](https://www.microsoft.com/download/details.aspx?id=49117)をダウンロードします。  ダウンロードされるファイルは `setup.exe` です。
3. `setup.exe /download configuration.xml` を実行して、Office コンポーネントをダウンロードします。
4. `setup.exe /configure configuration.xml` を実行して、Office コンポーネントをインストールします。

### <a name="change-the-microsoft-365-update-channel"></a>Microsoft 365 の更新プログラム チャネルを変更する

Office 構成ツールを使用すれば、Office で更新プログラムを受信する頻度を設定することができます。 ただし、インストール後に Office で更新プログラムを受信する頻度を変更する必要がある場合は、更新プログラム チャネルの URL を変更することができます。 更新プログラム チャネル URL アドレスは「[組織内のデバイスの Microsoft 365 アプリ更新チャネルを変更する](/deployoffice/change-update-channels)」で見つけることができます。 以下の例は、月次更新チャネルを使用するように Microsoft 365 を設定する方法を示しています。

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>更新プログラムをインストールして構成する

### <a name="install-the-latest-windows-updates"></a>最新の Windows 更新プログラムをインストールする

テンプレート VM を発行する前に、セキュリティ上の理由から、テンプレート マシンに最新の Microsoft 更新プログラムをインストールすることをお勧めします。  そうすれば、更新プログラムが予期しないときに実行された場合に、学生の作業の中断を防げる可能性もあります。

1. [スタート] メニューから **[設定]** を起動します
2. **[更新** とセキュリティ] をクリックします
3. **[更新プログラムのチェック]** をクリックします
4. 更新プログラムがダウンロードされ、インストールされます。

PowerShell を使用して、テンプレート マシンを更新することもできます。

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>更新プログラムによっては、マシンの再起動が必要になる場合があります。  再起動が必要かどうかを確認するメッセージが表示されます。

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Microsoft Store アプリの最新の更新プログラムをインストールする

すべての Microsoft Store アプリを最新バージョンに更新することをお勧めします。  ここでは、Microsoft Store からアプリケーションを手動で更新する手順を示します。  

1. **Microsoft Store** アプリケーションを起動します。
2. アプリケーションの上の隅にある、ユーザーの写真の横の省略記号 (...) をクリックします。
3. ドロップダウン メニューから、 **[ダウンロード** と更新] を選択します。
4. **[更新プログラムの取得]** ボタンをクリックします。

また、PowerShell を使用して、既にインストールされている Microsoft Store アプリケーションを更新することもできます。

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Windows の自動更新を停止する

Windows を最新バージョンに更新した後で、Windows 更新プログラムを停止することを検討できます。  自動更新によって、スケジュールされた授業時間が妨げられる可能性があります。  コースが長期に行われるものである場合は、学生に更新プログラムを手動で確認することを求めるか、スケジュールされた授業時間外の時刻に自動更新を設定することを検討してください。  Windows Update のカスタマイズ オプションの詳細については、「[その他の Windows Update 設定の管理](/windows/deployment/update/waas-wu-settings)」を参照してください。

Windows の自動更新は、次の PowerShell スクリプトを使用して停止される場合があります。

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>外部言語パックをインストールする

仮想マシン上に追加の言語をインストールする必要がある場合は、Microsoft ストアを通じて追加することができます。

1. Microsoft Store を起動します
2. "言語パック" を検索します
3. インストールする言語を選択します

既にテンプレート VM にログオンしている場合は、[言語パックのインストール] ショートカット (`ms-settings:regionlanguage?activationSource=SMC-IA-4027670`) を使用して、適切な設定ページに直接移動します。

## <a name="remove-unneeded-built-in-apps"></a>不要な組み込みアプリを削除する

Windows 10 には、特定のクラスに必要ではない可能性がある、多くの組み込みアプリケーションが付属しています。 学生用のマシン イメージを簡単にするために、テンプレート マシンから一部のアプリケーションをアンインストールすることができます。  インストールされているアプリケーションのリストを表示するには、PowerShell の `Get-AppxPackage` コマンドレットを使用します。  以下の例では、削除できるすべてのインストール済みアプリケーションが示されています。

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

アプリケーションを削除するには、Remove-Appx コマンドレットを使用します。  以下の例では、XBox 関連のすべてを削除する方法が示されています。

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>一般的な教育関連のアプリケーションをインストールする

Windows ストア アプリでの教育によく使用される、その他のアプリをインストールします。 候補として、[Microsoft Whiteboard アプリ](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)、[Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)、[Minecraft Education Edition](https://education.minecraft.net/) などのアプリケーションがあります。 これらのアプリケーションは手動で Windows ストアを通じて、またはそれぞれの Web サイト経由でテンプレート VM 上にインストールする必要があります。

## <a name="conclusion"></a>まとめ

この記事では、効果的なクラスのために Windows テンプレート VM を準備する、省略可能な手順を示しました。  手順には、OneDrive のインストールと Microsoft 365 のインストール、Windows 用の更新プログラムのインストールおよび Microsoft Store アプリ用の更新プログラムのインストールが含まれます。  また、クラスに最適なスケジュールに更新プログラムを設定する方法について説明しました。  

## <a name="next-steps"></a>次のステップ
コスト管理に役立つ、Windows のシャットダウン動作を制御する方法に関する記事を参照してください。「[Windows シャットダウン動作の制御ガイド](how-to-windows-shutdown.md)」