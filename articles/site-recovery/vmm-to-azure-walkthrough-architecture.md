---
title: "Azure Site Recovery を使用した Azure への Hyper-V レプリケーション (System Center VMM あり) のアーキテクチャを確認する | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して、VMM クラウド内のオンプレミスの Hyper-V VM を Azure にレプリケートするときに使用されるコンポーネントとアーキテクチャの概要を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: df4e227d02901153d3cfcfd4dfd4f11de180763a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture"></a>手順 1: アーキテクチャを確認する


この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V 仮想マシンを Azure にレプリケートする際に使用するコンポーネントとプロセスについて説明します。

コメントはこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。



## <a name="architectural-components"></a>アーキテクチャ コンポーネント

VMM クラウド内の Hyper-V VM を Azure にレプリケートする場合に関係するコンポーネントは多数あります。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
**Azure** | Azure では、Microsoft Azure アカウント、Azure ストレージ アカウント、Azure ネットワークが必要です。 | レプリケートされたデータはストレージ アカウントに格納され、オンプレミス サイトからのフェールオーバーが発生したときにそのレプリケートされたデータで Azure VM が作成されます。<br/><br/> Azure VM は、作成時に Azure 仮想ネットワークに接続します。
**VMM サーバー** | VMM サーバーに、Hyper-V ホストを含むクラウドが 1 つ以上存在する。 | VMM サーバーで、Site Recovery プロバイダーをインストールして Site Recovery でレプリケーションを調整し、サーバーを Recovery Services コンテナーに登録します。
**Hyper-V ホスト** | VMM で管理されている 1 つ以上の Hyper-V ホスト/クラスター。 |  各ホストまたはクラスター メンバーに Recovery Services エージェントをインストールします。
**Hyper-V VM** | Hyper-V ホスト サーバー上で実行されている 1 つ以上の VM。 | VM に明示的にインストールする必要があるものはありません。
**ネットワーク** |VMM サーバー上に設定された論理ネットワークおよび VM ネットワーク。 VM ネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。 | VM ネットワークは、Azure 仮想ネットワークにマップされているため、フェールオーバー後に Azure VM が作成されると、ネットワークに配置されます。

これらのコンポーネントをデプロイするための前提条件と要件については、[サポート マトリックス](site-recovery-support-matrix-to-azure.md)を参照してください。


**図 1: VMM クラウド内の Hyper-V ホスト上の VM を Azure にレプリケートする**

![コンポーネント](./media/vmm-to-azure-walkthrough-architecture/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>レプリケーション プロセス

**図 2: Azure への Hyper-V レプリケーションのレプリケーションと回復のプロセス**

![ワークフロー](./media/vmm-to-azure-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>保護を有効にする

1. Hyper-V VM の保護を有効にした後、Azure Portal またはオンプレミスで、**保護の有効化**が始まります。
2. このジョブは、マシンが前提条件を満たしていることを確認してから、構成された設定を使用してレプリケーションをセットアップするために、[CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) を呼び出します。
3. ジョブが [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) メソッドを呼び出して初期レプリケーションを開始し、完全 VM レプリケーションを初期化して、VM の仮想ディスクを Azure に送信します。
4. ジョブは **[ジョブ]** タブで監視できます。    ![ジョブ一覧](media/vmm-to-azure-walkthrough-architecture/image1.png) ![[保護を有効にする] の詳細](media/vmm-to-azure-walkthrough-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>初期データをレプリケーする

1. 初期レプリケーションがトリガーされると、[Hyper-V VM スナップショット](https://technet.microsoft.com/library/dd560637.aspx)が作成されます。
2. 仮想ハード ディスクは、すべてが Azure にコピーされるまで、1 つずつレプリケートされます。 VM サイズとネットワーク帯域幅によっては、しばらく時間がかかる場合があります。 ネットワーク使用量の最適化については、 [Azure 保護ネットワークの帯域幅使用量のオンプレミスでの管理方法](https://support.microsoft.com/kb/3056159)に関するページを参照してください。
3. 初期レプリケーションの進行中にディスクの変更が発生した場合、Hyper-V レプリカ レプリケーション トラッカーはそれらの変更を Hyper-V レプリケーション ログ (.hrl) として追跡します。 これらのファイルは、ディスクと同じフォルダーに配置されます。 各ディスクには関連付けられた .hrl ファイルが存在し、これらはセカンダリ ストレージに送信されます。
4. 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されます。
5. 初期レプリケーションが完了すると、VM スナップショットは削除されます。 ログの差分ディスク変更は、親ディスクに同期され、マージされます。


### <a name="finalize-protection"></a>保護の最終処理

1. 初期レプリケーションが終了すると、"**バーチャル マシンの保護の最終処理**" ジョブによって、ネットワークなどのレプリケーション後の設定が構成され、仮想マシンが保護されます。
    ![保護の最終処理ジョブ](media/vmm-to-azure-walkthrough-architecture/image3.png)
2. Azure にレプリケートする場合は、仮想マシンの設定を微調整して、いつでもフェールオーバーできるようにしておくことが必要な場合があります。 この時点で、テスト フェールオーバーを実行して、すべてが想定通りに動作しているかを確認できます。

### <a name="replicate-the-delta"></a>デルタをレプリケートする

1. 初期レプリケーション後は、レプリケーションの設定に従って差分の同期が開始されます。
2. Hyper-V レプリカのレプリケーション トラッカーは、仮想ハード ディスクへの変更を .hrl ファイルとして追跡します。 レプリケーション用に構成された各ディスクには、関連付けられた .hrl ファイルがあります。 このログは、初期レプリケーションの完了後、顧客のストレージ アカウントに送信されます。 ログが Azure に送信される間、プライマリ ディスクでの変更は、同じディレクトリ内の別のログ ファイルで追跡されます。
3. 初期レプリケーションおよび差分レプリケーション中は、VM ビューで VM を監視できます。 [詳細情報](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="synchronize-replication"></a>レプリケーションを同期する

1. 差分レプリケーションに失敗した場合、完全なレプリケーションが帯域幅または時間の観点からコスト高になるときは、VM に再同期のマークが付けられます。 たとえば、.hrl ファイルがディスク サイズの 50% に達した場合、VM には再同期のマークが付けられます。
2.  再同期では、ソースとターゲットの仮想マシンのチェックサムを計算して差分データのみを送信することで、送信されるデータの量が最小限に抑えられます。 再同期では、ソース ファイルとターゲット ファイルを固定チャンクに分割する固定ブロック チャンク アルゴリズムが使用されます。 チャンクごとにチェックサムを生成し、比較することによって、ソース側のどのブロックをターゲットに適用すべきかが判断されます。
3. 再同期が完了すると、通常の差分レプリケーションが再開されます。 既定では再同期は業務時間外に自動的に実行するようにスケジュールされますが、手動で仮想マシンを再同期することもできます。 たとえば、ネットワークの停止など、なんらかの機能不全が発生した場合は、再同期を再開することができます。 そのためには、ポータルで VM を選択し、**[再同期]** を選択します。

    ![Manual resynchronization](media/vmm-to-azure-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>再試行ロジック

レプリケーション エラーが発生した場合に備えて、組み込み再試行があります。 このロジックは、次の 2 つのカテゴリに分類できます。

**カテゴリ** | **詳細**
--- | ---
**回復不可能なエラー** | 再試行は行われません。 VM の状態が **[重大]** になり、管理者による操作が必要になります。 たとえば、VHD チェーンの破損、レプリカ VM の無効な状態、ネットワーク認証エラー、承認エラー、VM 未検出エラー (スタンドアロン Hyper-V サーバーの場合) などのエラーがあります
**回復可能なエラー** | レプリケーション間隔ごとに、再試行が行われます。再試行の間隔は、最初の試行が開始された後、指数関数的バックオフを使用して、1、2、4、8、および 10 分ずつ長くなります。 エラーが解決しない場合は、30 分ごとに再試行してください。 エラーの例として、ネットワーク エラー、ディスク領域不足エラー、メモリ不足状態などがあります |



## <a name="failover-and-failback-process"></a>フェールオーバーとフェールバックのプロセス

1. 計画または計画外[フェールオーバー](site-recovery-failover.md)をオンプレミスの Hyper-V VM から Azure に実行できます。 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。
2. 単一のマシンをフェールオーバーするか、複数のマシンのフェールオーバーを調整するための[復旧計画](site-recovery-create-recovery-plans.md)を作成することができます。
4. フェールオーバーを実行すると、Azure に作成されたレプリカ VM が表示されるようになります。 必要に応じて、VM にパブリック IP アドレスを割り当てることができます。
5. その後、フェールオーバーをコミットして、レプリカの Azure VM からワークロードへのアクセスを開始します。
6. プライマリ オンプレミス サイトが再度使用可能になると、[フェールバック](site-recovery-failback-from-azure-to-hyper-v.md)できます。 Azure からプライマリ サイトへの計画フェールオーバーを開始します。 計画フェールオーバーの場合、データが失われないように、同じ VM と別の場所のどちらにフェールバックするかを選択し、Azure とオンプレミスの間で変更を同期することができます。 VM がオンプレミスで作成されたら、フェールオーバーをコミットします。




## <a name="next-steps"></a>次のステップ

[手順 2. のデプロイの前提条件の確認](vmm-to-azure-walkthrough-prerequisites.md)に関するページに進んでください。
