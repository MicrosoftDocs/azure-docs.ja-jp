---
title: "Azure Site Recovery を使用して StorSimple ファイル共有 DR を自動化する | Microsoft Docs"
description: "Microsoft Azure StorSimple ストレージでホストされているファイル共有用の障害復旧ソリューションを作成するための手順とベスト プラクティスについて説明します。"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: a0817ac21afb83745ec2cc213fc6029fa395d479


---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>StorSimple でホストされたファイル共有向けの Azure Site Recovery を使用した自動障害復旧ソリューション
## <a name="overview"></a>概要
Microsoft Azure StorSimple は、ファイル共有でよく使用される複雑な非構造データに対応できるハイブリッド クラウド ストレージ ソリューションです。 StorSimple では、クラウド ストレージがオンプレミス ソリューションの拡張機能として使用され、オンプレミス ストレージとクラウド ストレージをまたがってデータが自動的に階層化されます。 ローカル スナップショットおよびクラウド スナップショットを使用した統合型データ保護により、ストレージ インフラストラクチャを拡大する必要がなくなります。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) は、仮想マシンのレプリケーション、フェールオーバー、復旧を調整するといった障害復旧 (DR) 機能を提供する Azure ベースのサービスです。 Azure Site Recovery は、仮想マシンとアプリケーションの一貫したレプリケート、保護、およびプライベート/パブリック クラウドまたはホスト側のクラウドへのシームレスなフェールオーバーを実行するために、多くのレプリケーション テクノロジをサポートしています。

Azure Site Recovery、仮想マシンのレプリケーション、および StorSimple クラウド スナップショットの機能を使用することで、ファイル サーバー環境全体を保護することができます。 障害が発生した場合は、ワンクリックするだけで、数分以内に Azure 内でオンラインでファイル共有ができるようになります。

このドキュメントでは、StorSimple ストレージでホストされているファイル共有の障害復旧ソリューションの作成方法と、ワンクリック復旧計画を使用している計画済み、計画外、テスト フェールオーバーの実行方法を詳しく説明します。 つまり、障害発生時に StorSimple のフェールオーバーが有効になるように、Azure Site Recovery コンテナーで復旧計画を変更する方法を説明します。 さらに、サポートされている構成と前提条件についても説明します。 このドキュメントは、Azure Site Recovery と StorSimple のアーキテクチャの基礎知識をお持ちの方を対象としています。

## <a name="supported-azure-site-recovery-deployment-options"></a>サポートされている Azure Site Recovery のデプロイ オプション
まず Hyper-V または VMware 上で実行される物理サーバーあるいは仮想マシン (VM) としてファイル サーバーをデプロイしてから、StorSimple ストレージの分割されたボリュームでファイル共有を作成できます。 Azure Site Recovery を使用すると、物理および仮想のデプロイをセカンダリ サイトまたは Azure のいずれかで保護できます。 このドキュメントでは、Azure を Hyper-V でホストされるファイル サーバー VM の復旧サイトとして使用し、StorSimple ストレージでファイル共有をしている場合の障害復旧ソリューションを詳しく説明します。 ファイル サーバー が VMware VM または物理マシン上にある場合の他のシナリオでも、障害復旧ソリューションを同様に実装できます。

## <a name="prerequisites"></a>前提条件
StorSimple ストレージでホストされているファイル共有向けに Azure Site Recovery を使用するワンクリック障害復旧ソリューションを実装する場合、前提条件は次のとおりです。

* Hyper-V、VMware、または物理マシンでホストされているオンプレミスの Windows Server 2012 R2 ファイル サーバー VM
* Azure StorSimple Manager に登録されたオンプレミスの StorSimple ストレージ デバイス
* Azure StorSimple Manager で作成した StorSimple Cloud Appliance (シャットダウン状態でも保持されます)
* StorSimple ストレージ デバイスで構成されたボリュームでホストされているファイル共有
* [Azure Site Recovery Services コンテナー](../site-recovery/site-recovery-vmm-to-vmm.md) 

さらに、Azure が復旧サイトに設定されている場合は、 [Azure Virtual Machines 準備状況評価ツール](http://azure.microsoft.com/downloads/vm-readiness-assessment/) を VM で実行し、Azure VM および Azure Site Recovery サービスと互換性があることを確認してください。

(コストを引き上げる可能性のある) 待機時間が発生しないようにするには、StorSimple Cloud Appliance、オートメーション アカウント、およびストレージ アカウントを同じリージョンに作成してください。

## <a name="enable-dr-for-storsimple-file-shares"></a>StorSimple ファイル共有における障害復旧を有効にする方法
完全なレプリケーションと復旧を有効にするには、オンプレミス環境内の各コンポーネントを保護する必要があります。 このセクションでは以下の手順を説明します。

* Active Directory と DNS レプリケーションの設定方法 (オプション)
* Azure Site Recovery を使用してファイル サーバー VM の保護を有効にする方法
* StorSimple ボリュームの保護を有効にする方法
* ネットワークの構成方法

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Active Directory と DNS レプリケーションの設定方法 (オプション)
Active Directory と DNS を実行するコンピューターを保護して障害復旧サイトで使用できるようにするには、これらのコンピューターを明示的に保護する必要があります (フェールオーバー後に、ファイル サーバーへの認証によるアクセスを可能にするためです)。 2 つの方法をお勧めしますので、お客様のオンプレミス環境の複雑さに応じて選択してください。

#### <a name="option-1"></a>方法 1
お客様のアプリケーションの数が少ない場合は、オンプレミスのサイト全体に&1; つのドメイン コントローラーを使用して、サイト全体をフェールオーバーしてから、Azure Site Recovery レプリケーションを使用してドメイン コントローラー マシンをセカンダリ サイトにレプリケートすることをお勧めします (この方法は、サイト間、サイトと Azure 間の両方に適用できます)。

#### <a name="option-2"></a>方法 2
お客様のアプリケーションの数が多く、Active Directory フォレストを実行している場合は、一度に複数のアプリケーションをフェールオーバーしてから、障害復旧サイトで追加のドメイン コントローラーを (セカンダリ サイトまたは Azure のいずれかにおいて) 設定することをお勧めします。

障害復旧サイト上でドメイン コントローラーを使用できるようにする手順については、「 [Azure Site Recovery で Active Directory と DNS を保護する](../site-recovery/site-recovery-active-directory.md) 」を参照してください。 これ以降のドキュメントでは、ドメイン コントローラーが 障害復旧サイトで使用可能になっていることを前提とします。

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery を使用してファイル サーバー VM の保護を有効にする方法
この手順では、オンプレミスのファイル サーバー環境を準備し、Azure Site Recovery コンテナーを作成および準備してから、VM のファイル保護を有効にする必要があります。

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>オンプレミスのファイル サーバー環境を準備するには
1. **[ユーザー アカウント制御]** を **[通知しない]** に設定します。 この設定が必要なのは、Azure Site Recovery によってフェールオーバーされた後に、Azure  オートメーション スクリプトを使用して iSCSI ターゲットに接続するためです。

   1. Windows キー + Q キーを押して、 **UAC**を検索します。
   2. **[ユーザー アカウント制御設定の変更]**の設定を選択します。
   3. バーを下にドラッグして **[通知しない]**に設定します。
   4. **[OK]** をクリックして、入力を求められたら **[はい]** を選択します。

      ![](./media/storsimple-dr-using-asr/image1.png)
2. 各ファイル サーバー VM に VM エージェントをインストールします。 このインストールは、フェールオーバーされた VM で Azure オートメーション スクリプトを実行するために必要です。

   1. `C:\\Users\\<username>\\Downloads` に[エージェントをダウンロード](http://aka.ms/vmagentwin)します。
   2. 管理者モード (管理者として実行) で Windows PowerShell を開き、次のコマンドを入力してダウンロード先に移動します。

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > バージョンによって、ファイル名が異なる場合があります。
      >
      >
3. **[次へ]**をクリックします。
4. **[契約の条項]** をクリックして、**[次へ]** をクリックします。
5. **[完了]**をクリックします。
6. StorSimple ストレージで分割されたボリュームを使用して、ファイル共有を作成します。 詳細については、「 [StorSimple Manager サービスを使用してボリュームを管理する](storsimple-manage-volumes.md)」を参照してください。

   1. オンプレミスの VM で、Windows キー + Q キーを押して **iSCSI**を検索します。
   2. **[iSCSI イニシエーター]**を選択 します。
   3. **[構成]** タブを選択 し、イニシエーターの名前をコピーします。
   4. [Azure クラシック ポータル](https://manage.windowsazure.com/)にログインします。
   5. **[StorSimple]** タブを選択してから、物理デバイスを含む StorSimple Manager Service を選択します。
   6. ボリューム コンテナーを作成してから、ボリュームを作成します  (これらのボリュームは、ファイル サーバー VM でのファイル共有に使用します)。 イニシエーター名をコピーし、ボリューム作成時にアクセス制御レコードに適切な名前を付けます。
   7. **[構成]** タブを選択し、デバイスの IP アドレスを書き留めます。
   8. 使用しているオンプレミスの VM で **[iSCSI イニシエーター]** に再び移動し、[クイック接続] セクションに IP アドレスを入力します。 **[クイック接続]** をクリックします (デバイスが接続されます)。
   9. Azure 管理ポータルを開き、 **[ボリュームとデバイス]** タブを選択します。 **[自動構成]**をクリックします。 先ほど作成したボリュームが表示されます。
   10. ポータルで **[デバイス]** タブを選択してから、**[Create a New Virtual Device (新しい仮想デバイスの作成)]** を選択します  (フェールオーバーが発生した場合にこの仮想デバイスが使用されます)。 この新しい仮想デバイスはオフラインで維持できるため、余分なコストがかかりません。 仮想デバイスをオフラインにするには、ポータルの **[Virtual Machines]** セクションでデバイスをシャットダウンします。
   11. オンプレミスの VM に戻って [ディスクの管理] を開きます (Windows キー + X キーを押してから、 **[ディスクの管理]**を 選択します)。
   12. 余分なディスクが表示される場合があります (作成したボリュームの数によって異なります)。 1 つ目のディスクを右クリックして **[ディスクの初期化]** を選択し、**[OK]** をクリックします。 **[未割り当て]** セクションを右クリックして **[新しいシンプル ボリューム]** を選択し、ドライブ文字を割り当ててからウィザードを終了します。
   13. すべてのディスクに対して手順 l を繰り返します。 これで、エクスプ ローラーの **[PC]** にすべてのディスクが表示されます。
   14. ファイル サービスおよびストレージ サービス ロールを使用して、これらのボリュームでファイル共有を作成します。

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Azure Site Recovery コンテナーを作成および準備するには
ファイル サーバー VM を保護する前に Azure Site Recovery で作業を開始するには、「 [Azure Site Recovery を利用し、オンプレミス Hyper-V 仮想マシンと Azure (VMM なし) の間で複製する](../site-recovery/site-recovery-hyper-v-site-to-azure.md) 」を参照してください。

#### <a name="to-enable-protection"></a>保護を有効にするには
1. Azure Site Recovery を使用して保護したいオンプレミスの VM から iSCSI ターゲットを切断します。

   1. Windows キー + Q キーを押して、 **iSCSI**を検索します。
   2. **[iSCSI イニシエーターのセットアップ]**を選択します。
   3. 既に接続されていた StorSimple デバイスを切断します。 保護を有効にするとき、デバイスを切断する代わりに、ファイル サーバーの電源を数分間オフにすることもできます。

   > [!NOTE]
   > この場合、ファイル共有が一時的に使用できなくなります。
   >
   >
2. Azure Site Recovery ポータルから、ファイル サーバー VM の[仮想マシンの保護を有効にします](../site-recovery/site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication)。
3. 最初の同期の開始時に、ターゲットに再接続できます。 iSCSI イニシエーターに移動して StorSimple デバイスを選択し、 **[接続]**をクリックします。
4. 同期の完了後に VM のステータスが **[保護済み]** になっていれば、その VM を選択してから **[構成]** タブをクリックし、設定に合わせて VM のネットワークを更新します (これはフェールオーバーされた VM を含むネットワークです)。 ネットワークが表示されないときは、同期が実行中ということです。

### <a name="enable-protection-of-storsimple-volumes"></a>StorSimple ボリュームの保護を有効にする方法
StorSimple ボリュームの **[このボリュームの既定のバックアップの有効化]** オプションを選択していない場合は、StorSimple Manager サービスの **[バックアップ ポリシー]** に移動して、すべてのボリュームに適切なバックアップ ポリシーを作成します。 バックアップ頻度を、アプリケーションで実行したい回復ポイントの目標 (RPO) に設定することをお勧めします。

### <a name="configure-the-network"></a>ネットワークの構成方法
ファイル サーバー VM の場合、フェールオーバー後に VM ネットワークが適切な障害復旧ネットワークに接続されるように Azure Site Recovery でネットワーク設定を構成します。

ネットワーク設定を構成するには、次の図に示すように **[VMM クラウド]** または **[保護グループ]** で VM を選択します。

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>復旧計画の作成
ASR で復旧計画を作成し、ファイル共有のフェールオーバー プロセスを自動化することができます。 障害が発生した場合は、ワンクリックするだけで数分以内にファイル共有を稼働できます。 この自動化を有効にするには、Azure オートメーション アカウントが必要です。

#### <a name="to-create-the-account"></a>アカウントを作成するには
1. Azure クラシック ポータルにアクセスして、 **[Automation]** セクションに移動します。
2. 新しいオートメーション アカウントを作成します。 アカウントは、StorSimple Cloud Appliance とストレージ アカウントが作成されたのと同じ geo/リージョンに設定してください。
3. **[新規]** &gt; **[App Services]** &gt; **[Automation]** &gt; **[Runbook]** &gt; **[ギャラリーから]** をクリックして、必要なすべての Runbook をオートメーション アカウントにインポートします。

   ![](./media/storsimple-dr-using-asr/image3.png)
4. ギャラリーの **[障害復旧]** ウィンドウで、次の Runbook を追加します。

   * StorSimple ボリューム コンテナーのフェールオーバー
   * テスト フェールオーバー (TFO) 後の StorSimple ボリュームのクリーンアップ
   * フェールオーバー後の StorSimple デバイスでのボリューム マウント
   * StorSimple Virtual Appliance の開始
   * Azure VM でのカスタム スクリプト拡張機能のアンインストール

     ![](./media/storsimple-dr-using-asr/image4.png)
5. オートメーション アカウントの Runbook を選択して **[作成者]** タブをクリックして、すべてのスクリプトを発行します。 この手順の後、 **[Runbook]** タブは次のように表示されます。

    ![](./media/storsimple-dr-using-asr/image5.png)
6. オートメーション アカウントで、**[資産]** タブの **[設定の追加]** &gt; **[資格情報の追加]** をクリックしてから Azure の資格情報を追加し、その資産に AzureCredential という名前を付けます。

   Windows PowerShell の資格情報を使用します。 資格情報は、組織 ID ユーザー名とパスワードを含み、この Azure サブスクリプションにアクセスでき、多要素認証が無効にされている必要があります。 資格情報は、フェールオーバー時にユーザーに代わって認証し、障害復旧サイトでファイル サーバー ボリュームを稼働するために必要です。
7. オートメーション アカウントで、**[資産]** タブを選択してから **[設定の追加]** &gt; **[変数の追加]** を選択し、次の変数を追加します。 これらの資産を暗号化することもできます。 これらの変数は、復旧計画によって異なります。 (次の手順で作成する) 復旧計画の名前が TestPlan の場合、変数は TestPlan StorSimRegKey や TestPlan-AzureSubscriptionName などになります。

   * *RecoveryPlanName***-StorSimRegKey**: StorSimple Manager サービス登録キー。
   * *RecoveryPlanName***-AzureSubscriptionName**: Azure サブスクリプションの名前。
   * *RecoveryPlanName***-ResourceName**: StorSimple デバイスを含む StorSimple リソースの名前。
   * *RecoveryPlanName***-DeviceName**: フェールオーバーする必要のあるデバイス。
   * *RecoveryPlanName***-TargetDeviceName**: フェールオーバーされるコンテナーが含まれる StorSimple Cloud Appliance。
   * *RecoveryPlanName***-VolumeContainers**: フェールオーバーする必要があるデバイスのボリューム コンテナーの、コンマで区切られた文字列。(例: volcon1、volcon2、volcon3。)
   * RecoveryPlanName**-TargetDeviceDnsName**: ターゲット デバイスのサービス名 (これは **[仮想マシン]** セクションに表示されます: サービス名は DNS 名と同じです)。
   * *RecoveryPlanName***-StorageAccountName**: (フェールオーバーされた VM で実行する必要がある) スクリプトが格納されるストレージ アカウント名。 スクリプトを一時的に格納する領域があれば、どのストレージ アカウントでも使用できます。
   * *RecoveryPlanName***-StorageAccountKey**: 上記のストレージ アカウントのアクセス キー。
   * *RecoveryPlanName***-ScriptContainer**: スクリプトが格納されるクラウドのコンテナー名。 コンテナーが存在しない場合に作成されます。
   * *RecoveryPlanName***-VMGUIDS**: VM が保護されたら、Azure Site Recovery はフェールオーバーされた VM の詳細が識別できる一意の ID を、すべての VM に割り当てます。 VMGUID を取得するには、**[Recovery Services]** タブを選択して、**[保護された項目]** &gt; **[保護グループ]** &gt; **[マシン]** &gt; **[プロパティ]** をクリックします。 VM が複数ある場合は、コンマ区切りの文字列として GUID を追加します。
   * *RecoveryPlanName***-AutomationAccountName**: Runbook と資産を追加したオートメーション アカウントの名前。

   たとえば、復旧計画の名前が、fileServerpredayRP であった場合、すべての資産を追加し終えたら **[資産]** タブは次のように表示されます。

   ![](./media/storsimple-dr-using-asr/image6.png)
8. **[Recovery Services]** セクションに移動して、先ほど作成した Azure Site Recovery コンテナーを選択します。
9. **[復旧計画]** タブを選択し、次のように新しい復旧計画を作成します。

   a.  名前を指定して、適切な **[保護グループ]** を選択します。

   b.  復旧計画に追加したい VM を保護グループから選択します。

   c.  復旧計画の作成後に、その計画を選択してカスタマイズ ビューを開きます。

   d.  **[すべてのグループのシャットダウン]** を選択して **[スクリプト]** をクリックし、**[すべてのグループのシャットダウンの前にプライマリ側スクリプトを追加]** を選択します。

   e.  オートメーション アカウント (Runbook の追加先) を選択してから、**[Fail over-StorSimple-Volume-Containers]** の Runbook を選択します。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。  **[グループ 1: 開始]** をクリックして **[仮想マシン]** を選択してから、復旧計画で保護する VM を追加します。

   g.  **[グループ 1: 開始]** をクリックして **[スクリプト]** を選択してから、**[After Group 1 (グループ 1 の後)]** と同じ順序で次のスクリプトをすべて追加します。

   * Start-StorSimple-Virtual-Appliance runbook
   * Fail over-StorSimple-volume-containers runbook
   * Mount-volumes-after-failover runbook
   * Uninstall-custom-script-extension runbook
10. 同じ **[グループ 1: 後の手順]** セクションで、上記の 4 つのスクリプトの後に手動アクションを追加します。 このアクションを追加するポイントでは、すべてが正しく動作していることを確認できます。 このアクションは、テスト フェールオーバーの一環として追加する必要があります (そのため **[テスト フェールオーバー]** チェック ボックスのみをオンにします)。
11. 手動アクションの後に、他の Runbook に使用した手順と同様にクリーンアップ スクリプトを追加します。 復旧計画を保存します。

    > [!NOTE]
    > テスト フェールオーバーの実行中、手動アクションを実行するときにすべてを検証してください。これは手動アクションの完了後に、ターゲット デバイスに複製された StorSimple ボリュームがクリーンアップの一環として削除されてしまうのを防ぐためです。
    >
    >

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>テスト フェールオーバーの実行
テスト フェールオーバー実行中の Active Directory に関する注意事項については、「 [Azure Site Recovery で Active Directory と DNS を保護する](../site-recovery/site-recovery-active-directory.md) 」を参照してください。 テスト フェールオーバーの実行時に、オンプレミスの設定はまったく影響を受けません。 オンプレミスの VM に接続された StorSimple ボリュームは、Azure の StorSimple Cloud Appliance に複製されます。 Azure ではテスト用の VM が立ち上がり、複製されたボリュームが VM に接続されます。

#### <a name="to-perform-the-test-failover"></a>テスト フェールオーバーを実行するには
1. Azure クラシック ポータルで、Site Recovery コンテナーを選択します。
2. ファイル サーバー VM 用に作成された復旧計画をクリックします。
3. **[テスト フェールオーバー]**クリックします。
4. テスト フェールオーバー プロセスを開始する仮想ネットワークを選択します。

   ![](./media/storsimple-dr-using-asr/image8.png)
5. セカンダリ環境が立ち上がったら、検証を行うことができます。
6. 検証が完了したら、 **[Validations Complete (検証完了)]**をクリックします。 テスト フェールオーバー環境がクリーニングされ、TFO 操作が完了します。

## <a name="perform-an-unplanned-failover"></a>計画されていないフェールオーバーの実行
計画されていないフェールオーバーでは、StorSimple ボリュームが仮想デバイスにフェールオーバーされ、レプリカ VM が Azure で稼働した後に、これらのボリュームが VM に接続されます。

#### <a name="to-perform-an-unplanned-failover"></a>計画されていないフェールオーバーを実行するには
1. Azure クラシック ポータルで、Site Recovery コンテナーを選択します。
2. ファイル サーバー VM 用に作成された復旧計画をクリックします。
3. **[フェールオーバー]** をクリックしてから、**[計画されていないフェールオーバー]** を選択します。

   ![](./media/storsimple-dr-using-asr/image9.png)
4. ターゲット ネットワークを選択し、チェック マーク アイコン (✓) をクリックしてフェールオーバー プロセスを開始します。

## <a name="perform-a-planned-failover"></a>計画されたフェールオーバーの実行
計画されたフェールオーバーでは、オンプレミスのファイル サーバー VM が正常にシャットダウンされ、StorSimple デバイス上のボリュームのクラウド バックアップ スナップショットが取得されます。 StorSimple ボリュームが仮想デバイスにフェールオーバーされ、Azure でレプリカ VM が稼働した後に、これらのボリュームが VM に接続されます。

#### <a name="to-perform-a-planned-failover"></a>計画されたフェールオーバーを実行するには
1. Azure クラシック ポータルで、Site Recovery コンテナーを選択します。
2. ファイル サーバー VM 用に作成された復旧計画をクリックします。
3. **[フェールオーバー]** をクリックしてから、**[計画されたフェールオーバー]** を選択します。
4. ターゲット ネットワークを選択し、チェック マーク アイコン (✓) をクリックしてフェールオーバー プロセスを開始します。

## <a name="perform-a-failback"></a>フェールバックの実行
フェールバックでは、バックアップの作成後に、StorSimple ボリューム コンテナーが元の物理デバイスにフェールオーバーされます。

#### <a name="to-perform-a-failback"></a>フェールバックを実行するには
1. Azure クラシック ポータルで、Site Recovery コンテナーを選択します。
2. ファイル サーバー VM 用に作成された復旧計画をクリックします。
3. **[フェールオーバー]** をクリックしてから、**[計画されたフェールオーバー]** または **[計画されていないフェールオーバー]** を選択します。
4. **[方向の変更]**クリックしします。
5. 適切なデータ同期と VM 作成のオプションを選択します。
6. チェック マーク アイコン (✓) をクリックして、フェールバック プロセスを開始します。

   ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>ベスト プラクティス
### <a name="capacity-planning-and-readiness-assessment"></a>キャパシティ プランニングと準備状態評価
#### <a name="hyper-v-site"></a>Hyper-V サイト
[ユーザー向け容量計画ツール](http://www.microsoft.com/download/details.aspx?id=39057) を使用すると、Hyper-V レプリカ環境のデプロイに必要なサーバー、ストレージ、およびネットワーク インフラストラクチャを設計できます。

#### <a name="azure"></a>Azure
[Azure Virtual Machine 準備状態評価ツール](http://azure.microsoft.com/downloads/vm-readiness-assessment/) を VM で実行すると、VM が Azure VM および Azure Site Recovery Services と互換性があることを確認できます。 準備状態評価ツールを使用すると、VM の構成が確認され、構成と Azure に互換性がないと警告が表示されます。 たとえば、C: ドライブが 127 GB を超える場合は警告が表示されます。

キャパシティ プランニングは、少なくとも&2; つの重要なプロセスで構成されます。

* オンプレミスの Hyper-V VM を Azure VM のサイズ (A6、A7、A8、A9 など) にマッピングする
* 必要なインターネット帯域幅を決定する

## <a name="limitations"></a>制限事項
* 現時点では、(1 つの StorSimple Cloud Appliance に) フェールオーバーできるのは 1 つの StorSimple デバイスのみです。 1 つのファイル サーバーが複数の StorSimple デバイスにまたがる場合は、現在サポートされていません。
* VM の保護を有効にするときにエラーが発生した場合は、iSCSI ターゲットが切断されていることを確認してください。
* 複数ボリューム コンテナーにまたがったバックアップ ポリシーに従ってグループ化されたすべてのボリューム コンテナーは、一緒にフェールオーバーされます。
* 選択したボリューム コンテナー内のすべてのボリュームはフェールオーバーされます。
* 1 つの StorSimple Cloud Appliance の最大容量が 64 TB であるため、合計 64 TB を超えるボリュームはフェール オーバーすることができません。
* 計画/計画されていないフェールオーバーが失敗し、VM が Azure で作成された場合は、その VM をクリーンアップしないでください。 代わりに、フェールバックを実行します。 VM を削除してしまうと、オンプレミスの VM を再び立ち上げることができなくなります。
* フェールオーバー後にボリュームが表示されない場合、VM に移動して、[ディスクの管理] を開き、ディスクを再スキャンしてからオンラインにします。
* 障害復旧サイトのドライブ文字がオンプレミスのドライブ文字と異なる場合があります。 このような場合は、フェールオーバーが完了したら、手動で問題を修正する必要があります。
* オートメーション アカウントに資産として入力する Azure の資格情報では、多要素認証を無効にする必要があります。 この認証が無効になっていないと、スクリプトは自動的に実行されないため、復旧計画が失敗します。
* フェールオーバー ジョブのタイムアウト: ボリューム コンテナーのフェールオーバーにかかる時間が、Azure Site Recovery でスクリプトあたりの制限されている時間 (現在は 120 分) を超えると、StorSimple のスクリプトはタイムアウトになります。
* バックアップ ジョブのタイムアウト: ボリュームのバックアップにかかる時間が、Azure Site Recovery でスクリプトあたりの制限されている時間 (現在は 120 分) を超えると、StorSimple のスクリプトはタイムアウトになります。

  > [!IMPORTANT]
  > Azure ポータルから手動でバックアップを実行し、復旧計画をもう一度実行してください。
  >
  >
* 複製ジョブのタイムアウト: ボリュームの複製にかかる時間が、Azure Site Recovery でスクリプトあたりの制限されている時間 (現在は 120 分) を超えると、StorSimple のスクリプトがタイムアウトになります。
* 時間同期のエラー: ポータルでバックアップが正常に作成された場合でも、バックアップが失敗したというエラーが StorSimple スクリプトで表示されることがあります。 このエラーの原因として、StorSimple アプライアンスの時間がタイム ゾーンの現在の時間と同期されていないことが考えられます。

  > [!IMPORTANT]
  > アプライアンスの時間とタイム ゾーンの現在の時間を同期してください。
  >
  >
* アプライアンス フェールオーバーのエラー: 復旧計画の実行中にアプライアンスのフェールオーバーが実行されると、StorSimple スクリプトが失敗する場合があります。

  > [!IMPORTANT]
  > アプライアンスのフェールオーバーが完了してから、復旧計画を再実行してください。
  >
  >

## <a name="summary"></a>概要
Azure Site Recovery を使用すると、StorSimple ストレージでホストされているファイル共有を含むファイル サーバー VM に対して、自動障害復旧計画を作成できます。 障害発生時に、任意の場所から数秒以内にフェールオーバーを開始し、数分以内にアプリケーションを稼働させることができます。



<!--HONumber=Jan17_HO4-->


