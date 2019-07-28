---
title: マスター VHD イメージに Office をインストールする - Azure
description: Azure への Windows Virtual Desktop プレビューのマスター イメージに Office をインストールし、カスタマイズする方法。
services: virtual-desktop
author: chjenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 0e89d37011ccdfc3acdace5b45faa8e9a64e4d3e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620463"
---
# <a name="install-office-on-a-master-vhd-image"></a>マスター VHD イメージに Office をインストールする

この記事では、Office 365 ProPlus、OneDrive、およびその他の一般的なアプリケーションをマスター仮想ハード ディスク (VHD) イメージにインストールして、Azure にアップロードする方法を説明します。 ユーザーが特定の業種 (LOB) 向けアプリケーションにアクセスする必要がある場合は、この記事の手順を完了した後にそれらをインストールすることをお勧めします。

この記事では、仮想マシンが既に作成されていることを前提としています。 そうでない場合は、「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md#create-a-vm)」を参照してください

この記事では、Azure と Hyper-V マネージャーのどちらでプロビジョニングされているかにかかわらず、VM に対して昇格されたアクセス権があることも前提としています。 そうでない場合は、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)」を参照してください。

>[!NOTE]
>これらの手順は、組織の既存のプロセスで使用できる Windows Virtual Desktop プレビュー固有の構成に対するものです。

## <a name="install-office-in-shared-computer-activation-mode"></a>共有コンピューターのアクティブ化モードで Office をインストールする

共有コンピューターのライセンス認証により、組織内で複数のユーザーによってアクセスされるコンピューターに Office 365 ProPlus をデプロイすることができます。 共有コンピューターのライセンス認証の詳細については、「[Office 365 ProPlus に対する共有コンピューターのライセンス認証の概要](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus)」を参照してください。

[Office 展開ツール](https://www.microsoft.com/download/details.aspx?id=49117)を使用して Office をインストールします。 Windows 10 Enterprise マルチセッションでは次のバージョンの Office のみがサポートされています。
- Office 365 ProPlus
- Microsoft 365 Business サブスクリプションに付属している Office 365 Business

Office 展開ツールには、構成 XML ファイルが必要です。 次のサンプルをカスタマイズするには、「[Office 展開ツールの構成オプション](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)」をご覧ください。

マイクロソフトが提供しているこのサンプル構成 XML では、次の操作を行います。

- Insiders チャネルから Office をインストールし、実行時に Insiders チャネルから更新プログラムを配信します。
- x64 アーキテクチャを使用します。
- 自動更新を無効にします。
- Visio とプロジェクトをインストールします。
- Office の既存のインストールを削除し、その設定を移行します。
- 共有コンピューターのライセンス認証を有効にします。

>[!NOTE]
>Visio のステンシル検索機能は、プレビューの構成中は Windows Virtual Desktop で動作しません。

このサンプル構成 XML では、以下の操作は行われません。

- Skype for Business のインストール
- ユーザーごとのモードでの OneDrive のインストール。 詳しくは、「[マシンごとのモードで OneDrive をインストールする](#install-onedrive-in-per-machine-mode)」をご覧ください。

>[!NOTE]
>共有コンピューターのライセンス認証は、グループ ポリシー オブジェクト (GPO) またはレジストリ設定を使用して設定することができます。 GPO は、 **[コンピューターの構成] \\ [ポリシー] \\ [管理用テンプレート] \\ [Microsoft Office 2016 (マシン)] \\ [ライセンスの設定]** にあります

Office 展開ツールには、setup.exe が含まれています。 Office をインストールするには、コマンド ラインで次のコマンドを実行します。

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>サンプルの configuration.xml

次の XML サンプルでは、Insider リリースがインストールされます。

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office チームは、**OfficeClientEdition** パラメーターに 64 ビット インストールを使用することをお勧めします。

Office をインストールした後で、Office の既定の動作を更新できます。 次のコマンドを個別にまたはバッチ ファイルで実行して、動作を更新します。

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>マシンごとのモードで OneDrive をインストールする

OneDrive は、通常はユーザーごとにインストールされます。 この環境では、マシンごとにインストールする必要があります。

マシンごとのモードで OneDrive をインストールする方法を次に示します。

1. 最初に、OneDrive のインストーラーをステージングする場所を作成します。 ローカル ディスク フォルダーまたは [\\\\unc](file://unc) で問題ありません。

2. <https://aka.ms/OneDriveWVD-Installer> リンクを使用して、ステージング場所に OneDriveSetup.exe をダウンロードします。

3. **\<ExcludeApp ID="OneDrive" /\>** を省略することで office を OneDrive と共にインストールした場合は、次のコマンドを実行して、管理者特権でのコマンド プロンプトから既存のユーザーごとの OneDrive インストールをアンインストールします。
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. このコマンドを管理者特権でのコマンド プロンプトから実行して、**AllUsersInstall** レジストリ値を設定します。

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 次のコマンドを実行して OneDrive をマシンごとのモードでインストールします。

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 次のコマンドを実行して、すべてのユーザーのサインイン時に開始する OneDrive を構成します。

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 次のコマンドを実行して、 **[Silently configure user account]\(ユーザー アカウントをサイレントで構成\)** を有効にします。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 次のコマンドを実行して、Windows の既知のフォルダーを OneDrive にリダイレクトおよび移動します。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams と Skype

Windows Virtual Desktop では、Skype for Business と Teams はサポートされていません。

## <a name="next-steps"></a>次の手順

イメージに Office が追加されたので、引き続きマスター VHD イメージをカスタマイズすることができます。 「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md)」を参照してください。
