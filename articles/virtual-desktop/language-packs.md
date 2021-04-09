---
title: Windows Virtual Desktop の Windows 10 VM に言語パックをインストールする - Azure
description: Windows Virtual Desktop で Windows 10 マルチセッション VM の言語パックをインストールする方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463342"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Windows 10 マルチセッション イメージへの言語パックの追加

Windows Virtual Desktop は、ユーザーがいつでも、どこにいてもデプロイできるサービスです。 そのため、Windows 10 Enterprise マルチセッション イメージに表示される言語をユーザーがカスタマイズできるようにすることが重要です。

ユーザーの言語ニーズに対応するには、次の 2 つの方法があります。

- 言語ごとにカスタマイズされたイメージを使用して専用のホスト プールを作成します。
- 言語とローカライズの要件が異なる複数のユーザーを同じホスト プールに含めますが、各自が確実に必要な言語を選択できるようにイメージをカスタマイズします。

後者の方が、はるかに効率的でコスト効率が高い方法です。 ただし、ご自身の判断でニーズに最適な方法を決定してください。 この記事では、イメージの言語をカスタマイズする方法について説明します。

## <a name="prerequisites"></a>前提条件

Windows 10 Enterprise マルチセッション イメージをカスタマイズして複数の言語を追加するには、次のものが必要です。

- Windows 10 Enterprise マルチセッション バージョン 1903 以降を搭載した Azure 仮想マシン (VM)

- イメージで使用する OS バージョンの言語 ISO、オンデマンド機能 (FOD) ディスク 1、および受信トレイ アプリ ISO。 いずれも次の場所からダウンロードできます。
     
     - 言語 ISO:
        - [Windows 10 バージョン 1903 または 1909 言語パック ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10 バージョン 2004 または 20H2 言語パック ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD ディスク 1 ISO:
        - [Windows 10 バージョン 1903 または 1909 FOD ディスク 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10 バージョン 2004 または 20H2 FOD ディスク 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 受信トレイ アプリ ISO:
        - [Windows 10 バージョン 1903 または 1909 受信トレイ アプリ ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10 バージョン 2004 受信トレイ アプリ ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10 バージョン 20H2 受信トレイ アプリ ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Local Experience Pack (LXP) ISO ファイルを使用してイメージをローカライズする場合は、最適な言語エクスペリエンスのために適切な LXP ISO をダウンロードする必要もあります
        - Windows 10 バージョン 1903 または 1909 を使用している場合:
          - [Windows 10 バージョン 1903 または 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Windows 10 バージョン 2004 または 20H2 を使用している場合は、「[Windows 10 での言語の追加: 既知の問題](/windows-hardware/manufacture/desktop/language-packs-known-issue)」の情報を使用して、次の LXP ISO からご自分に適したものを見つけてください。
          - [Windows 10 バージョン 2004 または 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10 バージョン 2004 または 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10 バージョン 2004 または 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10 バージョン 2004 または 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10 バージョン 2004 または 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10 バージョン 2004 または 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Azure ファイル共有または Windows ファイル サーバー仮想マシン上のファイル共有

>[!NOTE]
>ファイル共有 (リポジトリ) は、カスタム イメージの作成に使用する予定の Azure VM からアクセスできる必要があります。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>言語パッケージとオンデマンド機能のコンテンツ リポジトリを作成する

言語パッケージと FOD 用のコンテンツ リポジトリおよび受信トレイ アプリ パッケージ用のリポジトリを作成するには、次の手順に従います。

1. Azure VM 上で、Windows 10 Enterprise マルチセッション バージョン 1903、1909、2004 イメージの Windows 10 Multi-Language ISO、FOD、および受信トレイ アプリを「[前提条件](#prerequisites)」のリンクからダウンロードします。

2. VM 上で ISO ファイルを開いてマウントします。

3. 言語パック ISO にアクセスし、**LocalExperiencePacks** および **x64\\langpacks** フォルダーの内容をコピーし、その内容をファイル共有に貼り付けます。

4. **FOD ISO ファイル** にアクセスし、その内容をすべてコピーしてファイル共有に貼り付けます。
5. 受信トレイ アプリ ISO で **amd64fre** フォルダーにアクセスし、準備した受信トレイ アプリ用のリポジトリにコンテンツをコピーします。

     >[!NOTE]
     > 限られたストレージを使用して作業している場合は、ユーザーに必要なことがわかっている言語のファイルのみをコピーします。 ファイル名に含まれる言語コードを参照して、ファイルを区別することができます。 たとえば、フランス語のファイル名には "fr-FR" というコードが含まれています。 使用可能なすべての言語の言語コードの完全な一覧については、[Windows で利用可能な言語パック](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)のページを参照してください。

     >[!IMPORTANT]
     > 言語によっては、異なる名前付け規則に従うサテライト パッケージに追加のフォントが必要になる場合があります。 たとえば、日本語のフォント ファイル名には "Jpan" が含まれます。
     >
     > [!div class="mx-imgBorder"]
     > ![ファイル名に言語タグ "Jpan" を含む日本語の言語パックの例。](media/language-pack-example.png)

6. 言語コンテンツ リポジトリ共有に対するアクセス許可を設定して、カスタム イメージの作成に使用する VM から読み取りアクセスができるようにします。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>カスタム Windows 10 Enterprise マルチセッション イメージを手動で作成する

カスタム Windows 10 Enterprise マルチセッション イメージを手動で作成するには、次のようにします。

1. Azure VM をデプロイし、Azure ギャラリーにアクセスして、使用している Windows 10 Enterprise マルチセッションの現在のバージョンを選択します。
2. VM をデプロイしたら、ローカル管理者として RDP を使用して VM に接続します。
3. VM に最新の Windows 更新プログラムがすべて含まれていることを確認します。 必要に応じて、更新プログラムをダウンロードし、VM を再起動します。
4. 言語パッケージ、FOD、受信トレイ アプリのファイル共有リポジトリに接続して、ドライブ (たとえば、ドライブ E) にマウントします。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>カスタム Windows 10 Enterprise マルチセッション イメージを自動的に作成する

自動化されたプロセスを通じて言語をインストールする場合は、PowerShell でスクリプトを設定できます。 次のサンプル スクリプトを使用すると、Windows 10 Enterprise マルチセッション バージョン 2004 用のスペイン語 (スペイン)、フランス語 (フランス)、および中国語 (PRC) の言語パックとサテライト パッケージをインストールできます。 このスクリプトで、言語インターフェイス パックと必要なすべてのサテライト パッケージをイメージに統合します。 ただし、このスクリプトを変更して他の言語をインストールすることもできます。 スクリプトは、管理者特権の PowerShell セッションから実行してください。そうしないと、機能しません。

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

インストールする必要がある言語の数によっては、スクリプトの実行に時間がかかることがあります。

スクリプトの実行が完了したら、言語パックが正しくインストールされていることを確認します。そのためには、 **[スタート]**  >  **[設定]**  >  **[時刻と言語]**  >  **[言語]** に移動します。 そこに言語ファイルがある場合は、完了しています。

Windows イメージに言語を追加したら、追加した言語がサポートされるように受信トレイ アプリを更新する必要もあります。 これを行うには、プレインストールされているアプリを受信トレイ アプリ ISO のコンテンツで更新します。 この更新を切断されている (VM からインターネットにアクセスできない) 環境で実行するには、次の PowerShell スクリプト サンプルを使用してプロセスを自動化します。

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>ISO に含まれている受信トレイ アプリは、プレインストールされている Windows アプリの最新バージョンではありません。 すべてのアプリの最新バージョンを入手するには、追加の言語をインストールした後に、Windows ストア アプリを使用してアプリを更新し、更新プログラムの手動検索を実行する必要があります。

完了したら、必ず共有を解除してください。

## <a name="finish-customizing-your-image"></a>イメージのカスタマイズを完了する

言語パックをインストールしたら、カスタマイズしたイメージに追加する他のソフトウェアをインストールできます。

イメージのカスタマイズが完了したら、システム準備ツール (sysprep) を実行する必要があります。

Sysprep を実行するには、次のようにします。

1. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行してイメージを生成します。  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 「[Azure で一般化された VM の管理対象イメージを作成する](../virtual-machines/windows/capture-image-resource.md)」の手順に従って、VM を停止してから、それをマネージド イメージにキャプチャします。

3. これで、カスタマイズしたイメージを使用して Windows Virtual Desktop ホスト プールをデプロイできるようになりました。 ホスト プールのデプロイ方法の詳細については、「[チュートリアル: Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」を参照してください。

## <a name="enable-languages-in-windows-settings-app"></a>Windows 設定アプリで言語を有効にする

最後に、ホスト プールをデプロイしたら、各ユーザーの言語一覧に言語を追加して、[設定] メニューで優先する言語を選択できるようにする必要があります。

インストールした言語をユーザーが確実に選択できるようにするには、ユーザーとしてサインインし、次の PowerShell コマンドレットを実行して、インストールした言語パックを [言語] メニューに追加します。 このスクリプトは、ユーザーが自分のセッションにサインインしたときにアクティブになる自動化されたタスクまたはログオン スクリプトとして設定することもできます。

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

ユーザーは言語設定を変更した後、Windows Virtual Desktop セッションからサインアウトし、変更を有効にするためにもう一度サインインする必要があります。 

## <a name="next-steps"></a>次のステップ

言語パックの既知の問題については、「[Windows 10 バージョン 1803 以降のバージョンでの言語パックの追加: 既知の問題](/windows-hardware/manufacture/desktop/language-packs-known-issue)」を参照してください。

Windows 10 Enterprise マルチセッションについて他に質問がある場合は、[FAQ](windows-10-multisession-faq.md) をご確認ください。
