---
title: マスター VHD イメージに Office をインストールする - Azure
description: Windows Virtual Desktop マスター イメージに Office をインストールして、Azure 向けにカスタマイズする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326050"
---
# <a name="install-office-on-a-master-vhd-image"></a>マスター VHD イメージに Office をインストールする

この記事では、Microsoft 365 Apps for enterprise、OneDrive、およびその他の一般的なアプリケーションをマスター仮想ハード ディスク (VHD) イメージにインストールして、Azure にアップロードする方法を説明します。 ユーザーが特定の業種 (LOB) 向けアプリケーションにアクセスする必要がある場合は、この記事の手順を完了した後にそれらをインストールすることをお勧めします。

この記事では、仮想マシンが既に作成されていることを前提としています。 そうでない場合は、「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md#create-a-vm)」を参照してください

この記事では、Azure と Hyper-V マネージャーのどちらでプロビジョニングされているかにかかわらず、VM に対して昇格されたアクセス権があることも前提としています。 そうでない場合は、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

>[!NOTE]
>これらの手順は、組織の既存のプロセスで使用できる Windows Virtual Desktop 固有の構成に対するものです。

## <a name="install-office-in-shared-computer-activation-mode"></a>共有コンピューターのアクティブ化モードで Office をインストールする

共有コンピューターのライセンス認証により、組織内で複数のユーザーによってアクセスされるコンピューターに Microsoft 365 Apps for enterprise をデプロイすることができます。 共有コンピューターのライセンス認証の詳細については、[Microsoft 365 Apps に対する共有コンピューターのライセンス認証の概要](/deployoffice/overview-shared-computer-activation)に関するページを参照してください。

[Office 展開ツール](https://www.microsoft.com/download/details.aspx?id=49117)を使用して Office をインストールします。 Windows 10 Enterprise マルチセッションでは次のバージョンの Office のみがサポートされています。

   - Microsoft 365 Apps for enterprise
   - Microsoft 365 Business Premium サブスクリプションに付属している Microsoft 365 Apps for business

Office 展開ツールには、構成 XML ファイルが必要です。 次のサンプルをカスタマイズするには、「[Office 展開ツールの構成オプション](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)」をご覧ください。

マイクロソフトが提供しているこのサンプル構成 XML では、次の操作を行います。

   - 月次エンタープライズ チャネルから Office をインストールし、月次エンタープライズ チャネルから更新プログラムを配信します。
   - x64 アーキテクチャを使用します。
   - 自動更新を無効にします。
   - Office の既存のインストールを削除し、その設定を移行します。
   - 共有コンピューターのライセンス認証を有効にします。

>[!NOTE]
>Visio のステンシル検索機能が Windows Virtual Desktop で期待どおりに動作しないことがあります。

このサンプル構成 XML では、以下の操作は行われません。

   - Skype for Business のインストール
   - ユーザーごとのモードでの OneDrive のインストール。 詳しくは、「[マシンごとのモードで OneDrive をインストールする](#install-onedrive-in-per-machine-mode)」をご覧ください。

>[!NOTE]
>共有コンピューターのライセンス認証は、グループ ポリシー オブジェクト (GPO) またはレジストリ設定を使用して設定することができます。 GPO は、 **[コンピューターの構成] \\ [ポリシー] \\ [管理用テンプレート] \\ [Microsoft Office 2016 (マシン)] \\ [ライセンスの設定]** にあります

Office 展開ツールには、setup.exe が含まれています。 Office をインストールするには、コマンド ラインで次のコマンドを実行します。

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>サンプルの configuration.xml

次の XML サンプルでは、月次エンタープライズ チャネル リリースがインストールされます。

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office チームは、**OfficeClientEdition** パラメーターに 64 ビット インストールを使用することをお勧めします。

Office をインストールした後で、Office の既定の動作を更新できます。 次のコマンドを個別にまたはバッチ ファイルで実行して、動作を更新します。

```cmd
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

1. 最初に、OneDrive のインストーラーをステージングする場所を作成します。 ローカル ディスク フォルダーまたは [\\\\unc] (file://unc) の場所で問題ありません。

2. <https://aka.ms/OneDriveWVD-Installer> リンクを使用して、ステージング場所に OneDriveSetup.exe をダウンロードします。

3. **\<ExcludeApp ID="OneDrive" /\>** を省略することで Office を OneDrive と共にインストールした場合は、次のコマンドを実行して、管理者特権でのコマンド プロンプトから既存のユーザーごとの OneDrive インストールをアンインストールします。

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. このコマンドを管理者特権でのコマンド プロンプトから実行して、**AllUsersInstall** レジストリ値を設定します。

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 次のコマンドを実行して OneDrive をマシンごとのモードでインストールします。

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 次のコマンドを実行して、すべてのユーザーのサインイン時に開始する OneDrive を構成します。

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 次のコマンドを実行して、 **[Silently configure user account]\(ユーザー アカウントをサイレントで構成\)** を有効にします。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 次のコマンドを実行して、Windows の既知のフォルダーを OneDrive にリダイレクトおよび移動します。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams と Skype for Business

Windows Virtual Desktop では、Skype for Business はサポートされていません。

Microsoft Teams をインストールする方法については、「[Windows Virtual Desktop で Microsoft Teams を使用する](teams-on-wvd.md)」をご覧ください。 Windows Virtual Desktop での Microsoft Teams のメディア最適化はプレビューで使用できません。

## <a name="next-steps"></a>次のステップ

イメージに Office が追加されたので、引き続きマスター VHD イメージをカスタマイズすることができます。 「[マスター VHD イメージを準備してカスタマイズする](set-up-customize-master-image.md)」を参照してください。
