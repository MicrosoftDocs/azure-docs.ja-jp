---
title: "Azure からオンプレミスへのフェールオーバーに必要な Linux マスター ターゲット サーバーをインストールする方法 | Microsoft Docs"
description: "Linux 仮想マシンを再保護する前に、Linux マスター ターゲット サーバーが必要になります。 そのインストール方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: ef4324ebf3c24cdf2ebed58e4938f401b66b9c95
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Linux マスター ターゲット サーバーをインストールする方法
仮想マシンをフェールオーバーした後、仮想マシンをオンプレミス サイトにフェールバックできます。 フェールバックするには、Azure からオンプレミス サイトへの仮想マシンを再保護する必要があります。 このプロセスには、トラフィックを受信するオンプレミス マスター ターゲット サーバーが必要です。 保護された仮想マシンが Windows 仮想マシンである場合、Windows マスター ターゲットが必要です。 Linux 仮想マシンには、Linux マスター ターゲットが必要になります。 ここでは、Linux のマスター ターゲットを作成してインストールする方法について説明しています。

## <a name="overview"></a>概要
この記事では、Linux のマスター ターゲットをインストールするために必要な情報と手順を紹介しています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件

* マスター ターゲットのデプロイ先となるホストを正しく選ぶためには、フェールバックの切り替え先の判断が必要となります。つまり既存のオンプレミスの仮想マシンにフェールバックするか、オンプレミスの仮想マシンが削除されているために新しい仮想マシンにフェールバックするかを判断します。
    * 既存の仮想マシンでは、マスター ターゲットのホストには、仮想マシンのデータストアへのアクセスが必要です。
    * オンプレミスの仮想マシンが存在しない場合、マスター ターゲットとして同じホスト上にフェールバックの仮想マシンが作成されます。 マスター ターゲットは任意の ESXi ホストにインストールすることができます。
* マスター ターゲットは、プロセス サーバーおよび構成サーバーと通信できるネットワーク上に存在する必要があります。
* マスター ターゲットのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 たとえば、構成サーバーのバージョンが 9.4 である場合、マスター ターゲットのバージョンは 9.4 または 9.3 にすることができますが、9.5 にすることはできません。
* マスター ターゲットは、VMware 仮想マシンにのみすることができますが、物理サーバーにすることはできません。


## <a name="steps-to-deploy-the-master-target-server"></a>マスター ターゲット サーバーをデプロイする手順

### <a name="install-centos-66-minimal"></a>CentOS 6.6 最小構成をインストールする

64 ビット CentOS 6.6 オペレーティング システムをインストールするには、次の手順を使用します。

1. 以下のリンクから最も近いミラーを選択して CentOS 6.6 最小構成 64 ビット ISO をダウンロードします。

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    CentOS 6.6 最小構成 64 ビット ISO を DVD ドライブに配置し、システムを起動します。

    ![[Welcome to CentoOS 6.6] ダイアログ ボックス](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. **[スキップ]** をクリックしてメディア テストのプロセスを無視します。

    ![[スキップ] を選択してメディア テストのプロセスを無視する](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. インストールのウェルカム画面で **[次へ]** をクリックします。

    ![インストールのウェルカム画面の [次へ] ボタン](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 優先言語として **[英語]** を選択し、**[次へ]** をクリックします。

    ![言語を選択する](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. キーボード レイアウトとして **[英語 (米国)]** を選択し、**[次へ]** をクリックします。

    ![[英語]キーボード レイアウトを選択する](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. **[Basic storage Devices (基本ストレージ デバイス)]** を選択し、**[次へ]** をクリックします。

    ![ストレージ デバイスを選択する](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. ハード ドライブ内の既存のデータが削除されることを示す警告メッセージが表示されます。 ハード ドライブに重要なデータが保存されていないことを確認し、**[Yes, discard any data (はい、すべてのデータを破棄します)]** をクリックします。

    ![続行する場合のデータの削除に関する警告](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. **[ホスト名]** ボックスにサーバーのホスト名を入力し、**[ネットワークの構成]** をクリックします。 **[ネットワーク接続]** ダイアログ ボックスでネットワーク インターフェイスを選択し、**[編集]** ボタンをクリックして IPV4 設定を構成します。

    ![ホスト名を選択して IPV4 を構成する](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. **[Editing System eth0 (System eth0 の編集)]** ダイアログ ボックスで、**[自動的に接続する]** チェック ボックスをオンします。 **[IPv4 設定]** タブで、**[方法]** の **[手動]** を選択し、**[追加]** ボタンをクリックします。 **静的 IP**、**ネットマスク**、**ゲートウェイ**、および **DNS サーバー**の詳細を指定します。 **[適用]** をクリックして詳細情報を保存します。

    ![ネットワーク構成の設定](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. タイム ゾーンを選択し、**[次へ]** をクリックします。

    ![タイム ゾーンを選択する](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. **[ルート パスワード]** にパスワードを入力して確認し、**[次へ]** をクリックします。

    ![パスワードを追加する](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. **[カスタム レイアウトの作成]** を選択し、**[次へ]** をクリックします。

    ![インストールの種類を選択する](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. **空き**パーティションを選択し、ファイル システムの種類として **ext4** を指定して **/**、**/var/crash**、および **/home** の各パーティションを作成するために **[作成]** をクリックします。 ファイル システムの種類として **swap** を指定して、**スワップ パーティション**を作成します。 パーティションのサイズを割り当てるには、次の表に示すサイズの割り当て式に従います。

    > [!NOTE]
    > Linux マスター ターゲット サーバーでは、Logical Volume Manager (LVM) をルートまたはリテンション期間ストレージ スペースとして使用しないでください。 Linux マスター ターゲットは、既定で、LVM パーティションおよびディスク検出を回避するように構成されます。

    ![パーティション名、パーティション サイズ、およびファイル システム タイプの表](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. パーティションの作成後、**[次へ]** をクリックします。

    ![パーティションの選択された値を示すダイアログ ボックス](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 既存のデバイスが見つかった場合、フォーマットによってデータが消去されることを警告するメッセージが表示されます。 **[フォーマット]** をクリックし、最新のパーティション テーブルを使用してハード ドライブをフォーマットします。

    ![[フォーマット] ボタンをクリックし、ディスクをフォーマットする](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. **[変更をディスクに書き込む]** をクリックして、パーティションの変更をディスクに適用します。

    ![[変更をディスクに書き込む] をクリックする](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. **[ブート ローダーをインストールする]** オプションを確認して、**[次へ]** をクリックしてルート パーティションにブート ローダーをインストールします。

    ![ルート パーティションにブート ローダーをインストールする](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. インストール プロセスが始まります。 進行状況を監視できます。

    ![インストールの進行状況を示すダイアログ ボックス](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. インストールが正常に完了すると次の画面が表示されます。 **[再起動]** をクリックします。

    ![正常に完了したインストール画面](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>インストール後の手順
次に、マスター ターゲット サーバーとして構成するマシンを準備します。

Linux 仮想マシンの各 SCSI ハード ディスクの ID を取得するには、**disk.EnableUUID = TRUE** パラメーターを有効にする必要があります。

このパラメーターを有効にするには、次の手順を使用します。

1. 仮想マシンをシャットダウンします。

2. 左ウィンドウで仮想マシンのエントリを右クリックし、**[設定の編集]** を選択します。

3. **[Options]** タブをクリックします。

4. 左ウィンドウで **[詳細設定] &gt; [全般]** を選択し、右側に表示される **[構成パラメーター]** ボタンをクリックします。

    ![[Options] タブ](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    **[構成パラメーター]** オプションは、マシンの実行時には使用できません。 このタブをアクティブにするには、仮想マシンをシャットダウンします。

5. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。

    - その値が存在していて **False** に設定されている場合、**True** に値を変更します (値の大文字小文字は区別されません)。

    - その値が存在していて **True** に設定されている場合、**[キャンセル]** をクリックします。

    - そのような値が存在しない場合、**[行の追加]** をクリックします。

    - **[名前]** 列に **disk.EnableUUID** を追加し、その値を **TRUE** に設定します。

    ![[disk.EnableUUID] が存在するかどうかを確認する](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>その他のパッケージをダウンロードおよびインストールする

> [!NOTE]
> 追加パッケージをダウンロードしてインストールするには、インターネットに接続できることを確認してください。 インターネットに接続できない場合は、これらの RPM パッケージを手動で検索してインストールする必要があります。

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

前のコマンドは、次の 15 パッケージを CentOS 6.6 リポジトリからダウンロードしてインストールします。 インターネットにアクセスできない場合は、次の RPM パッケージをダウンロードする必要があります。


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>特定のオペレーティング システムに必要な追加パッケージのインストール

> [!NOTE]
> ソースの保護されたマシンで、ルートまたはブート デバイスとして ReiserFS または XFS のファイル システムが使用されている場合、保護する前に、次の追加パッケージを Linux マスター ターゲットにダウンロードしてインストールする必要があります。


***ReiserFS (Suse11SP3 で使用されている場合。ReiserFS は Suse11SP3 の既定のファイル システムではありません)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL、CentOS 7 以降)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath
```
これは、マスター ターゲットでマルチパス パッケージを有効にするために必要です。

### <a name="get-the-installer-for-setup"></a>セットアップに使用するインストーラーの入手

マスター ターゲットからインターネットに接続できる場合は、以下の手順でインストーラーをダウンロードできます。 インターネットに接続できない場合は、プロセス サーバーからインストーラーをコピーしてインストールできます。

#### <a name="download-the-master-target-installation-packages"></a>マスター ターゲットのインストール パッケージをダウンロードする

[最新の Linux マスター ターゲット インストール ビットをダウンロードします](https://aka.ms/latestlinuxmobsvc)。

Linux を使用してこれをダウンロードするには、次のように入力します。

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

ダウンロードして、必ずホーム ディレクトリでインストーラーを解凍してください。 /usr/Local に解凍すると、インストールは失敗します。


#### <a name="access-the-installer-from-the-process-server"></a>プロセス サーバーからインストーラーにアクセスする

1. プロセス サーバーの C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository に移動します。

2. プロセス サーバーから必要なインストーラー ファイルをコピーし、自分の home ディレクトリに latestlinuxmobsvc.tar.gz という名前で保存します。


### <a name="apply-custom-configuration-changes"></a>カスタム構成変更を適用する

カスタム構成変更を適用するには、次の手順を使用します。


1. 次のコマンドを実行して、バイナリを解凍します。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![実行されるコマンドのスクリーンショット](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 次のコマンドを実行して、アクセス許可を付与します。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 次のコマンドを実行して、スクリプトを実行します。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> スクリプトは、サーバー上で 1 回のみ実行してください。 サーバーをシャットダウンします。 以降の手順で指定されているディスクを追加した後、サーバーを再起動してください。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>リテンション ディスクを Linux マスター ターゲット仮想マシンに追加する

リテンション ディスクを作成するには、次の手順を使用します。

1. 新しい **1 TB** ディスクを Linux マスター ターゲット 仮想マシンに接続し、マシンを**起動**します。

2. **multipath -ll** コマンドを使用し、リテンション ディスクのマルチパス ID を確認します。

    ```
    multipath -ll
    ```

    ![リテンション ディスクのマルチパス ID](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. ドライブをフォーマットし、新しいドライブにファイル システムを作成します。

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![ドライブにファイル システムを作成する](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. ファイル システムを作成したら、リテンション ディスクをマウントします。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![リテンション ディスクをマウントする](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. **fstab** エントリを作成し、毎回起動中にリテンション ドライブをマウントします。
    ```
    vi /etc/fstab
    ```
    **Ins** キーを押してファイルの編集を開始します。 新しい行を作成し、次のテキストを挿入します。 前述のコマンドでハイライトされているマルチパス ID に基づいてディスクのマルチパス ID を編集します。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    **Esc** キーを押して「**:wq**」(write and quit) と入力し、エディター ウィンドウを閉じます。

### <a name="install-the-master-target"></a>マスター ターゲットをインストールする

> [!IMPORTANT]
> マスター ターゲット サーバーのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 この条件が満たされない場合、再保護は成功しますが、レプリケーションで失敗します。


> [!NOTE]
> マスター ターゲット サーバーをインストールする前に、仮想マシンの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。

1. 構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase からパスフレーズをコピーし、次のコマンドを実行して同じローカル ディレクトリの passphrase.txt に保存します。

    ```
    echo <passphrase> >passphrase.txt
    ```
    例: echo itUx70I47uxDuUVY >passphrase.txt

2. 構成サーバーの IP アドレスをメモします。 次の手順で必要になります。

3. 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    例: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    スクリプトが完了するまで待機します。 マスター ターゲットは、正常に登録されていれば、ポータルの [Site Recovery インフラストラクチャ] ページに表示されます。

#### <a name="install-the-master-target-by-using-interactive-install"></a>対話型インストールによりマスター ターゲットをインストールする

1. 次のコマンドを実行してマスター ターゲットをインストールします。 エージェント ロールとして **マスター ターゲット** を選択します。

    ```
    ./install
    ```

2. インストールの既定の場所を選択します。続行するには、**Enter** キーを押します。

    ![マスター ターゲットのインストールの既定の場所を選択する](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. 構成する**グローバル**設定を選択します。

    ![グローバル設定を構成する](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. 構成サーバーの IP アドレスを指定します。

5. 構成サーバーのポートを 443 として指定します。

    ![構成サーバーの IP アドレスおよびポートを指定する](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. 構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase から構成サーバーのパスフレーズをコピーし、**[パスフレーズ]** ボックスに貼り付けます。 テキストを貼り付けた後も、ボックスには何も表示されません。

7. メニューの **[Quit (終了)]** に移動します。

8. インストールと登録を完了します。

### <a name="install-vmware-tools-on-the-master-target-server"></a>マスター ターゲット サーバーに VMware ツールをインストールする

VMware ツールは、データストアを検出できるように、マスター ターゲット上にインストールする必要があります。 ツールをインストールしていない場合は、再保護画面にデータストアが表示されません。 VMware ツールのインストール後、再起動する必要があります。

## <a name="next-steps"></a>次のステップ
マスター ターゲットは、インストールと登録が完了すると、**[Site Recovery インフラストラクチャ]** の **[マスター ターゲット]** セクション (構成サーバーの概要の下) に表示されます。

[再保護](site-recovery-how-to-reprotect.md)とフェールバックの作業に進んでください。

## <a name="common-issues"></a>一般的な問題

* マスター ターゲットをはじめとする管理コンポーネントでは一切 Storage vMotion を有効にしないでください。 再保護に成功した後でマスター ターゲットが移動されると、仮想マシン ディスク (VMDK) を切断できず、フェールバックが失敗します。
* マスター ターゲットの仮想マシンには、スナップショットが一切存在していないことが必要です。 スナップショットが存在するとフェールバックが失敗します。
* NIC の構成がカスタマイズされていることが原因で、起動中にネットワーク インターフェイスが無効となり、マスター ターゲット エージェントを初期化できない場合があります。 以下のプロパティが正しく設定されていることを確認してください。 イーサネット カード ファイル /etc/sysconfig/network-scripts/ifcfg-eth* で次のプロパティを確認します。      * BOOTPROTO=dhcp * ONBOOT=yes

