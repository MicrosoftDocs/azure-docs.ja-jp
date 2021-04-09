---
title: Azure Site Recovery で Windows VM を Azure Premium Storage に移行する
description: Azure Site Recovery を使用して Standard Storage アカウントから Premium Storage アカウントに VM ディスクを移行する方法について説明します。
author: luywang
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: aeb8028468a1e1944a4fd39275f134bfae00dfa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555348"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Azure Site Recovery を使用した Premium Storage への移行

[Azure Premium SSD](../disks-types.md) は、高負荷の I/O ワークロードを実行する仮想マシン (VM) に対して高パフォーマンスで待ち時間の少ないディスクのサポートを実現するサービスです。 このガイドでは、[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) を使用して、ユーザーが Standard Storage アカウントから Premium Storage アカウントに VM ディスクを移行する方法について説明します。

Site Recovery は、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび VM のレプリケーションを調整することでビジネス継続性とディザスター リカバリーの戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリケーションとワークロードの可用性を維持します。 プライマリ ロケーションが通常の動作に戻ると、その場所にフェールバックします。 

Site Recovery では、運用環境に影響を与えずにディザスター リカバリーの練習ができるよう、テスト フェールオーバーの機能が用意されています。 また、予想外の障害が発生した場合にはフェールオーバーを実行し、データの損失を (レプリケーション頻度に応じて) 最小限に抑えることができます。 Premium Storage に移行するシナリオでは、[Site Recovery でのフェールオーバー](../../site-recovery/site-recovery-failover.md)を使用して、ターゲット ディスクを Premium Storage アカウントに移行できます。

Premium Storage に移行する際は、Site Recovery を使用することをお勧めします。Site Recovery を使えばダウンタイムを最小限に抑えられます。 また、ディスクのコピーや新しい VM の作成を手作業で実施する手間を省くことができます。 Site Recovery では、フェールオーバー時にディスクが体系的にコピーされ、新しい VM が作成されます。 

Site Recovery は、ダウンタイムを最小またはゼロに抑えるため、さまざまな種類のフェールオーバーをサポートしています。 ダウンタイムを計画し、データ損失の量を見積もるときは、Site Recovery の「[フェールオーバーの種類](../../site-recovery/site-recovery-failover.md)」を参照してください。 [フェールオーバー後に Azure VM に接続する準備](../../site-recovery/vmware-azure-tutorial.md)が整っている場合は、フェールオーバー後に RDP を使用して Azure VM に接続できます。

![ディザスター リカバリーの図][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery コンポーネント

この移行シナリオに関連する Site Recovery コンポーネントを、以下に示します。

* **構成サーバー**: 通信を調整し、データのレプリケーションおよび回復プロセスを管理する Azure VM です。 この VM では設定 ファイルを 1 つ実行し、構成サーバーと、レプリケーションのゲートウェイとなる追加のコンポーネント (プロセス サーバー) をインストールします。 [構成サーバーの前提条件](../../site-recovery/vmware-azure-tutorial.md)に関するセクションを参照してください。 構成サーバーは、1 回設定するだけで同じリージョンへのすべての移行に使用できます。

* **プロセス サーバー** は、次の機能を持つレプリケーション ゲートウェイです。 

  1. ソース VM からレプリケーション データを受信します。
  2. キャッシュ、圧縮、暗号化を使用してデータを最適化します。
  3. データをストレージ アカウントに送信します。 

  また、ソース VM へのモビリティ サービスのプッシュ インストールを処理し、ソース VM の自動検出も実行します。 既定のプロセス サーバーは、構成サーバーにインストールされます。 追加のスタンドアロン プロセス サーバーをデプロイすることでデプロイメントを拡張できます。 [プロセス サーバーのデプロイに関するベスト プラクティス](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/)および[追加のプロセス サーバーのデプロイ](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)に関する説明を参照してください。 プロセス サーバーは、1 回設定するだけで同じリージョンへのすべての移行に使用できます。

* **モビリティ サービス** は、レプリケートする標準 VM すべてにデプロイされるコンポーネントです。 標準 VM で発生するデータ書き込みをキャプチャし、それをプロセス サーバーに転送します。 [レプリケーション対象のマシンの前提条件](../../site-recovery/vmware-azure-tutorial.md)に関するセクションを参照してください。

次の図は、これらのコンポーネント間でのやり取りを示しています。

![Site Recovery コンポーネントの相互作用][15]

> [!NOTE]
> Site Recovery は、記憶域スペース ディスクの移行をサポートしていません。

他のシナリオで必要になる追加コンポーネントについては、[シナリオ アーキテクチャ](../../site-recovery/vmware-azure-tutorial.md)に関するセクションを参照してください。

## <a name="azure-essentials"></a>Azure の必須コンポーネント

この移行シナリオの Azure の要件を、以下に示します。

* Azure サブスクリプション。
* レプリケートされたデータを格納するための Azure Premium Storage アカウント
* フェールオーバー時に作成された VM が接続する Azure 仮想ネットワーク。 Azure の仮想ネットワークは、Site Recovery が実行されているリージョンと同じリージョンにデプロイされている必要があります。
* レプリケーション ログを格納するための Azure Standard ストレージ アカウント。 これには、移行する VM ディスクと同じストレージ アカウントを使用できます。

## <a name="prerequisites"></a>前提条件

* 前のセクションの関連する移行シナリオのコンポーネントを理解している。
* [Site Recovery でのフェールオーバー](../../site-recovery/site-recovery-failover.md)について学習し、ダウンタイムを計画している。

## <a name="setup-and-migration-steps"></a>セットアップと移行の手順

Site Recovery を使用して、リージョン間または同じリージョン内で Azure IaaS VM を移行できます。 以降の手順は、[VMware VM または物理サーバーの Azure へのレプリケート](../../site-recovery/vmware-azure-tutorial.md)に関する記事を基に、この移行シナリオに応じた変更を加えたものとなっています。 この記事の手順に加えて、リンク先のページの詳細な手順も参照してください。

### <a name="step-1-create-a-recovery-services-vault"></a>手順 1:Recovery Services コンテナーを作成する

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **[リソースの作成]**  >  **[管理]**  >  **[Backup and Site Recovery (OMS)]** を選択します。 または、 **[参照]**  >  **[Recovery Services コンテナー]**  >  **[追加]** を選択します。
   >[!NOTE]
   >Backup および Site Recovery は以前は [OMS スイート](../../azure-monitor/terminology.md#april-2018---retirement-of-operations-management-suite-brand)に含まれていました。
1. VM をレプリケートする先のリージョンを指定します。 今回は同じリージョン内で移行を行うため、ソース VM とソース ストレージ アカウントがあるリージョンを選択します。 

### <a name="step-2-choose-your-protection-goals"></a>手順 2:保護の目標を選択する 

1. 構成サーバーをインストールする VM で [Azure Portal](https://portal.azure.com) を開きます。
2. **[Recovery Services コンテナー]**  >  **[設定]**  >  **[Site Recovery]**  >  **[手順 1:インフラストラクチャを準備する]**  >  **[保護の目標]** の順にクリックします。

   ![[保護の目標] ウィンドウの参照][2]

3. **[保護の目標]** の下の最初のドロップダウン リストで、 **[To Azure (Azure へ)]** を選択します。 2 番目のドロップダウン リストで、 **[非仮想化/その他]** を選択し、 **[OK]** を選択します。

   ![ボックスが入力された [保護の目標] ウィンドウ][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>手順 3:ソース環境 (構成サーバー) を設定する

1. **[インフラストラクチャの準備]**  >  **[ソースの準備]**  >  **[サーバーの追加]** ウィンドウに移動し、**Azure Site Recovery 統合セットアップ** とコンテナー登録キーをダウンロードします。 
 
   コンテナー登録キーは、統合セットアップを実行するために必要です。 キーは生成後 5 日間有効です。

   ![[サーバーの追加] ウィンドウの参照][4]

2. **[サーバーの追加]** ウィンドウで構成サーバーを追加します。

   ![構成サーバーが選択された [サーバーの追加] ウィンドウ][5]

3. 構成サーバーとして使用している VM で統合セットアップを実行して、構成サーバーとプロセス サーバーをインストールします。 インストールを完了するための手順は、[こちらのスクリーンショット](../../site-recovery/vmware-azure-tutorial.md)で確認できます。 この移行シナリオ用の手順については、以降のスクリーンショットを参照してください。

   1. **[開始する前に]** で **[Install the configuration server and process server]\(構成サーバーとプロセス サーバーをインストールする\)** を選択します。

      ![ページを開始する前に][6]

   2. **[登録]** で、コンテナーからダウンロードした登録キーを参照して選択します。

      ![[登録] ページ][7]

   3. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 この移行シナリオでは、 **[いいえ]** を選択します。

      ![[環境の詳細] ページ][8]

4. インストールが完了したら、 **[Microsoft Azure Site Recovery 構成サーバー]** ウィンドウで次の操作を実行します。
 
   1. **[アカウントの管理]** タブを使用して、Site Recovery が自動検出に使用できるアカウントを作成します (物理マシンの保護に関するシナリオでは、アカウントの設定は必要ありません。ただし、以降の手順のいずれかを実行するには少なくとも 1 つのアカウントが必要です。 アカウントを作成する場合には、アカウント名とパスワードに任意の文字列を使用してかまいません)。 
   2. **[Vault Registration (コンテナーの登録)]** タブを使用して、コンテナー資格情報ファイルをアップロードします。

      ![[Vault Registration]\(コンテナーの登録\) タブ][9]

### <a name="step-4-set-up-the-target-environment"></a>手順 4:ターゲット環境をセットアップする

**[インフラストラクチャの準備]**  >  **[ターゲット]** を選択し、フェールオーバー後に VM に使用するデプロイ モデルを指定します。 シナリオに応じて、 **[クラシック]** または **[Resource Manager]** を選択できます。

![[ターゲット] ウィンドウ][10]

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。 

> [!NOTE]
> レプリケートされたデータに Premium Storage アカウントを使用している場合は、レプリケーション ログを格納するために、追加の Standard Storage アカウントを設定する必要があります。

### <a name="step-5-set-up-replication-settings"></a>手順 5:レプリケーション設定をセットアップする

「[レプリケーション設定をセットアップする](../../site-recovery/vmware-azure-tutorial.md)」に従って、作成したレプリケーション ポリシーに構成サーバーが正常に関連付けられていることを確認します。

### <a name="step-6-plan-capacity"></a>手順 6:容量を計画する

1. [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md) を使用して、レプリケーション ニーズを満たすためのネットワーク帯域幅、ストレージ、およびその他の要件を正確に見積もります。 
2. 作業が完了したら、 **[容量計画は完了していますか?]** で **[はい、完了しました]** を選択します。

   ![容量計画を完了したことを確認するボックス][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>手順 7:モビリティ サービスをインストールし、レプリケーションを有効にする

1. [ソース VM にインストールをプッシュする](../../site-recovery/vmware-azure-tutorial.md)か、またはソース VM に[手動でモビリティ サービスをインストールする](../../site-recovery/vmware-azure-install-mobility-service.md)かを選択できます。 インストールをプッシュするための要件と手動インストーラーのパスは、リンク先のページをご確認ください。 手動インストールを実行する場合は、内部 IP アドレスを使用して構成サーバーを見つける必要があります。

   ![構成サーバーの詳細ページ][12]

   フェールオーバーされる VM には 2 つの一時ディスクが存在します。1 つはプライマリ VM の一時ディスク、もう 1 つは回復リージョンでの VM のプロビジョニング中に作成された一時ディスクです。 レプリケーションの前に一時ディスクを除外するには、レプリケーションを有効にする前に、モビリティ サービスをインストールします。 一時ディスクを除外する方法の詳細については、「[レプリケーションからディスクを除外する](../../site-recovery/vmware-azure-tutorial.md)」を参照してください。

2. レプリケーションを有効にするには、次の手順に従います。
   1. **[アプリケーションをレプリケートする]**  >  **[ソース]** を選択します。 レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** を選択して、追加のマシンのレプリケーションを有効にします。
   2. 手順 1 で、 **[ソース]** をプロセス サーバーとして設定します。
   3. 手順 2 で、フェールオーバー後のデプロイ モデル、移行先の Premium Storage アカウント、ログを保存するための Standard Storage アカウント、およびフェールオーバー先の仮想ネットワークを指定します。
   4. 手順 3 では、保護されている VM を IP アドレスを指定して追加します (検出するには、内部 IP アドレスが必要な場合があります)。
   5. 手順 4 で、以前にプロセス サーバー上で設定したアカウントを選択して、プロパティを構成します。
   6. 手順 5 で、「手順 5: レプリケーション設定をセットアップする」で以前に作成したレプリケーション ポリシーを選択します。
   7. **[OK]** を選択します。

   > [!NOTE]
   > Azure VM の割り当てを解除して再起動した場合、同じ IP アドレスが割り当てられる保証はありません。 構成サーバー、プロセス サーバー、または保護対象の Azure VM の IP アドレスが変更された場合、このシナリオのレプリケーションが正常に機能するとは限りません。

   ![[ソース] が選択されている [レプリケーションを有効にする] ウィンドウ][13]

Azure Storage 環境を設計する場合は、可用性セット内の VM ごとに別個のストレージ アカウントを使用することをお勧めします。 ストレージ層のベスト プラクティスに従って、[可用性セットごとに複数のストレージ アカウントを使用する](../availability.md)ことをお勧めします。 VM ディスクを複数のストレージ アカウントに分散することで、ストレージの可用性を向上させ、I/O を Azure ストレージ インフラストラクチャに分散させることができます。

VM が 1 つの可用性セットに存在する場合には、すべての VM のディスクを 1 つのストレージ アカウントにレプリケートするのではなく、複数の VM を複数回移行することを強くお勧めします。 こうすると、同じ可用性セット内の VM が単一のストレージ アカウントを共有しません。 **[レプリケーションを有効にする]** ウィンドウを使用して、各 VM のターゲット ストレージ アカウントを 1 つずつ設定します。
 
フェールオーバー後のデプロイメント モデルは、ニーズに合わせて選択できます。 フェールオーバー後のデプロイ モデルとして Azure Resource Manager を選択する場合、VM (Resource Manager) を VM (Resource Manager) にフェールオーバーするか、VM (クラシック) を VM (Resource Manager) にフェールオーバーすることができます。

### <a name="step-8-run-a-test-failover"></a>手順 8:テスト フェールオーバーの実行

レプリケーションが完了したかどうかを確認するには、[Site Recovery] インスタンスを選択し、 **[設定]**  >  **[レプリケートされたアイテム]** を選択します。 レプリケーション プロセスの状態と進捗状況が表示されます。 

初期レプリケーションが完了したら、テスト フェールオーバーを実行してレプリケーション戦略を検証します。 テスト フェールオーバーの詳細な手順については、[Site Recovery でのテスト フェールオーバーの実行](../../site-recovery/vmware-azure-tutorial.md)に関するセクションを参照してください。 

> [!NOTE]
> フェールオーバーを実行する前に、VM とレプリケーション戦略が要件を満たしていることを確認してください。 テスト フェールオーバーの実行の詳細については、「[Site Recovery での Azure へのフェールオーバーをテストする](../../site-recovery/site-recovery-test-failover-to-azure.md)」を参照してください。

テスト フェールオーバーの状態を確認するには、 **[設定]**  >  **[ジョブ]**  >  *<フェールオーバー計画名>* の順に選択します。 ウィンドウに、各手順の内訳とその結果 (成功、失敗) が表示されます。 いずれかの手順でテスト フェールオーバーが失敗した場合は、手順を選択してエラー メッセージを確認します。 

### <a name="step-9-run-a-failover"></a>手順 9:フェールオーバーの実行

テスト フェールオーバーが完了したら、フェールオーバーを実行してディスクを Premium Storage へ移行し、VM インスタンスをレプリケートします。 「[フェールオーバーの実行](../../site-recovery/site-recovery-failover.md#run-a-failover)」の詳細な手順に従ってください。 

**[仮想マシンをシャットダウンし、最新のデータを同期する]** をオンにします。 このオプションで、Site Recovery では保護された VM をシャットダウンしてからデータの同期を試行するようになるため、最新バージョンのデータをフェールオーバーすることができます。 このオプションを選択しなかった場合、または試行が成功しない場合は、利用可能な最新の復旧ポイントから VM のフェールオーバーが実行されます。 

Site Recovery では、Premium Storage 対応の VM と同じか類似の種類の VM インスタンスが作成されます。 「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」または「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」で、さまざまな VM インスタンスのパフォーマンスと価格を確認できます。

## <a name="post-migration-steps"></a>移行後の手順

1. **レプリケートされた VM を可用性セットに構成します (該当する場合)** 。 Site Recovery では、VM を可用性セットと併せて移行する操作はサポートされていません。 レプリケートされた VM のデプロイに応じて、次のいずれかを実行してください。
   * クラシック デプロイ モデルを使用して作成された VM の場合: Azure portal で可用性セットに VM を追加します。 詳細な手順については、「[既存の仮想マシンを可用性セットに追加する](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic)」を参照してください。
   * Resource Manager デプロイ モデルを使用して作成された VM の場合: VM の構成を保存し、可用性セット内の VM をいったん削除してから再作成します。 これを行うには、[Azure Resource Manager VM 可用性セットの設定](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)スクリプトを使用します。 このスクリプトを実行する前に、制限を確認し、ダウンタイムを計画します。

2. **以前の VM とディスクを削除します**。 Premium ディスクとソース ディスクの間に一貫性があり、新しい VM がソース VM と同じように動作することを確認します。 Azure Portal でソース ストレージ アカウントから VM を削除したうえで、ディスクを削除します。 VM を削除してもディスクが削除されない問題が発生した場合は、「[Troubleshoot storage resource deletion errors](../troubleshooting/storage-resource-deletion-errors.md)」(ストレージのリソースを削除するときに生じるエラーのトラブルシューティング) を参照してください。

3. **Azure Site Recovery インフラストラクチャをクリーンアップします**。 Site Recovery が不要になったら、インフラストラクチャをクリーンアップすることができます。 レプリケートされた項目、構成サーバー、回復ポリシーを削除してから、Azure Site Recovery コンテナーを削除します。

## <a name="troubleshooting"></a>トラブルシューティング

* [仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Microsoft Azure Site Recovery に関する Microsoft Q&A 質問ページ](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>次のステップ

仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

* [ストレージ アカウント間での Azure 仮想マシンの移行](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server VHD の作成と Azure へのアップロード](upload-generalized-managed.md)
* [Amazon AWS から Microsoft Azure への仮想マシンの移行](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースもご覧ください。

* [Azure ストレージ](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png