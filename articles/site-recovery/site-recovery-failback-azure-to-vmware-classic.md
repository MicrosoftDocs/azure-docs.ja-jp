---
title: "クラシック ポータルで Azure から VMware VM をフェールバックする | Microsoft Docs"
description: "VMware VM および物理サーバーを Azure にフェールオーバーした後に、オンプレミス サイトにフェールバックする方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/06/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 75653b84d6ccbefe7d5230449bea81f498e10a98
ms.openlocfilehash: a898cb9c66a22bb8d19170fdd03d0fc3b4d93000


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>VMware 仮想マシンと物理サーバーをオンプレミス サイトにフェールバックする (クラシック ポータル)
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-failback-azure-to-vmware.md)
> * [Azure クラシック ポータル](site-recovery-failback-azure-to-vmware-classic.md)
> * [Azure クラシック ポータル (レガシ)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

この記事では、Azure からオンプレミス サイトへ Azure Virtual Machines をフェールバックする方法について説明します。 この記事の手順は、 [このチュートリアル](site-recovery-vmware-to-azure-classic.md)を使用して、VMware 仮想マシンまたは Windows/Linux 物理サーバーをオンプレミス サイトから Azure にフェールオーバーした後に、それらをフェールバックする準備ができたときに実行します。

## <a name="overview"></a>概要
この図では、このシナリオのフェールバック アーキテクチャを示します。

このアーキテクチャは、プロセス サーバーがオンプレミスであり、ExpressRoute を使用している場合に使用します。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

このアーキテクチャは、プロセス サーバーが Azure 上にあり、VPN または ExpressRoute 接続のいずれかがある場合に使用します。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

ポートの一覧とフェールバック アーキテクチャの図については、以下の画像をご覧ください。

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

フェールバックのしくみは次のとおりです。

* Azure にフェールオーバー後、オンプレミス サイトへはいくつかの段階でフェールバックします。
  * **第 1 段階**: オンプレミス サイトで実行されている VMware VM へのレプリケートを開始できるように Azure VM を再保護します。 再保護を有効にすると、フェールオーバー保護グループを最初に作成したときに自動的に作成されたフェールバック保護グループに VM は移動されます。 フェールオーバー保護グループを復旧計画に追加した場合、そのフェールバック保護グループも計画に自動的に追加されます。  再保護時、フェールバック方法の計画を指定します。
  * **第 2 段階**: Azure VM がオンプレミス サイトへのレプリケーションを開始したら、フェールオーバーを実行して Azure からフェールバックします。
  * **第 3 段階**: データがフェールバックされたら、フェールバック先のオンプレミスの VM を再保護します。これにより、オンプレミスの VM は Azure へのレプリケートを開始できるようになります。


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>元のまたは別の場所にフェールバックする
VMware VM をフェールオーバーした場合、オンプレミスにまだ存在する場合、同じソース VM にフェールバックできます。 このシナリオでのみ、差分変更のみがフェールバックされます。 以下の点に注意してください。

* 物理サーバーをフェールオーバーした場合、常に新しい VMware VM にフェールバックは実行されます。
  * 物理マシンをフェールバックする前に注意してください
  * 保護されている物理マシンは Azure から VMware にフェールオーバーして戻った場合に、仮想マシンとして返されます
  * フェールバック先のマスター ターゲット サーバーを少なくとも&1; 台と、必要な ESX/ESXi ホストを必ず検出します。
* 元の VM にフェールバックする場合、次が必要です。

  * vCenter サーバーで VM を管理する場合は、マスター ターゲットの ESX ホストが VM のデータストアにアクセスできる必要があります。
  * VM が ESX ホスト上にあっても、vCenter によって管理されていない場合、VM のハード ディスクは MT のホストがアクセスできるデータストア内に存在する必要があります。
  * VM が ESX ホスト上にあり vCenter を使用しない場合、再保護する前に、MT の ESX ホストの検出を完了する必要があります。 これは、物理サーバーにフェールバックする場合も同様です。
  * (オンプレミスに VM がある場合) フェールバックを実行する前に削除するという選択肢もあります。 このようにすると、フェールバックにより、マスター ターゲット ESX ホストと同じホストに新しい VM が作成されます。
* 別の場所にフェールバックすると、オンプレミスのマスター ターゲット サーバーによって使用されるのと同じデータ ストアおよび同じ ESX ホストにデータは復元されます。

## <a name="prerequisites"></a>前提条件
* VMware VM および物理サーバーのフェールバックには、VMware 環境が必要です。 物理サーバーへのフェールバックはサポートされていません。
* フェールバックするには、最初に保護を設定するときに Azure ネットワークを作成する必要があります。 フェールバックには、Azure VM が配置されている Azure ネットワークからオンプレミス サイトへの VPN または ExpressRoute 接続が必要です。
* フェールバックする VM が vCenter サーバーによって管理されている場合、vCenter サーバー上の VM の検出に必要な権限があることを確認する必要があります。 詳細については、[こちら](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)を参照してください。
* VM 上にスナップショットがある場合、再保護は失敗します。 スナップショットまたはディスクを削除します。
* フェールバックを開始する前に、いくつかのコンポーネントを作成する必要があります。
  * **Azure にプロセス サーバーを作成します**。 これが、作成し、フェールバック中に実行し続ける必要のある Azure VM です。 コンピューターはフェールバックの完了後、削除できます。
  * **マスター ターゲット サーバーを作成する**: マスター ターゲット サーバーは、フェールバック データを送受信します。 オンプレミスに作成した管理サーバーには、既定でマスター ターゲット サーバーがインストールされています。 ただし、フェールバック トラフィックの量によっては、フェールバック用に別のマスター ターゲット サーバーを作成する必要があります。
  * Linux 上で実行するマスター ターゲット サーバーを別に作成する場合は、以下の説明に従って、マスター ターゲット サーバーをインストールする前に、Linux VM を設定する必要があります。
* フェールバックを実行するときは、構成サーバーがオンプレミスに存在している必要があります。 フェールバック時に仮想マシンが構成サーバー データベースに存在していないと、フェールバックは失敗します。 サーバーを定期的にバックアップするようスケジュールを設定してください。 万一障害が発生した場合は、フェールバックが正常に機能するよう同じ IP アドレスで復元する必要があります。

## <a name="set-up-the-process-server-in-azure"></a>Azure にプロセス サーバーをセットアップする
Azure の VM がデータをオンプレミスのマスター ターゲット サーバーに戻せるようにするために、Azure にプロセス サーバーをインストールする必要があります。

1. Site Recovery ポータルの **[構成サーバー]** で、新しいプロセス サーバーを追加するよう選択します。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. プロセス サーバー名を指定し、管理者として Azure VM に接続するために使用する名前とパスワードを入力します。 **[構成サーバー]** で、オンプレミスの管理サーバーを選択し、プロセス サーバーをデプロイする Azure ネットワークを指定します。 これは Azure VM が配置されているネットワークである必要があります。 選択したサブネットから一意の IP アドレスを指定し、デプロイメントを開始します。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   プロセス サーバーをデプロイするジョブがトリガーされます。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   Azure にプロセス サーバーをデプロイしたら、指定した資格情報を使用してプロセス サーバーにログインできます。 プロセス サーバーに最初にログインするときに、ダイアログが実行されます。 オンプレミスの管理サーバーの IP アドレスとパスフレーズを入力します。 ポートの設定は既定の 443 のままにします。 オンプレミスの管理サーバーを設定するときに、この設定を明確に変更した場合を除き、既定の 9443 ポートもデータのレプリケーション用にそのままにできます。

   > [!NOTE]
   > サーバーは、 **[VM プロパティ]**には表示されません。 登録先の管理サーバーの **[サーバー]** タブにのみ表示されます。 プロセス サーバーの表示には、10 ～ 15 分かかります。
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>マスター ターゲット サーバーをオンプレミスで設定する
マスター ターゲット サーバーは、フェールバック データを受信します。 マスター ターゲット サーバーは、オンプレミスの管理サーバーに自動的にインストールされますが、大量のデータをフェールバックする場合、マスター ターゲット サーバーを追加で設定した方がよい場合があります。 これを行うには、次の手順を実行します。

> [!NOTE]
> Linux にマスター ターゲット サーバーをインストールするには、次の手順に従います。
>
>

1. Windows にマスター ターゲット サーバーをインストールするには、マスター ターゲット サーバーをインストールする VM の [クイック スタート] ページを開き、Azure Site Recovery 統合セットアップ ウィザードのインストール ファイルをダウンロードします。
2. セットアップを実行し、**[開始する前に]** で **[Add additional process servers to scale out deployment (デプロイをスケールアウトするためにプロセス サーバーを追加する)]** を選択します。
3. [管理サーバーをセットアップ](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)したときと同様にウィザードを完了します。 **[構成サーバーの詳細]** ページで、このマスター ターゲット サーバーの IP アドレスと、VM にアクセスするためのパスフレーズを指定します。

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>マスター ターゲット サーバーとしての Linux VM をセットアップする
Linux VM としてマスター ターゲット サーバーを実行する管理サーバーを設定するには、CentOS 6.6 最小構成オペレーティング システムをインストールし、各 SCSI ハード ディスクの SCSI ID を取得し、いくつかの追加パッケージをインストールし、いくつかのカスタム変更を適用する必要があります。

#### <a name="install-centos-66"></a>CentOS 6.6 をインストールする
1. 管理サーバー VM に CentOS 6.6 最小構成オペレーティング システムをインストールします。 DVD ドライブに ISO イメージを保持し、システムを起動します。 メディア テストをスキップします。次に、言語として日本語を選択し、**[基本ストレージ デバイス]** を選択します。ハード ドライブに重要なデータがないことを確認したら、**[はい]** をクリックしてすべてのデータを破棄します。 管理サーバーのホスト名を入力し、サーバーのネットワーク アダプターを選択します。  **[Editing System (編集システム)]** ダイアログで **[自動的に接続する]** を選択し、静的 IP アドレス設定、ネットワーク設定、DNS 設定を追加します。 管理サーバーにアクセスするためにタイム ゾーンとルート パスワードを指定します。
2. 希望するインストールの種類を求められたら、パーティションとして **[Create Custom Layout (カスタム レイアウトを作成する)]** を選択します。 **[次へ]** select **[Free (Free)]** を選択して、[作成] をクリックします。 **FS Type:** **ext4** を使用して、**/**、**/var/crash**、**/home** の各パーティションを作成します。 **FS Type: swap**を使用してスワップ パーティションを作成します。
3. 既存のデバイスが検出される場合、警告メッセージが表示されます。 **[フォーマット]** をクリックし、パーティション設定を使用してドライブをフォーマットします。 **[Write change to disk (変更をディスクに書き込む)]** をクリックして、パーティションの変更を適用します。
4. **[Install boot loader (ブート ローダーをインストールする)]**  >  **[次へ]** の順に選択して、ルート パーティションにブート ローダーをインストールします。
5. インストールが完了したら、 **[再起動]**をクリックします。

#### <a name="retrieve-the-scsi-ids"></a>SCSI ID を取得する
1. インストール後、VM 内の各 SCSI ハード ディスクの SCSI ID を取得します。 これを実行するには、管理サーバー VM をシャットダウンし、VMware の VM のプロパティで VM エントリを右クリックして、**[設定の編集]**  >  **[オプション]** の順にクリックします。
2. **[詳細設定]**  >  **[General item (全般)]** の順に選択し、**[構成パラメーター]** をクリックします。 このオプションは、コンピューターの実行中は無効になります。 アクティブにするには、コンピューターをシャット ダウンする必要があります。
3. **disk.EnableUUID** 行が存在する場合は、値が **True** (大文字と小文字が区別されます) に設定されていることを確認します。 設定されている場合、キャンセルして、ゲスト オペレーティング システムの起動後に、その OS 内で SCSI コマンドをテストします。
4. この行が存在しない場合は、**[行の追加]** をクリックし、値を **True** に設定して行を追加します。 二重引用符は使用しないでください。

#### <a name="install-additional-packages"></a>追加パッケージをインストールする
いくつかその他のパッケージをダウンロードおよびインストールする必要があります。

1. マスター ターゲット サーバーがインターネットに接続されていることを確認します。
2. **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**コマンドを実行して、CentOS リポジトリから 15 個のパッケージをダウンロードし、インストールします。
3. 保護しているソース マシンが、ルートまたはブート デバイス用に Linux wit Reiser または XFS ファイル システムを実行している場合、次のようなパッケージを追加でインストールする必要があります。

   * # <a name="cd-usrlocal"></a>cd /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>カスタム変更を適用する
インストール後の手順と、パッケージのインストールが完了したら、次を実行して独自の変更を適用します。

1. RHEL 6-64 ユニファイド エージェント バイナリを、VM にコピーします。 次のコマンドを実行して、バイナリを解凍します: **tar –zxvf <file name>**
2. 次のコマンドを実行して、アクセス許可を付与します: **# chmod 755 ./ApplyCustomChanges.sh**
3. 次のスクリプトを実行します: **# ./ApplyCustomChanges.sh**。 このスクリプトは一度だけ実行します。 スクリプトが正常に実行されたら、サーバーを再起動します。

## <a name="run-the-failback"></a>フェールバックを実行する
### <a name="reprotect-the-azure-vms"></a>Azure VM を再保護する
1. Site Recovery ポータルの **[コンピューター]** タブで、フェールオーバーされた VM を選択し **[再保護]** をクリックします。
2. **[マスター ターゲット サーバー]** と **[プロセス サーバー]** で、オンプレミスのマスター ターゲット サーバーと Azure VM プロセス サーバーを選択します。
3. VM に接続するために構成したアカウントを選択します。
4. 保護グループのフェールバック バージョンを選択します。 たとえば、VM が PG1 で保護されている場合、PG1_Failback を選択する必要があります。
5. 別の場所に復元する場合は、マスター ターゲット サーバー用に構成したリテンション ドライブとデータ ストアを選択します。 オンプレミス サイトにフェールバックした場合、フェールバック保護計画の VMware VM はマスター ターゲット サーバーと同じデータ ストアを使用します。 レプリカの Azure VM を同じオンプレミスの VM に復元したい場合、オンプレミスの VM は、マスター ターゲット サーバーと同じデータ ストアに存在している必要があります。 オンプレミスに VM がない場合、再保護時に新しいものが作成されます。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. **[OK]** をクリックして再保護を開始すると、Azure からオンプレミス サイトに VM をレプリケートするジョブが開始されます。 進行状況は **[ジョブ]** タブで追跡できます。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>オンプレミス サイトでフェールオーバーを実行する
再保護後、VM は、その保護グループのフェールバック バージョンに移動され、存在する場合、Azure へのフェールオーバーに使用した復旧計画に自動的に追加されます。

1. **[復旧計画]** ページでフェールバック グループを含む復旧計画を選択し、**[フェールオーバー]** > **[計画されていないフェールオーバー]** の順にクリックします。
2. **[フェールオーバーの確認]** で、フェールオーバーの方向 (Azure へ) を確認し、フェールオーバーに使用する (最新の) 復旧ポイントを選択します。 レプリケーション プロパティの構成時に **マルチ VM** を有効にした場合は、最新のアプリケーションまたはクラッシュ整合性復旧ポイントに復旧できます。 チェック マークをクリックしてフェールオーバーを開始します。
3. フェールオーバー中に、Site Recovery によって Azure VM がシャット ダウンされます。 フェールバックが期待どおりに完了したことを確認したら、Azure VM が期待どおりにシャット ダウンされたこと確認します。

### <a name="reprotect-the--on-premises-site"></a>オンプレミス サイトを再保護する
フェールバックが完了したら、データはオンプレミス サイトに戻りますが、保護はされていません。 Azure に再度レプリケートを開始するには、次を実行します。

1. Site Recovery ポータルの **[コンピューター]** タブで、フェールバックされた VM を選択し、**[再保護]** をクリックします。
2. Azure へのレプリケーションが期待どおりに実行されていることを確認したら、Azure でフェールバックされた (現在実行されていない) Azure VM を削除できます。

### <a name="common-issues-in-failback"></a>フェールバックに関する一般的な問題
1. 読み取り専用ユーザー vCenter の検出を実行し、仮想マシンを保護すると、適切に動作し、フェールオーバーが実行されます。 再保護の場合は、データストアを検出できないため失敗します。 症状としては、再保護中はデータストアの一覧が表示されません。 これを解決するには、適切な権限を持つアカウントを使用して vCenter 資格情報を更新し、ジョブを再試行します。 [詳細については、こちらを参照してください。](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Linux VM をフェールバックし、オンプレミスで実行すると、ネットワーク マネージャーのパッケージがコンピューターからアンインストールされます。 これは、Azure で VM が復旧したときに、ネットワーク マネージャーのパッケージが削除されるためです。
3. VM が静的 IP アドレスで構成されている場合、Azure にフェールオーバーされると、IP アドレスは DHCP 経由で取得されます。 オンプレミスにフェールオーバーで戻るとき、VM は引き続き DHCP を使用して、IP アドレスを取得します。 このため、必要に応じて手動でコンピューターにログインし、IP アドレスを静的アドレスに設定し直す必要があります。
4. ESXi 5.5 Free エディションまたは vSphere 6 Free エディションのいずれかを使用すると、フェールオーバーは成功しますが、フェールバックが失敗します。 いずれかの評価ライセンスにアップグレードして、フェールバックを有効にする必要があります。

## <a name="failing-back-with-expressroute"></a>ExpressRoute を使用したフェールバック
VPN 接続または Azure ExpressRoute を使用してフェールバックできます。 ExpressRoute を使用する場合、次に注意してください。

* ExpressRoute が、ソース マシンがフェールオーバーする、フェールオーバーの発生後に Azure VM が配置される Azure VM 仮想ネットワーク上に設定されている必要があります。
* データは、パブリック エンドポイントの Azure ストレージ アカウントに複製されます。 ExpressRoute を使用するには、Site Recovery のレプリケーション用のターゲット データ センターを使用して ExpressRoute でパブリック ピアリングを設定する必要があります。



<!--HONumber=Jan17_HO5-->


