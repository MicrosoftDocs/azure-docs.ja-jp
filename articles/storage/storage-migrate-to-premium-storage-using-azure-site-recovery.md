---
title: "Azure Site Recovery を使用した Azure Premium Storage への移行 | Microsoft Docs"
description: "Site Recovery を使用して既存の仮想マシンを Azure Premium Storage に移行します。 Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。"
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2016
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 41e3db2762998bd042c0a23fccd03e599bd237a5
ms.lasthandoff: 03/27/2017


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>Azure Site Recovery を使用した Premium Storage への移行

[Azure Premium Storage](storage-premium-storage.md) は、高負荷の I/O ワークロードを実行する仮想マシン (VM) に対して高パフォーマンスで待ち時間の少ないディスクのサポートを実現するサービスです。 このガイドの目的は、[Azure Site Recovery](../site-recovery/site-recovery-overview.md) を使用して、ユーザーが Standard Storage アカウントから Premium Storage アカウントに VM ディスクを移行できるようにすることです。

Site Recovery は、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび VM のレプリケーションを調整することでビジネス継続性と障害復旧の戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリケーションとワークロードの可用性を維持します。 プライマリ ロケーションが通常の動作に戻ると、その場所にフェールバックします。 Site Recovery では、運用環境に影響を与えずに障害復旧の練習ができるよう、テスト フェールオーバーの機能が用意されています。 また、予想外の障害が発生した場合にはフェールオーバーを実行し、データの損失を (レプリケーション頻度に応じて) 最小限に抑えることができます。 Premium Storage に移行するシナリオでは、Azure Site Recovery で [Site Recovery でのフェールオーバー](../site-recovery/site-recovery-failover.md)を使用して、ターゲット ディスクを Premium Storage アカウントに移行できます。

Premium Storage に移行する際は、Site Recovery を使用することをお勧めします。Site Recovery を使えばダウンタイムを最小限に抑えられるほか、ディスクのコピーや新しい VM の作成を手作業で実施する手間を省くことができるからです。 Site Recovery では、フェールオーバー時にディスクが体系的にコピーされ、新しい VM が作成されます。 Site Recovery は、ダウンタイムを最小またはゼロに抑えるため、さまざまな種類のフェールオーバーをサポートしています。 ダウンタイムを計画し、データ損失の量を見積もるときは、Site Recovery の「[フェールオーバーの種類](../site-recovery/site-recovery-failover.md)」の表を参照してください。 [フェールオーバー後に Azure VM に接続する準備](../site-recovery/site-recovery-vmware-to-azure.md#prepare-vms-for-replication)が整っている場合は、フェールオーバー後に RDP を使用して Azure VM に接続できます。

![][1]

## <a name="migration-scenario-components"></a>移行シナリオのコンポーネント

**この移行シナリオに関連する Site Recovery コンポーネント:**

* **構成サーバー**: 通信を調整し、データのレプリケーションおよび回復プロセスを管理する Azure VM です。 この VM ではセットアップ ファイルを 1 つ実行し、構成サーバーと、レプリケーションのゲートウェイとなる追加のコンポーネント (プロセス サーバー) をインストールします。 [構成サーバーの前提条件](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites)に関するセクションを参照してください。 構成サーバーは、1 回構成するだけで同じリージョンへのすべての移行に使用できます。

* **プロセス サーバー**: ソース VM からレプリケーション データを受け取り、キャッシュ、圧縮、および暗号化によりデータを最適化してからストレージ アカウントに送信するレプリケーション ゲートウェイです。 また、ソース VM へのモビリティ サービスのプッシュ インストールを処理し、ソース VM の自動検出も実行します。 既定のプロセス サーバーは、構成サーバーにインストールされます。 追加のスタンドアロン プロセス サーバーをデプロイすることでデプロイメントを拡張できます。 [プロセス サーバーのデプロイに関するベスト プラクティス](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/)および[追加のプロセス サーバーのデプロイ](../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)に関する説明を参照してください。 プロセス サーバーは、1 回構成するだけで同じリージョンへのすべての移行に使用できます。

* **モビリティ サービス**: レプリケートする標準 VM すべてにデプロイされるコンポーネントです。 標準 VM で発生するデータ書き込みをキャプチャし、それをプロセス サーバーに転送します。 [レプリケーション対象のマシンの前提条件](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites)に関するセクションを参照してください。

**Azure の必須コンポーネント**: 

* Azure サブスクリプション
* レプリケートされたデータを格納するための Azure Premium Storage アカウント
* フェールオーバー時に作成された VM が接続する Azure 仮想ネットワーク (VNet)。 この Azure VNet は、Site Recovery が実行されているリージョンと同じリージョンに存在している必要があります。
* レプリケーション ログを格納するための Azure Standard Storage アカウント。 これには、移行する VM ディスクと同じストレージ アカウントを使用できます。

次の図は、これらのコンポーネント間でのやり取りを示しています。

![][15]

> [!NOTE]
> Site Recovery は、記憶域スペース ディスクの移行をサポートしていません。

他のシナリオで必要になる追加コンポーネントについては、[シナリオ アーキテクチャ](../site-recovery/site-recovery-vmware-to-azure.md)に関するセクションを参照してください。

## <a name="prerequisites"></a>前提条件

* 前のセクションの関連する移行シナリオのコンポーネントを理解している。
* [Site Recovery でのフェールオーバー](../site-recovery/site-recovery-failover.md)について学習し、ダウンタイムを計画している。

## <a name="setup-and-migration-steps"></a>セットアップと移行の手順

Site Recovery を使用して、リージョン間または同じリージョン内で Azure IaaS VM を移行できます。 以降の手順は、[VMware VM または物理サーバーの Azure へのレプリケート](../site-recovery/site-recovery-vmware-to-azure.md)に関する記事を基に、この移行シナリオに応じた変更を加えたものとなっています。 この記事の手順に加えて、リンク先のページの詳細な手順も参照してください。

1. **Recovery Services コンテナーを作成します**。 [Azure Portal](https://portal.azure.com) を使用して、Site Recovery コンテナーを作成および管理します。 **[新規]** > **[管理]** > **[Backup and Site Recovery (OMS)** **(バックアップと Site Recovery (OMS))]** の順にクリックします。 または、**[参照]** > **[Recovery Services コンテナー]** > **[追加]** の順にクリックします。 VM は、この手順で指定したリージョンにレプリケートされます。 今回は同じリージョン内で移行を行うため、ソース VM とソース ストレージ アカウントがあるリージョンを選択します。 Premium Storage アカウントへの移行は、[Azure Portal](https://portal.azure.com) でのみサポートされています。[クラシック ポータル](https://manage.windowsazure.com)ではサポートされていませんのでご注意ください。

2. **保護の目標を選択します**。 構成サーバーをインストールする VM で [Azure Portal](https://portal.azure.com) を開きます。 **[Recovery Services コンテナー]** > **[設定]** の順に移動します。 **[設定]** で、**[Site Recovery]** を選択します。 **[Site Recovery]** で、**[手順 1: インフラストラクチャを準備する]** を選択します。 **[インフラストラクチャの準備]** で、**[保護の目標]** を選択します。
  
  ![][2]
  
  **[保護の目標]** の下の最初のドロップダウン リストで、**[To Azure (Azure へ)]** を選択します。 2 番目のドロップダウン リストで、**[非仮想化/その他]** を選択し、**[OK]** をクリックします。
  
  ![][3]
  
3. **ソース環境 (構成サーバー) を設定します**。 **[インフラストラクチャの準備]** > **[ソースの準備]** > **[サーバーの追加]** ブレードに移動し、**Azure Site Recovery 統合セットアップ**と**コンテナー登録キー**をダウンロードします。 コンテナー登録キーは、統合セットアップを実行するために必要です。 キーは生成後 5 日間有効です。
  
  ![][4]
  
  ![][5]
  
  構成サーバーとして使用している VM で統合セットアップを実行して、構成サーバーとプロセス サーバーをインストールします。 インストールを完了するための手順は、[こちらのページ](../site-recovery/site-recovery-vmware-to-azure.md#set-up-the-source-environment)のスクリーンショットで確認できます。 この移行シナリオ用の手順については、以降のスクリーンショットを参照してください。

  **[開始する前に]** で **[Install the configuration server and process server] \(構成サーバーとプロセス サーバーをインストールする)** を選択します。
  
  ![][6]

  **[登録]** で、コンテナーからダウンロードした登録キーを参照して選択します。
  
  ![][7]

  **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 この移行シナリオでは、**[いいえ]** を選択します。
  
  ![][8]
  
  インストールが完了すると、**[Microsoft Azure Site Recovery 構成サーバー]** ウィンドウが表示されます。 **[アカウントの管理]** タブを使用して、Site Recovery が自動検出に使用できるアカウントを作成します  (物理マシンの保護に関するシナリオでは、アカウントの設定は必要ありません。ただし、以降の手順のいずれかを実行するには少なくとも 1 つのアカウントが必要です。 アカウントを作成する場合には、アカウント名とパスワードに任意の文字列を使用してかまいません)。**[Vault Registration (コンテナーの登録)]** タブを使用して、コンテナー資格情報ファイルをアップロードします。
  
  ![][9]

4. **ターゲット環境を設定します**。 **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、フェールオーバー後に VM に使用するデプロイメント モデルを指定します。 シナリオに応じて、**[クラシック]** または **[Resource Manager]** を選択できます。
  
  ![][10]

  Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。 レプリケートされたデータに Premium Storage アカウントを使用している場合は、レプリケーション ログを格納するために、追加の Standard Storage アカウントを設定する必要があります。

5. **レプリケーション設定をセットアップします**。 [このページ](../site-recovery/site-recovery-vmware-to-azure.md#set-up-replication-settings)の手順に従って、作成したレプリケーション ポリシーに構成サーバーが正常に関連付けられていることを確認します。

6. **容量計画**。 [Capacity Planner](../site-recovery/site-recovery-capacity-planner.md) を使用して、レプリケーション ニーズを満たすためのネットワーク帯域幅、ストレージ、およびその他の要件を正確に見積もります。 作業が完了したら、**[容量計画は完了していますか?]** で **[はい]** を選択します。
  
  ![][11]

7. **モビリティ サービスをインストールし、レプリケーションを有効にします**。 [ソース VM にインストールをプッシュする](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-discovery-and-push-installation)か、またはソース VM に[手動でモビリティ サービスをインストールする](../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md)かを選択できます。 インストールをプッシュするための要件と手動インストーラーのパスは、リンク先のページをご確認ください。 手動インストールを実行する場合は、内部 IP アドレスを使用して構成サーバーを見つける必要があります。 
  
  ![][12]
  
  フェールオーバーされる VM には 2 つの一時ディスクが存在します。1 つはプライマリ VM の一時ディスク、もう 1 つは回復リージョンでの VM のプロビジョニング中に作成された一時ディスクです。 レプリケーションの前に一時ディスクを除外するには、レプリケーションを有効にする前に、モビリティ サービスをインストールします。 一時ディスクを除外する方法の詳細については、「[レプリケーションからディスクを除外する](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication)」を参照してください。
**次に、レプリケーションを有効にします。**
  * **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めて有効にした後は、コンテナーで [+ レプリケート] をクリックして、追加のマシンのレプリケーションを有効にします。
  * 手順 1 で、ソースをプロセス サーバーとして設定します。
  * 手順 2 で、フェールオーバー後のデプロイメント モデル、移行先の Premium Storage アカウント、ログを保存するための Standard Storage アカウント、およびフェールオーバー先の仮想ネットワークを指定します。 
  * 手順 3 で、保護対象の VM を IP アドレスで追加します (VM を探すにあたり内部 IP アドレスが必要になる場合があります)。 
  * 手順 4 で、以前にプロセス サーバー上で設定したアカウントを選択して、プロパティを構成します。 
  * 手順 5 で、以前に作成したレプリケーション ポリシーを選択し、レプリケーション設定を行います。 
  **[OK]** をクリックしてレプリケーションを有効にします。
  
  > [!NOTE]
  > Azure VM の割り当てを解除して再起動した場合、同じ IP アドレスが割り当てられる保証はありません。 構成サーバー、プロセス サーバー、または保護対象の Azure VM の IP アドレスが変更された場合、このシナリオのレプリケーションが正常に機能するとは限りません。
  
  ![][13]
  
  Azure Storage 環境を設計する場合は、可用性セット内の VM ごとに別個のストレージ アカウントを使用することをお勧めします。 また、[Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) および [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) VM のストレージ レイヤーのベスト プラクティスに従うことをお勧めします。 VM ディスクを複数のストレージ アカウントに分散することで、ストレージの可用性を向上させ、I/O を Azure ストレージ インフラストラクチャに分散させることができます。 VM が 1 つの可用性セットに存在する場合には、すべての VM のディスクを 1 つのストレージ アカウントにレプリケートするのではなく、複数の VM を複数回移行して、同じ可用性セット内の VM が単一のストレージ アカウントを共有しないようにすることを強くお勧めします。 **[レプリケーションを有効にする]** ブレードを使用して、各 VM のターゲット ストレージ アカウントを 1 つずつ設定します。 フェールオーバー後のデプロイメント モデルは、ニーズに合わせて選択できます。 フェールオーバー後のデプロイメント モデルとして Resource Manager (RM) を選択した場合は、RM VM を RM VM にフェールオーバーすることができます。また、クラシック VM を RM VM にフェールオーバーすることもできます。

8. **テスト フェールオーバーを実行します**。 レプリケーションが完了したかどうかを確認するには、[Site Recovery] をクリックし、**[設定]** > **[レプリケートされたアイテム]** の順にクリックします。 レプリケーション プロセスの状態と進捗状況が表示されます。 初期レプリケーションが完了したら、テスト フェールオーバーを実行してレプリケーション戦略を検証します。 テスト フェールオーバーの詳細な手順については、[Site Recovery でのテスト フェールオーバーの実行](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover)に関するセクションを参照してください。 テスト フェールオーバーの状態を確認するには、**[設定]** > **[ジョブ]** > **<フェールオーバー計画名>** の順に選択します。 ブレードに、各手順の内訳とその結果 (成功、失敗) が表示されます。 いずれかの手順でテスト フェールオーバーが失敗した場合は、手順をクリックしてエラー メッセージを確認します。 フェールオーバーを実行する前に、VM とレプリケーション戦略が要件を満たしていることを確認してください。 テスト フェールオーバーの詳細と手順については、「[Site Recovery での Azure へのフェールオーバーをテストする](../site-recovery/site-recovery-test-failover-to-azure.md)」をご覧ください。

9. **フェールオーバーを実行します**。 テスト フェールオーバーが完了したら、フェールオーバーを実行してディスクを Premium Storage へ移行し、VM インスタンスをレプリケートします。 「[Run a failover (フェールオーバーの実行)](../site-recovery/site-recovery-failover.md#run-a-failover)」の詳細な手順に従ってください。 このとき、**[Shut down VMs and synchronize the latest data (VM をシャットダウンし、最新のデータを同期する)]** を選択します。こうすることで、Site Recovery では保護された VM をシャットダウンしてからデータの同期を試行するようになるため、最新バージョンのデータをフェールオーバーすることができます。 このオプションを選択しなかった場合、または試行が成功しない場合は、利用可能な最新の回復ポイントから VM のフェールオーバーが実行されます。 Site Recovery では、Premium Storage 対応の VM と同じか類似の種類の VM インスタンスが作成されます。 「[Windows Virtual Machines の料金](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/)」または「[Linux Virtual Machines の料金](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)」で、さまざまな VM インスタンスのパフォーマンスと価格を確認できます。

## <a name="post-migration-steps"></a>移行後の手順

1. **レプリケートされた VM を可用性セットに構成します (該当する場合)**。 Site Recovery では、VM を可用性セットと併せて移行する操作はサポートされていません。 レプリケートされた VM のデプロイに応じて、次のいずれかを実行してください。
  * クラシック デプロイメント モデルを使用して作成された VM の場合: Azure Portal で可用性セットに VM を追加します。 詳細な手順については、「[既存の仮想マシンを可用性セットに追加する](../virtual-machines/windows/classic/configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set)」を参照してください。
  * Resource Manager デプロイメント モデルの場合: VM の構成を保存し、可用性セット内の VM をいったん削除してから再作成します。 これを行うには、[Azure Resource Manager VM 可用性セットの設定](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)スクリプトを使用します。 このスクリプトの制限事項を確認し、スクリプトを実行する前にダウンタイムを計画してください。

2. **以前の VM とディスクを削除します**。 これらを削除する前に、Premium ディスクとソース ディスクの間に一貫性があり、新しい VM がソース VM と同じように動作することを確認してください。 Resource Manager (RM) デプロイメント モデルの場合、Azure Portal でソース ストレージ アカウントから VM を削除したうえで、ディスクを削除します。 クラシック デプロイメント モデルの場合は、クラシック ポータルまたは Azure Portal で VM とディスクを削除できます。 VM を削除してもディスクが削除されない問題が発生した場合は、[RM デプロイで VHD を削除するときに生じるエラーのトラブルシューティング](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)または[クラシック デプロイメントでの VHD の削除に関するトラブルシューティング](storage-cannot-delete-storage-account-container-vhd.md)のページを参照してください。

3. **Azure Site Recovery インフラストラクチャをクリーンアップします**。 Site Recovery が不要になった場合は、レプリケートされたアイテム、構成サーバー、および回復ポリシーを削除した後、Azure Site Recovery コンテナーを削除することで、インフラストラクチャをクリーンアップできます。

## <a name="troubleshooting"></a>トラブルシューティング

* [仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>次のステップ

仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

* [ストレージ アカウント間での Azure 仮想マシンの移行](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server VHD の作成と Azure へのアップロード](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Amazon AWS から Microsoft Azure への仮想マシンの移行](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースもご覧ください。

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png

