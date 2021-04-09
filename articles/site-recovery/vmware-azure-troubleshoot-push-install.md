---
title: Azure Site Recovery でのモビリティ サービスのプッシュ インストールに関するトラブルシューティング
description: Azure Site Recovery を使用したディザスター リカバリーのためにレプリケーションを有効にする際のモビリティ サービスのインストールに関するエラーのトラブルシューティング。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127874"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Mobility Service のプッシュ インストールに関するトラブルシューティング

Mobility Service のインストールは、レプリケーションを有効にするための重要な手順です。 前提条件を満たしていて、サポート対象の構成を使用していれば、この手順は成功します。 Mobility Service のインストール時に直面する最も一般的な問題の原因は次のとおりです。

* [資格情報/特権のエラー](#credentials-check-errorid-95107--95108)
* [ログイン エラー](#login-failures-errorid-95519-95520-95521-95522)
* [接続エラー](#connectivity-failure-errorid-95117--97118)
* [ファイルとプリンターの共有エラー](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation (WMI) のエラー](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [サポートされていないオペレーティング システム](#unsupported-operating-systems)
* [サポートされていないブート構成](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [ボリューム シャドウ コピー サービス (VSS) のインストール エラー](#vss-installation-failures)
* [GRUB 構成でデバイス UUID ではなくデバイス名が使用されている](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [論理ボリューム マネージャー (LVM) のボリューム](#lvm-support-from-920-version)
* [再起動の警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

レプリケーションを有効にすると、Azure Site Recovery によって Mobility Service エージェントの仮想マシン (VM) へのインストールが試行されます。 このプロセスの一環として、構成サーバーによって仮想マシンとの接続とエージェントのコピーが試行されます。 インストールを成功させるには、段階的なトラブルシューティング ガイダンスに従ってください。

## <a name="credentials-check-errorid-95107--95108"></a>資格情報チェック (ErrorID: 95107 および 95108)

レプリケーションを有効にする際に選択したユーザー アカウントが有効かつ正確なものであることを確認します。 Azure Site Recovery には、プッシュ インストールを実行するための **ルート** アカウント、または **管理者特権** を持つユーザー アカウントが必要です。 それ以外の場合、プッシュ インストールはソース マシン上でブロックされます。

Windows (**エラー 95107**) の場合は、ローカル アカウントまたはドメイン アカウントのいずれかを使用して、ユーザー アカウントにソース コンピューターの管理アクセス権があることを確認します。 ドメイン アカウントを使用していない場合、ローカル コンピューターでリモート ユーザー アクセス制御を無効にする必要があります。

* リモート ユーザー アクセス制御を無効にするレジストリ キーを手動で追加するには:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 新しい `DWORD`: `LocalAccountTokenFilterPolicy` を追加します
  * 値を `1` に設定します

* レジストリ キーを追加するには、コマンド プロンプトから次のコマンドを実行します。

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Linux (**エラー 95108**) の場合、Mobility Service エージェントのインストールを成功させるには、**ルート** アカウントを選択する必要があります。 また、SSH ファイル転送プロトコル (SFTP) サービスが実行されている必要があります。 _sshd_config_ ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。

1. **root** としてサインインします。
1. _/etc/ssh/sshd_config ファイル_ に移動し、`PasswordAuthentication` で始まる行を見つけます。
1. その行のコメントを解除し、値を `yes` に変更します。
1. `Subsystem` で始まる行を見つけて、その行のコメントを解除します。
1. `sshd` サービスを再開します。

選択したユーザー アカウントの資格情報を変更する場合は、[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従ってください。

## <a name="insufficient-privileges-failure-errorid-95517"></a>特権不足エラー (ErrorID: 95517)

モビリティ エージェントのインストールを選択したユーザーに管理者特権がない場合、ソース マシンへのモビリティ エージェント ソフトウェアのコピーは、構成サーバーおよびスケールアウト プロセス サーバーでは、ソース マシンへのモビリティ エージェント ソフトウェアのコピーは許可されません。 このエラーはアクセス拒否エラーの結果です。 ユーザー アカウントに管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従ってください。

## <a name="insufficient-privileges-failure-errorid-95518"></a>特権不足エラー (ErrorID: 95518)

ソース マシンへのサインインを試行しているときに、プライマリ ドメインとワークステーション間でドメインの信頼関係を確立できなかった場合、モビリティ エージェントのインストールは、エラー ID 95518 で失敗します。 モビリティ エージェントをインストールするために使用されるユーザー アカウントに、ソース マシンのプライマリ ドメイン経由でサインインするための管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従ってください。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>ログイン エラー (ErrorID: 95519、95520、95521、95522)

ここでは、資格情報とログイン エラー メッセージについて説明します。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>ユーザー アカウントの資格情報が無効になっている (エラー ID:95519)

レプリケーションを有効にする際に選択したユーザー アカウントが無効でした。 ユーザー アカウントを有効にするには、[こちらの記事](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)を参照するか、次のコマンドのテキスト _username_ を実際のユーザー名に置き換えて実行してください。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>ログイン試行が複数回失敗したため、資格情報がロックアウトされた (ErrorID: 95520)

コンピューターにアクセスするための再試行が複数回失敗すると、ユーザー アカウントがロックされます。 このエラーは以下が原因である可能性があります。

* 構成のセットアップ時に指定された資格情報が正しくありません。
* レプリケーションを有効にする際に選択したユーザー アカウントが間違っています。

[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従って選択した資格情報を変更し、操作をやり直してください。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>ソース マシン上でログオン サーバーを利用できない (ErrorID: 95521)

このエラーは、ソース マシン上でログオン サーバーを利用できない場合に発生します。 ログオン サーバーが使用できない場合、ログイン要求は失敗し、モビリティ エージェントをインストールできません。 ログインを成功させるには、ソース マシン上でログオン サーバーが利用可能であり、Netlogon サービスが開始されていることを確認します。 詳細については、「[Windows ログオンのシナリオ](/windows-server/security/windows-authentication/windows-logon-scenarios)」を参照してください。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>ソース マシン上でログオン サービスが実行されていない (エラー ID:95522)

ソース マシン上でログイン サービスが実行されていないため、ログイン要求エラーが発生しています。 モビリティ エージェントをインストールできません。 このエラーを解決するには、次のいずれかの方法を使用して、ソース マシン上で `Netlogon` サービスを開始します。

* コマンド プロンプトから `Netlogon` サービスを開始するには、コマンド `net start Netlogon` を実行します。
* タスク マネージャーから `Netlogon` サービスを開始します。

## <a name="connectivity-failure-errorid-95117--97118"></a>接続エラー (ErrorID: 95117、97118)

モビリティ エージェントをインストールするために、構成サーバーおよびスケールアウト プロセス サーバーによってソース VM への接続が試行されます。 このエラーは、ネットワークの接続に問題があるため、ソース マシンにアクセスできない場合に発生します。

このエラーを解決するには:

* ローカル アカウントまたはドメイン アカウントのいずれかを使用して、ユーザー アカウントにソース コンピューターの管理アクセス権があることを確認します。 ドメイン アカウントを使用していない場合、ローカル コンピューターでリモート ユーザー アクセス制御を無効にする必要があります。
  * リモート ユーザー アクセス制御を無効にするレジストリ キーを手動で追加するには:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * 新しい `DWORD`: `LocalAccountTokenFilterPolicy` を追加します
    * 値を `1` に設定します
  * レジストリ キーを追加するには、コマンド プロンプトから次のコマンドを実行します。

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* 構成サーバーからソース マシンに対して ping を実行できることを確認します。 レプリケーションを有効にする際にスケールアウト プロセス サーバーを選択した場合は、プロセス サーバーからソース マシンに ping を実行できることを確認します。

* ファイルとプリンターの共有サービスがお使いの仮想マシンで有効になっていることを確認します。 [こちら](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)で手順を確認してください。

* お使いの仮想マシンで WMI サービスが有効になっていることを確認します。 [こちら](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)で手順を確認してください。

* お使いの仮想マシン上のネットワーク共有フォルダーにプロセス サーバーからアクセスできることを確認します。 [こちら](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)で手順を確認してください。

* 次のコマンドに示すように、ソース サーバー マシンのコマンド ラインから `Telnet` を使用して、HTTPS ポート 135 で構成サーバーまたはスケールアウト プロセス サーバーに ping を実行します。 このコマンドを使用すると、ネットワーク接続の問題またはファイアウォール ポートのブロックの問題がないかどうかを確認できます。

  `telnet <CS/ scale-out PS IP address> <135>`

* また、Linux VM の場合は、次の操作を実行します。
  * 最新の OpenSSH、OpenSSH Server、および OpenSSL パッケージがインストールされているかどうかを確認します。
  * SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
  * SFTP サービスが実行中である必要があります。 _sshd_config_ ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。

    1. **root** としてサインインします。
    1. _/etc/ssh/sshd_config_ ファイルに移動し、`PasswordAuthentication` で始まる行を見つけます。
    1. その行のコメントを解除し、値を `yes` に変更します。
    1. `Subsystem` で始まる行を見つけて、その行のコメントを解除します。
    1. `sshd` サービスを再開します。

* 一定の期間が経過した後に適切な応答がない場合は、接続の試行が失敗した可能性があります。または、接続されたホストが応答しなかったため接続を確立できませんでした。
* 接続、ネットワーク、ドメインに関連する問題である可能性があります。 また、DNS 名解決の問題または TCP ポート消費の問題が原因である可能性もあります。 ドメインにそのような既知の問題があるかどうかを確認してください。

## <a name="connectivity-failure-errorid-95523"></a>接続エラー (ErrorID: 95523)

このエラーは、ソース マシンが存在するネットワークが見つからない場合、削除された場合、または使用できなくなった場合に発生します。 このエラーを解決する唯一の方法は、ネットワークが存在することを確認することです。

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>ソース マシンのネットワーク共有フォルダーのアクセスを確認する (ErrorID:95105、95523)

指定された資格情報を使って、プロセス サーバー (PS) から仮想マシンのネットワーク共有フォルダーにリモートでアクセスできるかどうかを確認します。 アクセスを確認するには: 

1. プロセス サーバー マシンにログインします。
2. エクスプローラーを開きます。 アドレス バーに「`\\<SOURCE-MACHINE-IP>\C$`」と入力し、Enter キーを押します。

    ![PS で開かれたフォルダー](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. ファイル エクスプローラーから、資格情報が求められます。 ユーザー名とパスワードを入力して、[OK] をクリックします。 <br><br/>

    ![資格情報の入力](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > ソース マシンがドメインに参加している場合は、`<domainName>\<username>` としてユーザー名と共にドメイン名を指定します。 ソース マシンがワーク グループ内にある場合は、ユーザー名のみを指定します。

4. 接続に成功した場合、プロセス サーバーからソース マシンのフォルダーをリモートで表示できます。

    ![ソース マシンから表示可能なフォルダー](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

接続に失敗した場合は、すべての前提条件を満たしているかどうか確認してください。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>ファイルとプリンターの共有サービス チェック (ErrorID: 95105 および 95106)

接続チェックが完了したら、ファイルとプリンターの共有サービスが仮想マシンで有効になっているかどうかを確認します。 ソース マシン上にモビリティ エージェントをコピーするには、これらの設定が必要です。

**Windows 2008 R2 およびそれ以前のバージョン** の場合:

* Windows ファイアウォールを介してファイルとプリンターの共有を有効にする
  1. **[コントロール パネル]**  >  **[システムとセキュリティ]**  >  **[Windows ファイアウォール]** を開きます。 左側のペインで、コンソール ツリーの **[詳細設定]**  >  **[受信の規則]** を選択します。
  1. [ファイルとプリンターの共有 (NB セッション受信)] 規則と [ファイルとプリンターの共有 (SMB 受信)] 規則を探します。
  1. 各規則を右クリックして、 **[規則の有効化]** をクリックします。

* グループ ポリシーを使用してファイル共有を有効にするには:
  1. **[スタート]** に移動し、「`gpmc.msc`」と入力して検索します。
  1. ナビゲーション ウィンドウで、 **[ローカル コンピューター ポリシー]**  >  **[ユーザーの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[ネットワーク共有]** フォルダーを開きます。
  1. 詳細ウィンドウで、 **[ユーザーがプロファイル内のファイルを共有できないようにします]** をダブルクリックします。

     グループ ポリシー設定を無効にして、ユーザーがファイルを共有できるようにするには、 **[無効]** を選択します。

  1. **[OK]** を選択して変更を保存します。

  詳細については、「[グループ ポリシーを使用してファイルの共有を有効または無効にする](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))」を参照してください。

より新しいバージョンの Windows または Linux でファイルとプリンターの共有を有効にするには、[VMware VM と物理サーバーのディザスター リカバリーのために Mobility Service をインストールする](vmware-azure-install-mobility-service.md)方法のページの手順に従ってください。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) の構成チェック (エラー コード:95103)

ファイルとプリンターのサービスのチェックが完了したら、ファイアウォール経由のプライベート、パブリック、およびドメイン プロファイル用の WMI サービスを有効にします。 ソース マシン上でリモート実行を完了するには、これらの設定が必要です。

WMI を有効にするには:

1. **[コントロール パネル]**  >  **[セキュリティ]** に移動し、 **[Windows ファイアウォール]** を選択します。
1. **[設定の変更]** を選択し **[例外]** タブを選択します。
1. **[例外]** ウィンドウで、Windows Management Instrumentation (WMI) のチェック ボックスをオンにして、ファイアウォール経由の WMI トラフィックを有効にします。

次のコマンドを使用して、コマンド プロンプトからファイアウォールを通過する WMI トラフィックを有効にすることもできます。

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

WMI のトラブルシューティングに関するその他の記事を次に示します。

* [基本的な WMI テスト](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI のトラブルシューティング](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI スクリプトと WMI サービスに関する問題のトラブルシューティング](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>サポートされていないオペレーティング システム

サポートされていないオペレーティング システムが原因で、エラーが発生する可能性があります。 Mobility Service を正常にインストールするには、サポートされているオペレーティング システムとカーネルのバージョンを使用します。 プライベート パッチを使用しないようにします。

Azure Site Recovery でサポートされているオペレーティング システムとカーネルのバージョンのリストを表示する場合は、[サポート マトリックス ドキュメント](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>サポートされていないブート ディスク構成 (エラー ID:95309、95310、95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>ブートおよびシステムのパーティション/ボリュームが同じディスクではない (ErrorID: 95309)

9\.20 より前のバージョンでは、異なるディスク上のブートおよびシステムのパーティションおよびボリュームは、サポートされる構成ではありませんでした。 [9.20 バージョン](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)以降では、この構成がサポートされています。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>ブート ディスクが使用できない (ErrorID: 95310)

ブート ディスクのない仮想マシンは保護されません。 ブート ディスクがあると、フェールオーバー操作中に仮想マシンがスムーズに回復します。 ブート ディスクがないと、フェールオーバー後、マシンの起動に失敗します。 確実に仮想マシンにブート ディスクを搭載させ、操作をやり直してください。 また、同じコンピューター上の複数のブート ディスクはサポートされていません。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>ソース マシンに複数のブート ディスクが存在する (エラー ID:95311)

1 つの仮想マシンに複数のブート ディスクが存在する状態は[サポートされている構成](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)ではありません。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>複数のディスク上のシステム パーティション (ErrorID: 95313)

9\.20 バージョンより前のバージョンでは、複数のディスク上のルート パーティションまたはボリュームの設定はサポートされていない構成でした。 [9.20 バージョン](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)以降では、この構成がサポートされています。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>GRUB 構成で UUID ではなくデバイス名が指定されているため、保護を有効にできなかった (エラー ID:95320)

### <a name="possible-cause"></a>考えられる原因

Grand Unified Bootloader (GRUB) 構成ファイル ( _/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_、または _/etc/default/grub_) には、ユニバーサル一意識別子 (UUID) ではなく実際のデバイス名としてパラメーター **root** および **resume** の値が含まれる場合があります。 VM を再起動するとデバイス名が変わる可能性があるため、Site Recovery では UUID アプローチが義務付けられています。 たとえば、フェールオーバー時に VM が同じ名前でオンラインにならず、問題が発生する場合があります。

次に例を示します。

- 次の行は、GRUB ファイル _/boot/grub2/grub.cfg_ のものです。

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 次の行は、GRUB ファイル _/boot/grub/menu.lst_ のものです。

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 行には、UUID ではなく、パラメーター **root** および **resume** の実際のデバイス名が含まれています。

### <a name="how-to-fix"></a>修正方法

デバイス名を対応する UUID に置き換える必要があります。

1. コマンド `blkid \<device name>` を実行して、デバイスの UUID を検出します。

   次に例を示します。

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 次に、`root=UUID=\<UUID>` のような形式で、デバイス名を UUID に置き換えます。 たとえば、ファイル _/boot/grub2/grub.cfg_、 _/boot/grub2/grub.cfg_、または _/etc/default/grub_ で、デバイス名を前述の root および resume パラメーターの UUID に置き換えた場合、ファイル内の行は次のようになります。

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 保護を再開します。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Mobility Service のインストールが完了する際に再起動の警告が返された (ErrorID: 95265 および 95266)

Site Recovery Mobility Service には多数のコンポーネントがありますが、そのうちの 1 つに、フィルター ドライバーと呼ばれるものがあります。 フィルター ドライバーは、システムの再起動時にのみ、システム メモリ内に読み込まれます。 フィルター ドライバーの修正は、システムの再起動時に新しいフィルター ドライバーが読み込まれたときにのみ実現できます。

> [!IMPORTANT]
> これは警告であり、既存のレプリケーションは、新しいエージェントの更新後にも動作します。 再起動は、新しいフィルター ドライバーの利点を活用したい任意のタイミングで実行できますが、再起動しない場合は古いフィルター ドライバーが動作し続けます。 そのため、再起動せずに更新プログラムを適用すると、フィルター ドライバーを例外として、**Mobility Service の他の拡張機能と修正の利点が実現します**。 アップグレード後に毎回再起動を行うことは、推奨はされますが、必須ではありません。 再起動が必要な場合の詳細については、Azure Site Recovery のサービスの更新プログラムに関するページの「[モビリティ サービスのアップグレード後に再起動する](service-updates-how-to.md#reboot-after-mobility-service-upgrade)」セクションを参照してください。

> [!TIP]
>メンテナンス期間中のアップグレードのスケジュールに関するベスト プラクティスについては、Azure Site Recovery のサービスの更新プログラムに関するページの「[最新のオペレーティング システム/カーネルのサポート](service-updates-how-to.md#support-for-latest-operating-systemskernels)」を参照してください。

## <a name="lvm-support-from-920-version"></a>9\.20 以降のバージョンでの LVM のサポート

9\.20 より前のバージョンでは、論理ボリューム マネージャー (LVM) はデータ ディスクでのみサポートされていました。 `/boot` パーティションは、LVM ボリュームではなく、ディスク パーティション上にある必要があります。

[9.20 バージョン](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)以降では、[LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 上の OS ディスクがサポートされています。

## <a name="insufficient-space-errorid-95524"></a>空き領域不足 (ErrorID: 95524)

ソース マシン上にモビリティ エージェントをコピーする場合は、少なくとも 100 MB の空き領域が必要です。 ソース マシンに必要な空き領域があることを確認してから、操作をやり直してください。

## <a name="low-system-resources"></a>システム リソースが少ない

この問題で発生する可能性のあるエラー ID は、95572 と 95573 です。 この問題は、システムで使用できるメモリが少なく、モビリティ サービスのインストールでメモリを割り当てることができないときに発生します。 インストールが進行し、正常に完了できるよう、確実に十分なメモリが解放されているようにします。

## <a name="vss-installation-failures"></a>VSS インストール エラー

ボリューム シャドウ コピー サービス (VSS) のインストールは、モビリティ エージェントのインストールに含まれています。 このサービスは、アプリケーション整合性復旧ポイントを生成するプロセスで使用されます。 VSS のインストール中のエラーは、複数の理由で発生することがあります。 正確なエラーを特定するには、_C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ を参照します。 次のセクションでは、いくつかの一般的なエラーと解決手順を示します。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS エラー -2147023170 [0x800706BE] - 終了コード 511

この問題は、ウイルス対策ソフトウェアによって Azure Site Recovery サービスの操作がブロックされているときに、最もよく発生します。

この問題を解決するには、次の手順を実行します。

1. [ウイルス対策プログラムからのフォルダーの除外](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)の一覧を確認します。
1. ウイルス対策プロバイダーによって公開されているガイドラインに従って、Windows で DLL の登録をブロック解除します。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS エラー 7 [0x7] - 終了コード 511

このエラーは、VSS をインストールするためのメモリが不足しているために発生するランタイム エラーです。 この操作が正常に完了するためには、ディスク領域を増やします。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS エラー -2147023824 [0x80070430] - 終了コード 517

このエラーは、Azure Site Recovery VSS プロバイダー サービスが[削除対象としてマークされている](/previous-versions/ms838153(v=msdn.10))と発生します。 次のコマンドを実行し、ソース マシンに VSS を手動でインストールしてみてください

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS エラー -2147023841 [0x8007041F] - 終了コード 512

このエラーは、Azure Site Recovery VSS プロバイダー サービスのデータベースが[ロックされている](/previous-versions/ms833798(v=msdn.10))場合に発生します。コマンド プロンプトから次のコマンドを実行し、ソース マシンに VSS を手動でインストールしてみてください

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

エラーが発生した場合は、ウイルス対策プログラムまたはその他のサービスが "**開始中**" の状態で停止していないかどうかを確認します。 "**開始中**" 状態のプロセスでは、データベース サービスのロックを保持できます。 VSS プロバイダーのインストールでエラーが発生する原因になります。 "**開始中**" 状態のサービスが存在しないことを確認したうえで、上記の操作をやり直してください。

### <a name="vss-exit-code-806"></a>VSS 終了コード 806

このエラーは、インストールに使用されているユーザー アカウントに `CSScript` コマンドを実行するアクセス許可がない場合に発生します。 スクリプトを実行するユーザー アカウントに必要なアクセス許可を提供し、操作を再試行してください。

### <a name="other-vss-errors"></a>その他の VSS エラー

コマンド プロンプトから次のコマンドを実行し、ソース マシンに VSS プロバイダー サービスを手動でインストールしてみてください

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS エラー - 0x8004E00F

このエラーは、通常、モビリティ エージェントのインストール中に `DCOM` の問題が発生し、`DCOM` が重大な状態になった場合に発生します。

次の手順を使用して、エラーの原因を特定します。

### <a name="examine-the-installation-logs"></a>インストール ログを確認する

1. _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ にあるインストール ログを開きます。
2. 次のエラーがあれば、この問題が発生しています。

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

この問題を解決するには:

DCOM の問題を解決するためのサポートを得るために、[Microsoft Windows プラットフォーム チーム](https://aka.ms/Windows_Support)にお問い合わせください。

DCOM の問題が解決されたら、コマンド プロンプトから次のコマンドを使用して、Azure Site Recovery VSS プロバイダーを手動で再インストールします。

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

お客様のディザスター リカバリー要件の中でアプリケーションの整合性が特に重要ではない場合は、VSS プロバイダーのインストールをバイパスできます。

Azure Site Recovery VSS プロバイダーのインストールをバイパスし、インストール後に Azure Site Recovery VSS プロバイダーを手動でインストールするには、次の手順を実行します。

1. モビリティ サービスをインストールします。 インストールは次の手順で失敗します。**インストール後の構成**。
1. VSS のインストールをバイパスするには:
   1. 次の場所にある Azure Site Recovery モビリティ サービス インストール ディレクトリを開きます。

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. インストールが常に成功するように、Azure Site Recovery VSS プロバイダー インストール スクリプトの _InMageVSSProvider_Install_ および _InMageVSSProvider_Uninstall.cmd_ に次の行を追加して変更します。

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. モビリティ エージェントを手動でインストールします。
1. インストールが成功し、次の手順である **[構成]** に移る際に、追加した行を削除します。
1. VSS プロバイダーをインストールするには、管理者としてコマンド プロンプトを開き、次のコマンドを実行します。

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Azure Site Recovery VSS プロバイダーが Windows サービスにサービスとしてインストールされていることを確認します。 コンポーネント サービス MMC を開き、VSS プロバイダーが表示されていることを確認します。
1. VSS プロバイダーのインストールが引き続き失敗する場合は、テクニカル サポートに相談し、暗号化アプリケーション プログラミング インターフェイス (CAPI2) のアクセス許可エラーを解決します。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>クラスター化されていないマシンでクラスター サービスが有効になっていることが原因で VSS プロバイダーのインストールが失敗する

この問題が発生すると、Azure Site Recovery VSS プロバイダーのインストール中に Azure Site Recovery Mobility Agent のインストールが失敗します。 VSS プロバイダーのインストールを妨げる問題が `COM+` にあるため、このエラーが発生します。

### <a name="to-identify-the-issue"></a>問題を特定するには

構成サーバーの _C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log_ にあるログに、次の例外が見つかります。

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

この問題を解決するには:

1. このマシンがクラスター化されていないマシンであること、およびクラスター コンポーネントが使用されていないことを確認します。
1. コンポーネントが使用されていない場合は、マシンからクラスター コンポーネントを削除します。

## <a name="drivers-are-missing-on-the-source-server"></a>ソース サーバーのドライバーが見つからない

モビリティ エージェントのインストールに失敗した場合は、_C:\ProgramData\ASRSetupLogs_ にあるログを確認し、いくつかのコントロール セットの必須ドライバーの中に不足しているものがないか確認します。

この問題を解決するには:

1. `regedit.msc` などのレジストリ エディターを使用して、レジストリを開きます。
1. `HKEY_LOCAL_MACHINE\SYSTEM` ノードを開きます。
1. `SYSTEM` ノードで、コントロール セットを見つけます。
1. 各コントロール セットを開いて、次の Windows ドライバーがあることを確認します。

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. 不足しているドライバーを再インストールします。

## <a name="next-steps"></a>次のステップ

VMware VM のディザスター リカバリーを設定する方法については、[こちら](vmware-azure-tutorial.md)を参照してください。
