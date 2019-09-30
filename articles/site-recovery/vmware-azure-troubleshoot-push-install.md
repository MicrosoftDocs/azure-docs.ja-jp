---
title: ディザスター リカバリーのためのレプリケーションを有効にする際のモビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのためのレプリケーションを有効にする際のモビリティ サービスのインストールに関するエラーのトラブルシューティング
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 4aa18379962c289f5094795988a247f4c7e35df2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910650"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

モビリティ サービスのインストールは、レプリケーションを有効にする際の重要な手順です。 前提条件を満たしていて、サポート対象の構成を使用していれば、この手順は成功します。 モビリティ サービスのインストール時に直面する最も一般的な問題の原因は次のとおりです。

* [資格情報/特権のエラー](#credentials-check-errorid-95107--95108)
* [ログイン エラー](#login-failures-errorid-95519-95520-95521-95522)
* [接続エラー](#connectivity-failure-errorid-95117--97118)
* [ファイルとプリンターの共有エラー](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI エラー](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [サポート対象外のオペレーティング システム](#unsupported-operating-systems)
* [サポートされていないブート構成](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS インストール エラー](#vss-installation-failures)
* [GRUB 構成でデバイス UUID ではなくデバイス名が使用されている](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM ボリューム](#lvm-support-from-920-version)
* [再起動の警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

レプリケーションを有効にすると、Azure Site Recovery はモビリティ サービス エージェントを仮想マシンにプッシュ インストールしようとします。 その一環として、構成サーバーは仮想マシンとの接続およびエージェントのコピーを試行します。 インストールを成功させるには、以下に示す段階的なトラブルシューティング ガイダンスに従ってください。

## <a name="credentials-check-errorid-95107--95108"></a>資格情報チェック (ErrorID: 95107 および 95108)

* レプリケーションを有効にする際に選択したユーザー アカウントが**有効かつ正確**なものであることを確認します。
* Azure Site Recovery には、プッシュ インストールを実行するための **ROOT** アカウント、または**管理者特権**を持つユーザー アカウントが必要です。 それ以外の場合、プッシュ インストールはソース マシン上でブロックされます。
  * Windows (**エラー 95107**) の場合は、ソース マシンに対する管理用のアクセス権 (ローカルまたはドメイン) がユーザー アカウントにあることを確認します。
  * ドメイン アカウントを使用していない場合、ローカル コンピューターでリモート ユーザー アクセス制御を無効にする必要があります。
    * リモート ユーザー アクセス制御を無効にするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに新しい DWORD である LocalAccountTokenFilterPolicy を追加します。 値を 1 に設定します。 この手順を実行するには、コマンド プロンプトで次のコマンドを実行します。

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux (**エラー 95108**) でモビリティ エージェントのインストールを成功させるには、ルート アカウントを選択する必要があります。 さらに、SFTP サービスが実行中である必要があります。 sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。
    1. root としてサインインします。
    2. /etc/ssh/sshd_config ファイルに移動し、PasswordAuthentication で始まる行を見つけます。
    3. この行のコメントを解除し、値を yes に変更します。
    4. Subsystem で始まる行を見つけ、その行のコメントを解除します。
    5. sshd サービスを再起動します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="insufficient-privileges-failure-errorid-95517"></a>特権不足エラー (ErrorID: 95517)

モビリティ エージェントのインストールを選択したユーザーに管理者特権がない場合、ソース マシンへのモビリティ エージェント ソフトウェアのコピーは、構成サーバー/スケールアウト プロセス サーバーによって許可されません。 そのため、このエラーはアクセス拒否エラーの結果です。 ユーザー アカウントに管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="insufficient-privileges-failure-errorid-95518"></a>特権不足エラー (ErrorID: 95518)

ソース マシンへのサインインを試行しているときに、プライマリ ドメインとワークステーション間でドメインの信頼関係を確立できなかった場合、モビリティ エージェントのインストールは、エラー ID 95518 で失敗します。 そのため、モビリティ エージェントをインストールするために使用されるユーザー アカウントに、ソース マシンのプライマリ ドメイン経由でサインインするための管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>ログイン エラー (エラー ID:95519、95520、95521、95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>ユーザー アカウントの資格情報が無効になっている (エラー ID:95519)

レプリケーションを有効にする際に選択したユーザー アカウントが無効になっています。 ユーザー アカウントを有効にするには、[こちらの記事](https://aka.ms/enable_login_user)を参照するか、次のコマンドのテキスト *username* を実際のユーザー名に置き換えて実行してください。
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>ログイン試行が複数回失敗したために資格情報がロックアウトされた (エラー ID:95520)

コンピューターにアクセスするための再試行が複数回失敗すると、ユーザー アカウントがロックされます。 このエラーは以下が原因である可能性があります。

* 構成のセットアップ時に指定された資格情報が正しくない、または
* レプリケーションを有効にする際に選択したユーザー アカウントが間違っている

そのため、選択されている資格情報を[こちら](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)の手順に従って変更し、しばらくしてから操作を再試行してください。

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>ソース マシン上でログオン サーバーを利用できない (エラー ID:95521)

このエラーは、ソース マシン上でログオン サーバーを利用できない場合に発生します。 ログオン サーバーを利用できないと、ログイン要求エラーが発生します。その結果、モビリティ エージェントをインストールできません。 ログインを成功させるには、ソース マシン上でログオン サーバーが利用可能であり、ログオン サービスが開始されていることを確認します。 詳細については、「KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) エラー メッセージ」をご覧ください。現在、ご利用いただけるログオン サーバーはありません。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>ソース マシン上でログオン サービスが実行されていない (エラー ID:95522)

ソース マシン上でログイン サービスが実行されていないため、ログイン要求エラーが発生しています。 そのため、モビリティ エージェントをインストールできません。 問題を解決してログインを成功させるには、ソース マシン上でログオン サービスが実行されていることを確認します。 ログオン サービスを開始するには、コマンド プロンプトから "net start Logon" コマンドを実行するか、タスク マネージャーから "NetLogon" サービスを開始します。

## <a name="connectivity-failure-errorid-95117--97118"></a>**接続エラー (ErrorID: 95117 および 97118)**

構成サーバー/スケールアウト プロセス サーバーは、モビリティ エージェントをインストールするためにソース VM への接続を試みます。 このエラーは、ネットワーク接続の問題が原因でソース マシンに到達できない場合に発生します。 解決するには、以下を行います。

* 構成サーバーからソース マシンに ping を実行できることを確認します。 レプリケーションを有効にする際にスケールアウト プロセス サーバーを選択した場合は、プロセス サーバーからソース マシンに ping を実行できることを確認します。
  * ソース サーバー マシンのコマンド ラインから、Telnet を使用して、次のように https ポート (135) を指定して構成サーバー/スケールアウト プロセス サーバーを ping し、ネットワーク接続の問題であるか、ファイアウォールのポート ブロックの問題であるかを確認します。

     `telnet <CS/ scale-out PS IP address> <135>`
* また、**Linux VM** の場合は次のことを確認します。
  * 最新の openssh、openssh-server、および openssl パッケージがインストールされているかどうかを確認します。
  * SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
  * SFTP サービスが実行中である必要があります。 sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。
    * root としてサインインします。
    * /etc/ssh/sshd_config ファイルに移動し、PasswordAuthentication で始まる行を見つけます。
    * この行のコメントを解除し、値を yes に変更します。
    * Subsystem で始まる行を見つけ、その行のコメントを解除します。
    * sshd サービスを再起動します。
* 一定の期間が経過した後に適切な応答がない場合は、接続の試行が失敗した可能性があります。または、接続されたホストが応答しなかったため接続を確立できませんでした。
* 接続/ネットワーク/ドメインに関連する問題である可能性があります。 また、DNS 名解決の問題または TCP ポート消費の問題が原因である可能性もあります。 ドメインにそのような既知の問題があるかどうかを確認してください。

## <a name="connectivity-failure-errorid-95523"></a>接続エラー (ErrorID: 95523)

このエラーは、その中にソース マシンが存在するネットワークが見つからない、削除されている、またはもう利用できない場合に発生します。 このエラーを解決する唯一の方法は、そのネットワークが存在することを確認することです。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>ファイルとプリンターの共有サービス チェック (ErrorID: 95105 および 95106)

接続チェックが完了したら、ファイルとプリンターの共有サービスが仮想マシンで有効になっているかどうかを確認します。 ソース マシン上にモビリティ エージェントをコピーするには、これらの設定が必要です。

**Windows 2008 R2 およびそれ以前のバージョン**の場合は、次の手順を実行します。

* Windows ファイアウォールを介してファイルとプリンターの共有を有効にする
  * コントロール パネルを開きます。[システムとセキュリティ]、[Windows ファイアウォール] の順に選択し、左側のウィンドウで [詳細設定] をクリックし、コンソール ツリーの [受信の規則] をクリックします。
  * [ファイルとプリンターの共有 (NB セッション受信)] 規則と [ファイルとプリンターの共有 (SMB 受信)] 規則を探します。 各規則を右クリックして、 **[規則の有効化]** をクリックします。
* グループ ポリシーを使用してファイル共有を有効にする
  * [スタート] メニューに移動し、「gpmc.msc」と入力して検索します。
  * ナビゲーション ウィンドウで、[ローカル コンピューター ポリシー]、[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、および [ネットワーク共有] の各フォルダーを順に開きます。
  * 詳細ウィンドウで、 **[ユーザーがプロファイル内のファイルを共有できないようにします]** をダブルクリックします。 グループ ポリシー設定を無効にして、ユーザーがファイルを共有できるようにするには、[無効] をクリックします。 [OK] をクリックして変更を保存します。 詳細については、「[グループ ポリシーを使用してファイルの共有を有効または無効にする](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))」を参照してください。

**より新しいバージョン**の場合は、「[VMware VM と物理サーバーのディザスター リカバリーのためにモビリティ サービスをインストールする](vmware-azure-install-mobility-service.md)」に記載されている手順に従ってファイルとプリンターの共有を有効にします。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) の構成チェック (エラー コード:95103)

ファイルとプリンターのサービスのチェックが完了したら、ファイアウォール経由のプライベート、パブリック、およびドメイン プロファイル用の WMI サービスを有効にします。 ソース マシン上でリモート実行を完了するには、これらの設定が必要です。 有効にするには、

* コントロール パネルに移動し、[セキュリティ] をクリックし、[Windows ファイアウォール] をクリックします。
* [設定の変更] をクリックし、[例外] タブをクリックします。
* [例外] ウィンドウで、Windows Management Instrumentation (WMI) のチェック ボックスをオンにして、ファイアウォール経由の WMI トラフィックを有効にします。 

また、コマンド プロンプトで、ファイアウォール経由の WMI トラフィックを有効にすることもできます。 次のコマンドを使用します。`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
WMI のトラブルシューティングに関するその他の記事を次に示します。

* [基本的な WMI テスト](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI のトラブルシューティング](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI スクリプトと WMI サービスに関する問題のトラブルシューティング](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>サポート対象外のオペレーティング システム

問題が発生するもう 1 つの最も一般的な理由は、サポート対象外のオペレーティング システムです。 モビリティ サービスのインストールを成功させるには、サポート対象のオペレーティング システム/カーネルのバージョンを使用してください。 プライベート修正プログラムは使用しないでください。
Azure Site Recovery でサポートされているオペレーティング システムとカーネルのバージョンのリストを表示する場合は、[サポート マトリックス ドキュメント](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>サポートされていないブート ディスク構成 (エラー ID:95309、95310、95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>ブートおよびシステムのパーティション/ボリュームが同じディスクではありません (ErrorID: 95309)

9\.20 より前のバージョンでは、異なるディスク上のブートおよびシステムのパーティション/ボリュームは、サポートされる構成ではありませんでした。 [9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、この構成がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>ブート ディスクが使用できない (エラー ID:95310)

ブート ディスクのない仮想マシンは保護されません。 これは、フェールオーバーの操作中に、仮想マシンを確実にスムーズに復旧させるためです。 ブート ディスクがないと、フェールオーバー後、マシンの起動に失敗します。 確実に仮想マシンにブート ディスクを搭載させ、操作をもう一度お試しください。 また、同じマシンに複数のブート ディスクを搭載させることはできません。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>ソース マシンに複数のブート ディスクが存在する (エラー ID:95311)

1 つの仮想マシンに複数のブート ディスクが存在する状態は[サポートされている構成](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)ではありません。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>複数のディスク上のシステム パーティション (ErrorID: 95313)

9\.20 より前のバージョンでは、複数のディスク上に存在するルート パーティションまたはボリュームは、サポートされる構成ではありませんでした。 [9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、この構成がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>GRUB 構成で UUID ではなくデバイス名が指定されているため、保護を有効にできなかった (エラー ID:95320)

**考えられる原因:** </br>
GRUB 構成ファイル ("/boot/grub/menu.lst"、"/boot/grub/grub.cfg"、"/boot/grub2/grub.cfg"、または "/etc/default/grub") で、**root** パラメーターと **resume** パラメーターの値として、UUID でなく、実際のデバイス名が含まれている可能性があります。 デバイス名は VM のリブートによって変更される可能性があり、フェールオーバー時に VM が同じ名前で起動されないことで問題が発生するため、Site Recovery では UUID を使用する必要があります。 例: </br>


- GRUB ファイル **/boot/grub2/grub.cfg** の次の行。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- GRUB ファイル **/boot/grub/menu.lst** の次の行。
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

上記の太字の文字列がある場合、GRUB では、UUID ではなく、"resume" パラメーターと "root" パラメーターの実際のデバイス名が使用されています。
 
**修正方法:**<br>
デバイス名を対応する UUID に置き換える必要があります。<br>


1. "blkid \<デバイス名>" コマンドを実行して、デバイスの UUID を検出します。 例:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. 次に、"root=UUID=\<UUID>" のような形式で、デバイス名を UUID に置き換えます。 たとえば、"/boot/grub2/grub.cfg"、"/boot/grub2/grub.cfg"、"/etc/default/grub" ファイルで、デバイス名を上記の root および resume パラメーターの UUID に置き換えた場合、ファイル内の行は次のようになります。 <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. 保護を再起動します

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>モビリティ サービスのインストールが完了する際に再起動の警告が返された (エラー ID:95265 および 95266)

Site Recovery モビリティ サービスには多数のコンポーネントがありますが、そのうちの 1 つに、フィルター ドライバーと呼ばれるものがあります。 フィルター ドライバーは、システムの再起動時にのみ、システム メモリ内に読み込まれます。 つまり、フィルター ドライバーの修正は、新しいフィルター ドライバーが読み込まれたときにのみ実現できます。そしてその読み込みは、システムの再起動時にのみ発生します。

なお、これは警告であり、既存のレプリケーションは、新しいエージェントの更新後にも動作することに**注意してください**。 再起動は、新しいフィルター ドライバーの利点を活用したい任意のタイミングで実行できますが、再起動しない場合は古いフィルター ドライバーが動作し続けます。 そのため、更新後に再起動をしなかった場合は、フィルター ドライバーとは別に、**モビリティ サービスの他の機能強化や修正のメリットが実現**されます。 したがって、アップグレード後に毎回再起動を行うことは、推奨はされますが、必須ではありません。 再起動が必須となる場合については、「Azure Site Recovery のサービスの更新情報」の「[モビリティ エージェントのアップグレード後のソース マシンの再起動](https://aka.ms/v2a_asr_reboot)」セクションを参照してください。

> [!TIP]
>メンテナンス期間中のアップグレードのスケジュールに関するベスト プラクティスについては、「Azure Site Recovery のサービスの更新情報」の「[最新の OS/カーネル バージョンのサポート](https://aka.ms/v2a_asr_upgrade_practice)」を参照してください。

## <a name="lvm-support-from-920-version"></a>9\.20 以降のバージョンでの LVM のサポート

9\.20 より前のバージョンでは、データ ディスク用の LVM のみがサポートされていました。 /boot はディスク パーティション上にあり、LVM ボリュームではないことが必要です。

[9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、[LVM 上の OS ディスク](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

## <a name="insufficient-space-errorid-95524"></a>空き領域不足 (ErrorID: 95524)

ソース マシン上にモビリティ エージェントをコピーする場合は、少なくとも 100 MB の空き領域が必要です。 そのため、ソース マシン上に必要な空き領域があることを確認し、操作を再試行してください。

## <a name="vss-installation-failures"></a>VSS インストール エラー

VSS のインストールは、モビリティ エージェントのインストールの一部です。 このサービスは、アプリケーション整合性復旧ポイントを生成するプロセスで使用されます。 VSS のインストール中のエラーは、複数の理由で発生することがあります。 正確なエラーを特定するには、**c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log** を参照します。 次のセクションでは、いくつかの一般的なエラーと解決手順を示します。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS エラー -2147023170 [0x800706BE] - 終了コード 511

この問題は、ウイルス対策ソフトウェアによって Azure Site Recovery サービスの操作がブロックされているときに、最もよく発生します。 この問題を解決するには:

1. [こちら](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)で示されているすべてのフォルダーを除外します。
2. ウイルス対策プロバイダーによって公開されているガイドラインに従って、Windows で DLL の登録をブロック解除します。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS エラー 7 [0x7] - 終了コード 511

これはランタイム エラーであり、VSS をインストールするのに十分なメモリがないことが原因です。 この操作が正常に完了するためには、ディスク領域を増やします。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS エラー -2147023824 [0x80070430] - 終了コード 517

このエラーは、Azure Site Recovery VSS プロバイダー サービスが[削除対象としてマークされている](https://msdn.microsoft.com/library/ms838153.aspx)と発生します。 次のコマンド ラインを実行し、ソース マシンに VSS を手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS エラー -2147023841 [0x8007041F] - 終了コード 512

このエラーは、Azure Site Recovery VSS プロバイダー サービスのデータベースが[ロックされている](https://msdn.microsoft.com/library/ms833798.aspx)場合に発生します。次のコマンド ラインを実行し、ソース マシンに VSS を手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

エラーが発生した場合は、ウイルス対策プログラムやその他のサービスが "開始中" 状態でスタックしていないかどうかを確認します。 データベース サービスのロックが保持されている可能性があります。 VSS プロバイダーのインストールでエラーが発生する原因になります。 "開始中" 状態のサービスが存在しないことを確認したうえで、上記の操作を再試行してください。

### <a name="vss-exit-code-806"></a>VSS 終了コード 806

このエラーは、インストールに使用されているユーザー アカウントに CSScript コマンドを実行するアクセス許可がない場合に発生します。 スクリプトを実行するユーザー アカウントに必要なアクセス許可を提供し、操作を再試行してください。

### <a name="other-vss-errors"></a>その他の VSS エラー

次のコマンド ラインを実行し、ソース マシンに VSS プロバイダー サービスを手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS エラー - 0x8004E00F

このエラーは通常、DCOM の問題が原因でモビリティ エージェントのインストール中に発生します。この場合、DCOM は重大な状態にあります。

次の手順を使用して、エラーの原因を特定します。

**インストール ログを確認する**

1. c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log にあるインストール ログを開きます。
2. 次のエラーがあれば、この問題が発生しています。

    既存のアプリケーションを登録解除しています...カタログ オブジェクトを作成します  アプリケーションのコレクションを取得します 

    ERROR:

    - エラー コード: -2147164145 [0x8004E00F]
    - 終了コード:802

この問題を解決するには:

DCOM の問題を解決するためのサポートを得るために、[Microsoft Windows プラットフォーム チーム](https://aka.ms/Windows_Support)にお問い合わせください。

DCOM の問題が解決されたら、次のコマンドを使用して、Azure Site Recovery VSS プロバイダーを手動で再インストールします。
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
お客様のディザスター リカバリー要件の中でアプリケーションの整合性が特に重要ではない場合は、VSS プロバイダーのインストールをバイパスできます。 

Azure Site Recovery VSS プロバイダーのインストールをバイパスし、インストール後に Azure Site Recovery VSS プロバイダーを手動でインストールするには、次の手順を実行します。

1. モビリティ サービスをインストールします。 
   > [!Note]
   > 
   > インストールは、"インストール後の構成" の手順で失敗します。 
2. VSS のインストールをバイパスするには:
   1. 次の場所にある Azure Site Recovery モビリティ サービス インストール ディレクトリを開きます。
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. インストールが常に成功するように、Azure Site Recovery VSS プロバイダー インストール スクリプトの **nMageVSSProvider_Install** および **InMageVSSProvider_Uninstall.cmd** に次の行を追加して変更します。
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. モビリティ エージェントのインストールを手動で再実行します。 
4. インストールが成功し、次の手順である **[構成]** に移る際に、追加した行を削除します。
5. VSS プロバイダーをインストールするには、管理者としてコマンド プロンプトを開き、次のコマンドを実行します。
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. ASR VSS プロバイダーが Windows サービスのサービスとしてインストールされていることを確認し、コンポーネント サービス MMC を開いて、ASR VSS プロバイダーが一覧にあることを確認します。
10. VSS プロバイダーのインストールがまだ失敗する場合は、CX を操作して、CAPI2 のアクセス許可エラーを解決します。

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>クラスター化されていないマシンでクラスター サービスが有効になっていることが原因で VSS プロバイダーのインストールが失敗する

この問題は、VSS プロバイダーのインストールを妨げる COM+ の問題が原因となっており、ASAzure Site RecoveryR VSS プロバイダーのインストール手順の間に Azure Site Recovery モビリティ エージェントのインストール エラーを引き起こします。
 
### <a name="to-identify-the-issue"></a>問題を特定するには

構成サーバーの C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log にあるログに、次の例外が見つかります。

COM+ は Microsoft 分散トランザクション コーディネーターと通信できませんでした (HRESULT からの例外:0x8004E00F)

この問題を解決するには:

1.  このマシンがクラスター化されていないマシンであること、およびクラスター コンポーネントが使用されていないことを確認します。
3.  コンポーネントが使用されていない場合は、マシンからクラスター コンポーネントを削除します。

## <a name="drivers-are-missing-on-the-source-server"></a>ソース サーバーのドライバーが見つからない

モビリティ エージェントのインストールに失敗した場合は、C:\ProgramData\ASRSetupLogs にあるログを確認し、いくつかのコントロール セットの必須ドライバーの中に不足しているものがないか確認します。
 
この問題を解決するには:
  
1. regedit.msc などのレジストリ エディターを使用して、レジストリを開きます。
2. HKEY_LOCAL_MACHINE\SYSTEM ノードを開きます。
3. この SYSTEM ノードで、コントロール セットを見つけます。
4. 各コントロール セットを開いて、次の Windows ドライバーがあることを確認します。

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
不足しているドライバーを再インストールします。

## <a name="next-steps"></a>次の手順

VMware VM のディザスター リカバリーを設定する[方法を確認する](vmware-azure-tutorial.md)
