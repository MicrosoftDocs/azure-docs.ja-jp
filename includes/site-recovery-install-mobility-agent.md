---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>モビリティ サービスをインストールする
仮想マシンと物理サーバーの保護を有効にするための最初の手順は、モビリティ サービスのインストールです。 これは、次のような何とおりかの方法で実行できます。

* **プロセス サーバー プッシュ**: マシンでレプリケーションを有効にしたら、プロセス サーバーからモビリティ サービス コンポーネントをプッシュしてインストールします。 
マシンで最新バージョンのコンポーネントを既に実行している場合、プッシュ インストールは実行されないことに*注意してください*。
* **エンタープライズ プッシュ**: WSUS、System Center Configuration Manager、[Azure Automation および Desired State configuration](site-recovery-automate-mobility-service-install.md) などのエンタープライズ プッシュ プロセスを使用してコンポーネントを自動的にインストールします。 これを実行する前に、構成サーバーをセットアップします。
* **手動インストール**: レプリケートする各マシンにコンポーネントを手動でインストールします。 これを実行する前に、構成サーバーをセットアップします。

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Windows マシンで自動プッシュを準備する
プロセス サーバーによってモビリティ サービスが自動的にインストールされるように Windows マシンを準備する方法について説明します。

1. プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。 このアカウントには管理者特権 (ローカルまたはドメイン) が必要です。このアカウントはプッシュ インストールにのみ使用されます。

   > [!NOTE]
   > ドメイン アカウントを使用していない場合、ローカル マシンで "リモート ユーザー アクセス” コントロールを無効にする必要があります。 無効にするには、レジストリ エディターで、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System の下に、値 1 を指定した DWORD エントリの LocalAccountTokenFilterPolicy を追加します。 CLI からレジストリ エントリを追加するには、「 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**」と入力します。
   >
   >
2. 保護対象のマシンの Windows ファイアウォールで、 **[ファイアウォールを介したアプリまたは機能を許可する]**を選択します。 **[ファイルとプリンターの共有]** と **[Windows Management Instrumentation]** を有効にします。 マシンがドメインに属している場合は、GPO を使用してファイアウォール設定を構成できます。

   ![ファイアウォールの設定](./media/site-recovery-vmware-to-azure/mobility1.png)
3. 作成したアカウントを追加します。

   * **cspsconfigtool**を開きます。 これはデスクトップにショートカットがあり、[インストール場所]\home\svsystems\bin フォルダーに保存されています。
   * **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
   * 作成したアカウントを追加します。 アカウントの追加後、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Linux サーバーの自動プッシュを準備する
1. 「 [保護対象のマシンの前提条件](#protected-machine-prerequisites)」に示すように、保護対象の Linux マシンがサポートされていることを確認します。 Linux マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。
2. プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。 このアカウントは、ソース Linux サーバーのルート ユーザーである必要があり、プッシュ インストールにのみ使用されます。

   * **cspsconfigtool**を開きます。 これはデスクトップにショートカットがあり、[インストール場所]\home\svsystems\bin フォルダーに保存されています。
   * **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
   * 作成したアカウントを追加します。 アカウントの追加後、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。
3. ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
4. レプリケートするマシンに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
5. SSH がポート 22 で有効であり実行中であることを確認します。
6. 以下の手順で、sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にします。

   * root としてログインします。
   * /etc/ssh/sshd_config ファイルで、**PasswordAuthentication** で始まる行を見つけます。
   * この行のコメントを解除し、値 **no** を **yes** に変更します。
   * **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>モビリティ サービスを手動でインストールする
インストーラーは、構成サーバーの **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository** にあります。

| ソース オペレーティング システム | モビリティ サービスのインストール ファイル |
| --- | --- |
| Windows Server (64 ビットのみ) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4、6.5、6.6 (64 ビットのみ) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Windows Server にモビリティ サービスをインストールする
1. 関連するインストーラーをダウンロードして実行します。
2. **[開始する前に]** で **[モビリティ サービス]** を選択します。

    ![モビリティ サービス](./media/site-recovery-vmware-to-azure/mobility3.png)
3. **[Configuration Server Details] (構成サーバーの詳細)** で、構成サーバーの IP アドレスと、統合セットアップの実行時に生成されたパスフレーズを指定します。 パスフレーズを取得するには、構成サーバーで「**<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v**を実行します。

    ![モビリティ サービス](./media/site-recovery-vmware-to-azure/mobility6.png)
4. **[インストール場所]** で既定の設定をそのまま使用し、**[次へ]** をクリックしてインストールを開始します。
5. **[インストールの進行状況]** でインストールを監視し、要求されたらマシンを再起動します。 サービスのインストール後、ポータルで状態が更新されるまでに約 15 分かかることがあります。

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>コマンド プロンプトを使用して Windows サーバーにモビリティ サービスをインストールする
1. 保護するサーバー上のローカル フォルダー (C:\Temp など) にインストーラーをコピーします。 インストーラーは、構成サーバーの **[インストール場所]\home\svsystems\pushinstallsvc\repository** にあります。 Windows オペレーティング システムのパッケージには、Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe のような名前が付けられています。
2. このファイルの名前を MobilitySvcInstaller.exe に変更します。
3. 次のコマンドを実行して MSI インストーラーを抽出します。

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "構成サーバーの IP アドレス" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>コマンドライン構文の全文
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**パラメーター**

* **/Role:** 必須。 モビリティ サービスをインストールするかどうかを指定します。 入力値 Agent|MasterTarget
* **/InstallLocation:** 必須。 サービスをインストールする場所を指定します。
* **/PassphraseFilePath:** 必須。 構成サーバーのパスフレーズ。
* **/LogFilePath:** 必須。 インストールのログ ファイルが作成される場所です。

#### <a name="uninstall-the-mobility-service-manually"></a>モビリティ サービスを手動でアンインストールする
モビリティ サービスは、コントロール パネルの [プログラムの追加と削除] を使用するか、コマンドラインで「MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}」を実行してアンインストールできます。

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Linux サーバーでモビリティ サービスをインストールする
1. 上記の表を基に適切な tar アーカイブを、レプリケートする Linux マシンにコピーします。
2. シェル プログラムを開き、「`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`」を実行して zip 形式の tar アーカイブをローカル パスに抽出します。
3. tar アーカイブの内容を抽出したローカル ディレクトリに passphrase.txt ファイルを作成します。 このファイルを作成するには、構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase からパスフレーズをコピーし、シェルで「*`echo <passphrase> >passphrase.txt`*を実行して passphrase.txt に保存します。
4. *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*を実行して、モビリティ サービスをインストールします。
5. 構成サーバーの内部 IP アドレスを指定し、ポート 443 が選択されていることを確認します。 サービスのインストール後、ポータルで状態が更新されるまでに約 15 分かかることがあります。

**コマンドラインからインストールする場合**:

構成サーバーの C:\Program Files (x86)\InMage Systems\private\connection からパスフレーズをコピーし、"passphrase.txt" というファイル名で構成サーバーに保存します。 次のコマンドを実行します。 この例では、構成サーバーの IP アドレスは 104.40.75.37、HTTPS ポートは 443 です。


運用サーバーにインストールするには:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

マスター ターゲット サーバーにインストールするには:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




