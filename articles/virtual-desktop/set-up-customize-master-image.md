---
title: マスター VHD イメージを準備してカスタマイズする - Azure
description: Windows Virtual Desktop のマスター イメージを準備、カスタマイズし、Azure にアップロードする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca92e6e888ead6bbd79c7a8d524516ee95e8c45d
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092930"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>マスター VHD イメージを準備してカスタマイズする

この記事では、仮想マシン (VM) を作成してそこにソフトウェアをインストールする方法など、マスター仮想ハード ディスク (VHD) イメージを準備して Azure にアップロードする方法を説明します。 これらの手順は、組織の既存のプロセスで使用できる Windows Virtual Desktop 固有の構成に対するものです。

>[!IMPORTANT]
>Azure イメージ ギャラリーのイメージを使用することをお勧めします。 ただし、カスタマイズしたイメージを使用する必要がある場合は、Windows Virtual Desktop エージェントが VM にまだインストールされていないことを確認してください。 カスタマイズしたイメージを Windows Virtual Desktop エージェントで使用すると、登録がブロックされたり、ユーザー セッションの接続が妨げられたりするなど、イメージに関する問題が発生する可能性があります。  

## <a name="create-a-vm"></a>VM の作成

Windows 10 Enterprise マルチセッションは、Azure イメージ ギャラリーで入手できます。 このイメージをカスタマイズにするには、2 つのオプションがあります。

最初のオプションでは、「[管理イメージから VM を作成する](../virtual-machines/windows/create-vm-generalized-managed.md)」の手順に従ってAzure 内で仮想マシン (VM) をプロビジョニングしてから、「[ソフトウェアの準備とインストール](set-up-customize-master-image.md#software-preparation-and-installation)」に進みます。

2 番目のオプションでは、イメージをダウンロードし、Hyper-V VM をプロビジョニングし、ニーズに合わせてカスタマイズすることで、イメージをローカルで作成します。これについては次のセクションで説明します。

### <a name="local-image-creation"></a>ローカル イメージの作成

イメージをローカルの場所にダウンロードした後、**Hyper-V マネージャー** を開き、コピーした VHD を使用して VM を作成します。 次の手順はシンプルなバージョンですが、「[Create a virtual machine in Hyper-V (Hyper-V 内で仮想マシンを作成する)](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)」で詳細な手順を確認できます。

コピーした VHD を使用して VM を作成するには:

1. **新しい仮想マシン ウィザード** を開きます。

2. [世代の指定] ページで、 **[第 1 世代]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[世代の指定] ページのスクリーンショット。 [第 1 世代] オプションが選択されている。](media/a41174fd41302a181e46385e1e701975.png)

3. [チェックポイントの種類] で、チェック ボックスをオフにしてチェックポイントを無効にします。

    > [!div class="mx-imgBorder"]
    > ![[チェックポイント] ページの [チェックポイントの種類] セクションのスクリーンショット。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

PowerShell で次のコマンドレットを実行してチェックポイントを無効にすることもできます。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定ディスク

既存の VHD から VM を作成する場合、既定ではダイナミック ディスクが作成されます。 次の図に示すように、 **[ディスクの編集]** を選択して固定ディスクに変更できます。 詳しい手順については、「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」をご覧ください。

> [!div class="mx-imgBorder"]
> ![[ディスクの編集] オプションのスクリーンショット。](media/35772414b5a0f81f06f54065561d1414.png)

次の PowerShell コマンドレットを実行して、ディスクを固定ディスクに変更することもできます。

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>ソフトウェアの準備とインストール

このセクションでは、FSLogix と Windows Defender の準備とインストールのほか、アプリとイメージのレジストリについてのいくつかの基本的な構成オプションについて説明します。

エンタープライズ用 Microsoft 365 アプリと OneDrive を VM にインストールする場合は、「[マスター VHD イメージに Office をインストールする](install-office-on-wvd-master-image.md)」に移動し、そこに記載されている手順に従ってアプリをインストールします。 完了したら、この記事に戻ります。

ユーザーが特定の LOB アプリケーションにアクセスする必要がある場合は、このセクションの手順を完了した後にそれらをインストールすることをお勧めします。

### <a name="set-up-user-profile-container-fslogix"></a>ユーザー プロファイル コンテナーを設定する (FSLogix)

FSLogix コンテナーをイメージの一部として含めるには、「[ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)」の手順に従います。 [このクイックスタート](/fslogix/configure-cloud-cache-tutorial/)を使用して FSLogix コンテナーの機能をテストできます。

### <a name="configure-windows-defender"></a>Windows Defender を構成する

VM に Windows Defender が構成されている場合、ファイルの添付中は VHD ファイルと VHDX ファイルの内容全体をスキャンしないように構成されていることを確認してください。

この構成では、ファイル添付中の VHD ファイルと VHDX ファイルのスキャンのみ削除され、リアルタイム スキャンには影響しません。

Windows Server 上で Windows Defender を構成する手順について詳しくは、「[Windows Server 上で Windows Defender ウイルス対策の除外を構成する](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)」をご覧ください。

特定のファイルをスキャンから除外するように Windows Defender を構成する方法について詳しくは、「[ファイル拡張子とフォルダーの場所に基づく除外の構成と検証](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)」をご覧ください。

### <a name="disable-automatic-updates"></a>自動更新を無効にする

ローカル グループ ポリシーを介して自動更新を無効にするには:

1. **[ローカル グループ ポリシー エディター]\\[管理用テンプレート]\\[Windows コンポーネント]\\[Windows Update]** を開きます。
2. **[Configure Automatic Update]\(自動更新の構成\)** を右クリックし、 **[無効]** に設定します。

コマンド プロンプトで次のコマンドを実行して自動更新を無効にすることもできます。

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 PC のスタート画面のレイアウトを指定する (省略可能)

Windows 10 PC のスタート画面のレイアウトを指定するには、このコマンドを実行します。

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>タイム ゾーン リダイレクトを設定する

タイム ゾーン リダイレクトは、ホスト プール内のすべての VM が同じセキュリティ グループに属しているため、グループ ポリシー レベルで適用できます。

タイム ゾーンをリダイレクトするには:

1. Active Directory サーバー上で、 **[グループ ポリシー管理コンソール]** を開きます。
2. ドメインとグループ ポリシー オブジェクトを展開します。
3. グループ ポリシー設定に対して作成した **グループ ポリシー オブジェクト** を右クリックし、 **[編集]** を選択します。
4. **グループ ポリシー管理エディター** で、 **[コンピューターの構成]**  >  **[ポリシー]**  >  **[管理テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[デバイスとリソースのリダイレクト]** に移動します。
5. **[タイム ゾーン リダイレクトを許可する]** 設定を有効にします。

このコマンドをマスター イメージに対して実行してタイム ゾーンをリダイレクトすることもできます。

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>ストレージ センサーを無効にする

Windows 10 Enterprise または Windows 10 Enterprise マルチセッションを使用する Windows Virtual Desktop セッション ホストでは、ストレージ センサーを無効にすることをお勧めします。 次のスクリーンショットに示すように、[設定] メニューの **[ストレージ]** でストレージ センサーを無効にできます。

> [!div class="mx-imgBorder"]
> ![[設定] の [ストレージ] メニューのスクリーン ショット。 [ストレージ センサー] オプションはオフになっている。](media/storagesense.png)

次のコマンドを実行して、レジストリで設定を変更することもできます。

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>追加の言語サポートを含める

この記事では、言語とリージョンのサポートの構成方法については説明しません。 詳細については、次の記事を参照してください。

- [Windows イメージへの言語の追加](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [オンデマンド機能](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Language and region features on demand (FOD) (言語とリージョンのオンデマンド機能 (FOD))](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>その他のアプリケーションとレジストリを構成する

このセクションでは、アプリケーションとオペレーティング システムの構成について説明します。 このセクションのすべての構成は、コマンドラインと regedit ツールで実行できるレジストリ エントリを使用して行います。

>[!NOTE]
>グループ ポリシー オブジェクト (GPO) またはレジストリ インポートのいずれかを使用して構成にベスト プラクティスを実装できます。 管理者は、組織の要件に基づいていずれかのオプションを選択できます。

Windows 10 Enterprise マルチセッションでのテレメトリ データのフィードバック ハブ コレクションの場合は、次のコマンドを実行します。

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

ワトソン クラッシュを修正するには、次のコマンドを実行します。

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

5k 解像度のサポートを修正するには、レジストリ エディターに次のコマンドを入力します。 サイドバイサイド スタックを有効にする前に、コマンドを実行する必要があります。

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Azure にアップロードするイメージの準備

構成が完了し、すべてのアプリケーションをインストールしたら、「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」の手順に従ってイメージを準備します。

イメージのアップロードの準備ができたら、VM がオフのままであるか、または割り当てが解除された状態になっていることを確認します。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure 内のストレージ アカウントへのマスター イメージのアップロード

このセクションは、マスター イメージがローカルで作成された場合にのみ適用されます。

次の手順では、マスター イメージを Azure ストレージ アカウントにアップロードする方法を説明します。 Azure ストレージ アカウントがまだない場合は、[こちらの記事](/azure/developer/javascript/tutorial-vscode-static-website-node-03)の指示に従って作成します。

1. まだ行っていない場合は、VM イメージ (VHD) を固定に変換します。 イメージを固定に変換しない場合は、イメージを正常に作成できません。

2. ストレージ アカウント内の BLOB コンテナーに VHD をアップロードします。 [Storage Explorer ツール](https://azure.microsoft.com/features/storage-explorer/)を使用して迅速にアップロードできます。 Storage Explorer ツールについて詳しくは、[こちらの記事](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)をご覧ください。

    > [!div class="mx-imgBorder"]
    > ![Microsoft Azure Storage Explorer ツールの検索ウィンドウのスクリーンショット。 [.vhd/vhdx ファイルをページ BLOB としてアップロードする (推奨)] チェック ボックスがオンになっている。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 次に、ブラウザー上で Azure portal に移動し、"イメージ" を検索します。 次のスクリーンショットに示すように、検索すると **[イメージの作成]** ページが表示されます。

    > [!div class="mx-imgBorder"]
    > ![イメージの値の例が入力された、Azure portal の [イメージの作成] ページのスクリーン ショット。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. イメージを作成すると、次のスクリーンショットに示すような通知が表示されます。

    > [!div class="mx-imgBorder"]
    > !["イメージが作成されました" 通知のスクリーンショット。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>次のステップ

これでイメージが作成されたので、ホスト プールを作成または更新することができます。 ホスト プールの作成と更新の方法について詳しくは、以下の記事をご覧ください。

- [Azure Resource Manager テンプレートを使用してホスト プールを作成する](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [チュートリアル:](create-host-pools-azure-marketplace.md)Azure Marketplace を使用してホスト プールを作成する
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)
- [ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md)
- [Windows Virtual Desktop の負荷分散方法を構成する](configure-host-pool-load-balancing.md)

VHD イメージを準備またはカスタマイズした後に接続の問題が発生した場合は、[トラブルシューティング ガイド](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved)を参照して役立ててください。
