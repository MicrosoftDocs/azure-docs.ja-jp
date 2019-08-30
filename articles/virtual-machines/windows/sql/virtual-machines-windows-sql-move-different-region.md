---
title: Azure Site Recovery サービスを使用して Azure 内の別のリージョンに SQL Server VM を移動する |Microsoft Docs
description: Azure 内のあるリージョンから別のリージョンに SQL Server 仮想マシンを移行する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 063876316c92780d061388283a55c7f50dd3d78a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100521"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Azure Site Recovery サービスを使用して Azure 内の別のリージョンに SQL Server VM を移動する

この記事では、Azure Site Recovery を使用して、Azure 内のあるリージョンから別のリージョンに SQL Server 仮想マシン (VM) を移行する方法について説明します。 

SQL Server VM を別のリージョンに移動するには、次の手順を実行する必要があります。
1. [**準備**](#prepare-to-move):ソースの SQL Server VM とターゲット リージョンの両方が、移動用に適切に準備されていることを確認します。 
1. [**構成**](#configure-azure-site-recovery-vault):SQL Server VM を移動するには、それが Azure Site Recovery コンテナー内のレプリケートされたオブジェクトである必要があります。 Azure Site Recovery コンテナーに SQL Server VM を追加する必要があります。 
1. [**テスト**](#test-move-process):SQL Server VM を移行するには、それをソース リージョンから、レプリケートされたターゲット リージョンにフェールオーバーする必要があります。 移動プロセスが正常に行われるようにするには、まず、SQL Server VM がターゲット リージョンに正常にフェールオーバーできることをテストする必要があります。 これにより、問題点が明らかになって、実際の移動を実行するときにそれらを回避することができます。 
1. [**移動**](#move-the-sql-server-vm):テスト フェールオーバーが成功して、SQL Server VM を移行しても安全であることがわかったら、ターゲット リージョンへの VM の移動を実行できます。 
1. [**クリーンアップ**](#clean-up-source-resources):課金を回避するには、SQL Server VM をコンテナーから削除し、リソース グループに残されている不要なリソースをすべて削除します。 

## <a name="verify-prerequisites"></a>前提条件を確認する 

- ソース リージョンからターゲット リージョンへの移動が[サポートされている](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)ことを確認します。  
- [シナリオのアーキテクチャとコンポーネント](../../../site-recovery/azure-to-azure-architecture.md)、および[サポートの制限事項と要件](../../../site-recovery/azure-to-azure-support-matrix.md)を確認します。 
- アカウントのアクセス許可を確認する。 自分で無料の Azure アカウントを作成した場合、自分がそのサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 VM のレプリケーションを有効にし、Azure Site Recovery を使用してデータをコピーするには、次のものが必要です。 
    - VM を作成するためのアクセス許可。 "*仮想マシン共同作成者*" 組み込みロールには次のアクセス許可が与えられています。 
        - 選択したリソース グループ内に VM を作成するためのアクセス許可。 
        - 選択した仮想ネットワーク内に VM を作成するためのアクセス許可。 
        - 選択したストレージ アカウントに書き込むためのアクセス許可。 
      - Azure Site Recovery の操作を管理するためのアクセス許可。 *Site Recovery 共同作成者*ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なアクセス許可がすべて付与されています。  

## <a name="prepare-to-move"></a>移動の準備をする
ソース SQL Server VM とターゲット リージョンの両方で移動の準備をします。 

### <a name="prepare-the-source-sql-server-vm"></a>ソース SQL Server VM を準備する

- 移動する SQL Server VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書がない場合は、セキュリティ上の制約により、ターゲット リージョンへのデータのコピーが禁止されます。 
- Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。 
- Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。 
- 移動する VM のネットワーク接続を制御するために認証プロキシを使用していないことを確認します。 
- 移動しようとしている VM にインターネットへのアクセスが存在しない場合や、ファイアウォール プロキシを使用してアウトバウンド アクセスを制御している場合は、要件を確認してください。 
- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 これにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。 

### <a name="prepare-the-target-region"></a>ターゲット リージョンを準備する

- 自分の Azure サブスクリプションで、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。 
- 自分のサブスクリプションに、ソース VM に一致するサイズの VM をサポートできるだけのリソースがあることを確認します。 Site Recovery を使用してターゲットにデータをコピーする場合は、Site Recovery によって、ターゲット VM に対して同じサイズか、可能な限り近いサイズが選択されます。 
- ソース ネットワーク レイアウトから特定されたすべてのコンポーネントについてターゲット リソースを作成します。 この手順は、ソース リージョンにあったすべての機能をターゲット リージョンの VM に確保するうえで重要となります。 
    - ソース VM のレプリケーションを有効にすると、Azure Site Recovery によって仮想ネットワークが自動的に検出されて作成されます。 また、自分でネットワークを事前に作成しておき、レプリケーションを有効にするユーザー フローの中で VM に割り当てることもできます。 その他のリソースについては、ターゲット リージョンに手動で作成する必要があります。
- 実際の環境に適した、最も一般的に使用されるネットワーク リソースをソース VM の構成に基づいて作成する場合は、次のドキュメントを参照してください。 
    - [ネットワーク セキュリティ グループ](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Load Balancer](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [パブリック IP アドレス](../../../virtual-network/virtual-network-public-ip-address.md)
    - その他のネットワーク コンポーネントについては、[ネットワークに関するドキュメント](../../../virtual-network/virtual-networks-overview.md)を参照してください。
- ターゲット リージョンへの最終的な移動を行う前に構成をテストしたい場合は、ターゲット リージョンに手動で非運用ネットワークを作成します。 運用環境のネットワークへの影響が最小限で済むため、この手順をお勧めします。 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery コンテナーを構成する

以下の手順では、Azure Site Recovery を使用してターゲット リージョンにデータをコピーする方法を紹介しています。 ソース リージョン以外のリージョンに Recovery Services コンテナーを作成します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. ナビゲーション ウィンドウの左上隅から **[リソースの作成]** を選択します。 
1. **[IT & Management tools]\(IT & 管理ツール\)** を選択し、次に **[Backup and Site Recovery]\(バックアップおよびサイトの回復\)** を選択します。 
1. **[基本]** タブの **[プロジェクトの詳細]** で、ターゲット リージョンに新しいリソース グループを作成するか、ターゲット リージョンで既存のリソース グループを選択します。 
1. **[インスタンスの詳細]** で、コンテナーの名前を指定し、次にドロップダウンからターゲット **リージョン**を選択します。 
1. **[確認と作成]** を選択して、Recovery Services コンテナーを作成します。 
1. ナビゲーション ウィンドウの左上隅から **[すべてのサービス]** を選択し、検索ボックスに「`recovery services`」と入力します。 
1. (省略可能) **[Recovery Services コンテナー]** の横の星を選択して、クイック ナビゲーション バーに追加します。 
1. **[Recovery Services コンテナー]** を選択し、作成した Recovery Services コンテナーを選択します。 
1. **[概要]** ウィンドウで、 **[レプリケート]** を選択します。 

   ![レプリケーションの構成](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. **[ソース]** を選択し、次にソースとして **[Azure]** を選択します。 ソース VM の場所など、他のドロップダウン フィールドに適切な値を選択します。 **[ソースの場所]** リージョンにあるリソース グループのみが、 **[ソース リソース グループ]** フィールドに表示されます。 
1. **[仮想マシン]** を選択し、移行する仮想マシンを選択します。 **[OK]** を選択して VM の選択を保存します。 
1. **[設定]** を選択し、次にドロップダウンから**ターゲットの場所**を選択します。 これは、前に準備したリソース グループにする必要があります。 
1. レプリケーションをカスタマイズしたら、 **[ターゲット リソースの作成]** を選択して、新しい場所にリソースを作成します。 
1. リソースの作成が完了したら、 **[レプリケーションを有効にする]** を選択して、ソースからターゲット リージョンへの SQL Server VM のレプリケーションを開始します。
1. レプリケーションの状態を確認するには、Recovery コンテナーに移動し、 **[レプリケートされたアイテム]** を選択して、SQL Server VM の**状態**を表示します。 **[保護済み]** の状態は、そのレプリケーションが完了していることを示しています。 

   ![レプリケーションの状態を確認する](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>移動プロセスをテストする
以下の手順では、Azure Site Recovery を使用して移動プロセスをテストする方法を紹介します。 

1. [Azure portal](https://portal.azure.com) で **Recovery Services コンテナー** に移動し、 **[レプリケートされたアイテム]** を選択します。 
1. 移動する SQL Server VM を選択し、 **[レプリケーションの正常性]** に **[正常]** と表示されているのを確認して、 **[テスト フェールオーバー]** を選択します。 

   ![VM のフェールオーバーをテストする](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. **[テスト フェールオーバー]** ページで、フェールオーバーに使用する**最新のアプリ整合性**復旧ポイントを選択します。それが、SQL Server のデータ整合性を保証できる唯一の種類のスナップショットです。 
1. **[Azure 仮想ネットワーク]** で仮想ネットワークを選択し、 **[OK]** を選択してフェールオーバーをテストします。 
   
   >[!IMPORTANT]
   > フェールオーバー テストには、別個の Azure VM ネットワークを使用することをお勧めします。 レプリケーションを有効にするときに設定した、VM の最終的な移動先となる運用環境のネットワークは使用しないでください。 

1. 進行状況を監視するには、コンテナーに移動し、 **[監視]** で **[Site Recovery ジョブ]** を選択し、進行中の**テスト フェールオーバー** ジョブを選択します。

   ![フェールオーバー テストの進行状況を監視する](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. テストが完了したら、ポータルで**仮想マシン**に移動して、新しく作成された仮想マシンを確認します。 SQL Server VM が実行中で、適切なサイズであることと、適切なネットワークに接続されていることを確認してください。 
1. フェールオーバー テストのリソースがクリーンアップされるまで**フェールオーバー** オプションは淡色表示されるので、テストの一部として作成された VM を削除します。 コンテナーに戻り、 **[レプリケートされたアイテム]** を選択し、SQL Server VM を選択して、 **[テストフェールオーバーのクリーンアップ]** を選択します。 **[メモ]** セクションに、テストに関連する観測を記録して保存し、 **[Testing is complete. Delete test failover virtual machines]\(テストが完了しました。テスト フェールオーバー仮想マシンを削除してください\)** の横のチェックボックスをオンにします。 **[OK]** を選択して、テスト後にリソースをクリーンアップします。 

   ![フェールオーバー テスト後にアイテムをクリーンアップする](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>SQL Server VM を移動する 
次の手順では、ソース リージョンからターゲット リージョンに SQL Server VM を移動する方法について説明します。 

1. **Recovery Services** コンテナーに移動し、 **[レプリケートされたアイテム]** を選択し、VM を選択し、 **[フェールオーバー]** を選択します。 

   ![フェールオーバーを開始する](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. **[復旧ポイント]** で、 **[最新のアプリ整合性]** 復旧ポイントを選択します。 
1. **[Shut down the machine before beginning failover]\(フェールオーバーを開始する前にマシンをシャットダウンする\)** の横のチェックボックスを選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 シャットダウンが失敗しても、フェールオーバーは続行されます。 
1. **[OK]** を選択すると、フェールオーバーが開始されます。
1. フェールオーバー プロセスは、前のセクションでフェールオーバー テストを監視していたときに表示したのと同じ **[Site Recovery ジョブ]** ページから監視できます。 
1. ジョブが完了したら、予想どおりに SQL Server VM がターゲット リージョンに表示されることを確認します。 
1. コンテナーに戻り、 **[レプリケートされたアイテム]** を選択し、SQL Server VM を選択し、 **[コミット]** を選択して、ターゲット リージョンへの移動プロセスを終了します。 コミット ジョブが完了するまでお待ちください。 
1. SQL Server VM を SQL VM リソース プロバイダーに登録して、Azure portal での **SQL 仮想マシン**の管理容易性、およびリソース プロバイダーに関連した機能を有効にします。 詳細については、[SQL Server VM の SQL VM リソース プロバイダーへの登録](virtual-machines-windows-sql-register-with-rp.md)に関する記事をご覧ください。 

  > [!WARNING]
  > SQL Server のデータの整合性は、アプリ整合性スナップショットでのみ保証されます。 **処理された最新の**スナップショットは、SQL Server のフェールオーバーには使用できません。これは、クラッシュ復旧スナップショットは、SQL Server のデータの整合性を保証できないためです。 

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする
課金を回避するには、SQL Server VM をコンテナーから削除し、関連付けられている不要なリソースをすべて削除します。 

1. **Site Recovery** コンテナーに戻り、 **[レプリケートされたアイテム]** を選択して、SQL Server VM を選択します。 
1. **[レプリケーションの無効化]** を選択します。 保護を無効にする理由を選択し、 **[OK]** を選択してレプリケーションを無効にします。 

   >[!IMPORTANT]
   > Azure Site Recovery レプリケーションの請求を避けるために、この手順を実行することは重要です。 

1. ソース リージョンのリソースを再利用する予定がない場合は、関連するすべてのネットワーク リソースと、対応するストレージ アカウントを削除します。 


## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


