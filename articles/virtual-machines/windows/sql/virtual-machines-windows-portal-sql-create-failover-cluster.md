---
title: SQL Server FCI - Azure Virtual Machines | Microsoft Docs
description: "この記事では、Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンスを作成する方法について説明します。"
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 6f0fe474787efc15db5c75266cde369725832aab
ms.lasthandoff: 03/18/2017


---

# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンスを構成します。

この記事では、Resource Manager モデルを使用して、Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 このソリューションでは、ストレージ (データ ディスク) を Windows クラスター内のノード (Azure VM) 間で同期させるためのソフトウェア ベースの仮想 SAN として、[Windows Server 2016 Datacenter Edition 記憶域スペース ダイレクト \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) を使用します。 S2D は、Windows Server 2016 の新機能です。

次の図は、Azure Virtual Machines での完全なソリューションを示しています。

![可用性グループ](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

この図は、次を示しています。

- Windows フェールオーバー クラスター内の 2 つの Azure 仮想マシン。 仮想マシンがフェールオーバー クラスター内にある場合、仮想マシンは "*クラスター ノード*" または "*ノード*" とも呼ばれます。
- 各仮想マシンには、2 つ以上のデータ ディスクがあります。
- S2D は、データ ディスク上のデータを同期し、同期されたストレージを記憶域プールとして提供します。 
- 記憶域プールは、クラスターの共有ボリューム (CSV) をフェールオーバー クラスターに提供します。
- SQL Server FCI のクラスター ロールは、CSV をデータ ドライブ用に使用します。 
- SQL Server FCI の IP アドレスを保持するための Azure ロード バランサー。
- Azure 可用性セットにより、すべてのリソースが保持されます。

   >[!NOTE]
   >この図では、すべての Azure リソースが同じリソース グループに含まれています。

S2D の詳細については、[Windows Server 2016 Datacenter Edition 記憶域スペース ダイレクト \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) に関する記事を参照してください。 

S2D では、コンバージド型とハイパー コンバージド型の 2 種類のアーキテクチャがサポートされています。 このドキュメントでのアーキテクチャは、ハイパー コンバージド型です。 ハイパー コンバージド インフラストラクチャでは、クラスター化されたアプリケーションをホストしている同じサーバーにストレージが配置されます。 このアーキテクチャでは、ストレージは各 SQL Server FCI ノード上にあります。

### <a name="example-azure-template"></a>Azure テンプレートの例

このソリューション全体を、Azure でテンプレートから作成できます。 テンプレートの例は、GitHub の[Azure クイック スタート テンプレート](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)から使用できます。 この例は、特定のワークロード向けに設計およびテストされたものではありません。 このテンプレートを実行すれば、ドメインに接続された S2D ストレージで SQL Server FCI を作成できます。 このテンプレートは、評価のうえ、目的に応じた変更が可能です。 

## <a name="before-you-begin"></a>開始する前に

知っておく必要がある点と、実行前に用意が必要なことがいくつかあります。

### <a name="what-to-know"></a>必要な知識
次のテクノロジについて、運用上の理解が必要です。

- [Windows クラスター テクノロジ](http://technet.microsoft.com/library/hh831579.aspx)
-  [SQL Server フェールオーバー クラスター インスタンス](http://msdn.microsoft.com/library/ms189134.aspx) 

さらに、次のテクノロジの概要について理解しておくことが必要です。

- [Windows Server 2016 の記憶域スペース ダイレクトを使用したハイパー コンバージド ソリューション](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure リソース グループ](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>必要なもの

この記事の手順を実行する前に、次のものが必要です。

- Microsoft Azure サブスクリプションが必要です。
- Azure Virtual Machines 上の Windows ドメイン。
- Azure 仮想マシン内にオブジェクトを作成する権限を持つアカウント。
- 十分な IP アドレス空間を持つ、次のコンポーネント用の Azure 仮想ネットワークとサブネット。
   - 両方の仮想マシン。
   - フェールオーバー クラスターの IP アドレス。
   - 各 FCI の IP アドレス。
- Azure ネットワーク上で構成された、ドメイン コントローラーを指す DNS。 

これらの前提条件が整ったら、フェールオーバー クラスターの構築を開始できます。 最初の手順で、仮想マシンを作成します。 

## <a name="step-1-create-virtual-machines"></a>手順 1. 仮想マシンを作成する

1. お使いのサブスクリプションで、[Azure Portal](http://portal.azure.com) にログインします。

1. [Azure 可用性セットを作成します](../../virtual-machines-windows-create-availability-set.md)。

   可用性セットを利用すると、障害ドメインと更新ドメインの仮想マシンがグループ化されます。 可用性セットがあると、アプリケーションがネットワーク スイッチ、サーバーのラックの電源装置など、単一障害点の影響を受けないようにすることができます。 

   仮想マシンのリソース グループを作成していない場合、これは Azure 可用性セットの作成時に行います。 可用性セットの作成に Azure Portal を使用する場合は、次の手順を実行します。

   - Azure Portal で **[+]** をクリックし、Azure Marketplace を開きます。 "**可用性セット**" を検索します。
   - **[可用性セット]** をクリックします。
   - **[作成]**をクリックします。
   - **[可用性セットの作成]** ブレードで、次の値を設定します。
      - **[名前]**: 可用性セットの名前。 
      - **[サブスクリプション]**: Azure のサブスクリプション。
      - **[リソース グループ]**: 既存のグループを使用する場合は、**[既存のものを使用]** をクリックし、ドロップダウン リストからグループを選択します。 または、**[新規作成]** を選択し、グループの名前を入力します。
      - **[場所]**: 仮想マシンを作成する予定の場所を設定します。 
      - **[障害ドメイン]**: 既定値 (3) を使用します。
      - **[更新ドメイン]**: 既定値 (5) を使用します。
   - **[作成]** をクリックして可用性セットを作成します。 

1. 可用性セット内に仮想マシンを作成します。

   Azure 可用性セット内に 2 つの SQL Server 仮想マシンをプロビジョニングします。 手順については、「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。 

   両方の仮想マシンを配置します。
   
   - 可用性セットと同じ Azure リソース グループ内。 
   - ドメイン コントローラーと同じネットワーク内。
   - 両方の仮想マシン、およびこのクラスター上で最終的に使用するすべての FCI 用に十分な IP アドレス空間を持つサブネット内。 
   - Azure 可用性セット内。   

      >[!IMPORTANT]
      >仮想マシンを作成した後に可用性セットを設定または変更することはできません。

   Azure Marketplace からイメージを選択します。 Windows Server と SQL Server、または Windows Server だけを含む Marketplace イメージを使用できます。 詳細については、「[Azure Virtual Machines における SQL Server の概要](../../virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。
   
   Azure ギャラリーの SQL Server の公式イメージには、インストール済みの SQL Server インスタンス、SQL Server のインストール ソフトウェア、必要なキーが含まれます。 
   
   SQL Server ライセンスのご希望の課金方法に従って、適切なイメージを選択します。 

   - **従量課金のライセンス**: これらのイメージの 1 分あたりのコストに SQL Server のライセンスが含まれます。
      - **SQL Server 2016 Enterprise on Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard on Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer on Windows Server Datacenter 2016**
   
   - **ライセンス持ち込み (BYOL)**
   
      - **{BYOL} SQL Server 2016 Enterprise on Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard on Windows Server Datacenter 2016** 
   
   >[!IMPORTANT]
   >仮想マシンを作成した後、事前にインストールされていたスタンドアロンの SQL Server インスタンスを削除します。 フェールオーバー クラスターと S2D を構成したら、プレインストールされた SQL Server メディアを使用して SQL Server FCI を作成します。 

   または、オペレーティング システムだけを含む Azure Marketplace イメージを使用することができます。 フェールオーバー クラスターと S2D の構成後、**Windows Server 2016 Datacenter** イメージを選択し、SQL Server FCI をインストールします。 このイメージには、SQL Server インストール メディアは含まれません。 各サーバーへの SQL Server のインストールを実行できる場所に、インストール メディアを配置します。

1. Azure によって仮想マシンが作成されたら、RDP で各仮想マシンに接続します。 

   RDP で最初に仮想マシンに接続する際、コンピューターによって、この PC をネットワーク上で検出可能にするかどうかが確認されます。 **[はい]**をクリックします。 

1. SQL Server ベースの仮想マシン イメージの 1 つを使用している場合は、SQL Server インスタンスを削除します。

   - **[プログラムと機能]** で **[Microsoft SQL Server 2016 (64 ビット)]** を右クリックし、**[アンインストールと変更]** をクリックします。 
   - **[削除]**をクリックします。 
   - 既定のインスタンスを選択します。
   - **[データベース エンジン サービス]** のすべての機能を削除します。 **[共有機能]** は削除しないでください。 次の図を参照してください。

      ![機能を削除する](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - **[次へ]** をクリックし、**[削除]** をクリックします。

1. <a name="ports"></a>ファイアウォール ポートを開きます。
   
   各仮想マシンで、Windows ファイアウォールの次のポートを開きます。 

   | 目的 | TCP ポート | メモ
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server の既定のインスタンスの通常のポートです。 ギャラリーからイメージを使用した場合、このポートが自動的に開きます。 
   | 正常性プローブ | 59999 | 開いている任意の TCP ポートです。 後の手順で、このポートを使用するようにロード バランサーの[正常性プローブ](#probe)とクラスターを構成します。  

1. 仮想マシンにストレージを追加します。 詳細については、[ストレージの追加](../../../storage/storage-premium-storage.md)に関するページを参照してください。

   両方の仮想マシンに、少なくとも 2 つのデータ ディスクが必要です。

   NTFS でフォーマットされたディスクではなく、フォーマットされていないディスクを接続します。 
      >[!NOTE]
      >NTFS でフォーマットされたディスクを接続する場合、ディスクの適格性チェックをしない S2D しか有効にできません。  
   
   最小で 2 つの Premium Storage (SSD ディスク) を各 VM に接続します。 少なくとも P30 (1 TB) のディスクをお勧めします。

   [ホスト キャッシュ] を **[読み取り専用]** に設定します。

   運用環境で使用するストレージ容量はワークロードによって異なります。 この記事で説明されている値は、デモおよびテスト用です。 

1. [既存のドメインに仮想マシンを追加します](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

仮想マシンを作成して構成したら、フェールオーバー クラスターを構成できます。

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>手順 2. S2D が有効な Windows フェールオーバー クラスターを構成する

次に、S2D が有効なフェールオーバー クラスターを構成します。 この手順では、次のサブ手順を実行します。

1. Windows フェールオーバー クラスタリング機能を追加する
1. クラスターを検証する
1. フェールオーバー クラスターを作成する
1. クラウド監視を作成する
1. ストレージを追加する

### <a name="add-windows-failover-clustering-feature"></a>Windows フェールオーバー クラスタリング機能を追加する

1. これを開始するには、ローカルの Administrators のメンバーであり、かつ Active Directory でオブジェクトを作成するアクセス許可を持つドメイン アカウントを使用して、RDP で最初の仮想マシンに接続します。 このアカウントを使用して、構成を進めます。

1. [各仮想マシンにフェールオーバー クラスタリング機能を追加します](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-cluster-features-to-both-sql-servers)。

   UI からフェールオーバー クラスタリング機能をインストールするには、両方の仮想マシンで次の手順を実行します。 
   - **[サーバー マネージャー]** で、**[管理]**、**[役割と機能の追加]** の順にクリックします。 
   - **役割と機能の追加ウィザード**で、**[機能の選択]** ページが表示されるまで **[次へ]** をクリックします。
   - **[機能の選択]** で **[フェールオーバー クラスタリング]** をクリックします。 必要なすべての機能と管理ツールを含めます。 **[機能の追加]** をクリックします。
   - **[次へ]** をクリックし、**[完了]** をクリックして、機能をインストールします。 

   PowerShell を使用したフェールオーバー クラスタリング機能をインストールするには、いずれかの仮想マシン上の管理者 PowerShell セッションから次のスクリプトを実行します。

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

次の手順は「[Windows Server 2016 で記憶域スペース ダイレクトを使用するハイパーコンバージド ソリューション](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)」の手順 3. の説明に従ったものですので、参考にしてください。 

### <a name="validate-the-cluster"></a>クラスターを検証する

このガイドは、[クラスターの検証](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)に関するセクションの手順に沿っています。

UI または PowerShell を使用して、クラスターを検証します。 

UI を使用してクラスターを検証するには、いずれかの仮想マシンから次の手順を実行します。 

1. **[サーバー マネージャー]** で、**[ツール]**、**[フェールオーバー クラスター マネージャー]** の順にクリックします。 
1. **[フェールオーバー クラスター マネージャー]** で、**[操作]** をクリックし、**[構成の検証...]** をクリックします。
1. **[次へ]**をクリックします。 
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、**[選択するテストのみを実行する]** を選択します。 **[次へ]**をクリックします。
1. **[テストの選択]** で、**[ストレージ]** 以外のすべてのテストを含めます。 次の図を参照してください。

   ![テストを検証する](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)
   
1. **[次へ]**をクリックします。
1. **[確認]** で**[次へ]** をクリックします。 

**構成の検証ウィザード**により、検証テストが実行されます。 

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

クラスターの検証後、フェールオーバー クラスターを作成します。

### <a name="create-the-failover-cluster"></a>フェールオーバー クラスターを作成する

このガイドは、[フェールオーバー クラスターの作成](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster)に関するセクションの手順に沿っています。

フェールオーバー クラスターを作成するには、以下が必要です。 
- クラスター ノードになる仮想マシンの名前。 
- フェールオーバー クラスターの名前。
- フェールオーバー クラスターの IP アドレス。 クラスター ノードと同じ Azure 仮想ネットワークおよびサブネットでは使用されていない IP アドレスを使用することができます。 

次の PowerShell を実行すると、フェールオーバー クラスターが作成されます。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。 

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>クラウド監視を作成する

クラウド監視とは、Azure Storage Blob に格納されている、新しい種類のクラスター クォーラム監視です。 これにより、監視の共有をホストする個別の VM が不要になります。

1. [フェールオーバー クラスターのクラウド監視を作成](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)します。 

1. BLOB コンテナーを作成します。 

1. アクセス キーと、コンテナーの URL を保存します。

1. フェールオーバー クラスターのクラスター クォーラム監視を構成します。 UI で [ユーザー インターフェイスでのクォーラム監視の構成](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)に関するページを参照してください。

### <a name="add-storage"></a>ストレージを追加する

S2D 用のディスクは、空で、パーティションやその他のデータもない状態である必要があります。 ディスクを消去するには、[このガイドの手順](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks)に従います。
   
1. [記憶域スペース ダイレクト \(S2D\) を有効化します](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   次の PowerShell を実行すると、記憶域スペース ダイレクトが有効化されます。  

   ```PowerShell
   Enable-ClusterS2D
   ```

   **[フェールオーバー クラスター マネージャー]** に、記憶域プールが表示されるようになります。

1. [ボリュームを作成します](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   S2D の機能の 1 つに、ユーザーが有効化した場合に、記憶域プールを自動的に作成するというものがあります。 これでボリュームを作成する準備が整いました。 PowerShell コマンドレット `New-Volume` は、書式設定、クラスターへの追加、クラスターの共有ボリューム (CSV) 作成などのボリューム作成プロセスを自動化するものです。 次の例では、800 ギガバイト (GB) の CSV を作成します。 

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   このコマンドが完了すると、800 GB のボリュームがクラスター リソースとしてマウントされます。 ボリュームは `C:\ClusterStorage\Volume1\` にあります。

   次の図は、S2D が有効なクラスターの共有ボリュームを示しています。

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>手順 3. クラスター フェールオーバーのテスト フェールオーバーを実行する

フェールオーバー クラスター マネージャーで、ストレージ リソースを他のクラスター ノードに移動できることを確認します。 **フェールオーバー クラスター マネージャー**を使用してフェールオーバー クラスターに接続し、ノード間でストレージを移動できれば、FCI を構成することができます。 

## <a name="step-4-create-sql-server-fci"></a>手順 4. SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。 

1. RDP で最初の仮想マシンに接続します。 

1. **フェールオーバー クラスター マネージャー**で、すべてのクラスター コア リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。 

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 **[Setup]**をクリックします。

1. **[SQL Server インストール センター]** で、**[インストール]** をクリックします。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** をクリックします。 ウィザードの指示に従って、SQL Server FCI をインストールします。 

   FCI のデータ ディレクトリは、クラスター化されたストレージ上にある必要があります。 S2D では、これは共有ディスクではなく、各サーバー上のボリュームのマウント ポイントです。 S2D は、両方のノード間でボリュームを同期します。 ボリュームは、クラスターの共有ボリュームとしてクラスターに表示されます。 データ ディレクトリとして CSV のマウント ポイントを使用します。 

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. ウィザードを完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。 

1. セットアップで FCI が最初のノードに正常にインストールされたら、RDP で 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開きます。 **[インストール]** をクリックします。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** をクリックします。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server で Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 このイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 「[SQL Server Management Studio (SSMS) のダウンロード](http://msdn.microsoft.com/library/mt238290.aspx)」を参照してください。

## <a name="step-5-create-azure-load-balancer"></a>手順 5. Azure ロード バランサーを作成する

Azure 仮想マシンでは、クラスターは、一度に 1 つのクラスター ノードに存在する必要がある IP アドレスを保持するためにロード バランサーを使用します。 このソリューションでは、ロード バランサーは SQL Server FCI の IP アドレスを保持します。 

[Azure のロード バランサーを作成し、構成します](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal でロード バランサーを作成する

ロード バランサーを作成するには、次の手順を実行します。

1. Azure Portal で、仮想マシンが含まれているリソース グループに移動します。

1. **[+ 追加]** をクリックします。 Marketplace で "**ロード バランサー**" を検索します。 **[ロード バランサー]** をクリックします。

1. **[作成]**をクリックします。 

1. 次の項目を入力して、ロード バランサーを構成します。

   - **[名前]**: ロード バランサーを識別する名前。 
   - **[Type (タイプ)]**: ロード バランサーのタイプとして、パブリックまたはプライベートのどちらかを選ぶことができます。 プライベート ロード バランサーには、同じ VNET 内からアクセスできます。 プライベート ロード バランサーは、ほとんどの Azure アプリケーションで使用できます。 アプリケーションがインターネット経由で直接 SQL Server にアクセスする必要がある場合は、パブリック ロード バランサーを使用します。 
   - **[仮想ネットワーク]**: 仮想マシンと同じネットワーク。 
   - **[サブネット]**: 仮想マシンと同じサブネット。 
   - **[プライベート IP アドレス]**: SQL Server FCI クラスターのネットワーク リソースに割り当てたものと同じ IP アドレス。
   - **[サブスクリプション]**: Azure のサブスクリプション。
   - **[リソース グループ]**: 仮想マシンと同じリソース グループを使用します。 
   - **[場所]**: 仮想マシンと同じ Azure の場所を使用します。 
   次の図を参照してください。

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
  
### <a name="configure-the-load-balancer-backend-pool"></a>ロード バランサーのバックエンド プールを構成する 

1. 仮想マシンが含まれている Azure リソース グループに戻り、新しいロード バランサーを探します。 リソース グループの表示を更新する必要がある場合があります。 ロード バランサーをクリックします。 

1. [ロード バランサー] ブレードで **[バックエンド プール]** をクリックします。 

1. **[+ 追加]** をクリックして、バックエンド プールを追加します。

1. バックエンド プールの名前を入力します。

1. **[+ 仮想マシンの追加]** をクリックします。

1. **[仮想マシンの選択]** ブレードで **[可用性セットの選択]** をクリックします。

1. SQL Server 仮想マシンを配置した可用性セットを選択します。 

1. **[仮想マシンの選択]** ブレードで、**[仮想マシンの選択]** をクリックします。 

   Azure Portal は、次の図のように表示されます。

   ![CreateLoadBalancerBackEnd](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. **[仮想マシンの選択]** ブレードで **[選択]** をクリックします。

1. **[OK]** を 2 回クリックします。 

### <a name="configure-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブを構成する

1. [ロード バランサー] ブレードで **[Health probes (正常性のプローブ)]** をクリックします。 

1. **[+ 追加]** をクリックします。 

1. **[Add health probe (正常性プローブの追加)]** ブレードで、<a name="probe"></a>正常性プローブのパラメーターを設定します。

   - **[名前]**: 正常性プローブの名前。
   - **[プロトコル]**: TCP。
   - **[ポート]**: 使用可能な TCP ポートに設定します。 このポートには、開かれたファイアウォール ポートが必要です。 ファイアウォールで正常性プローブに設定したのと[同じポート](#ports)を使用します。 
   - **[間隔]**: 5 秒。
   - **[異常しきい値]**: 連続エラー数 2。

1. [OK] をクリックします。

### <a name="set-load-balancing-rules"></a>負荷分散規則を設定する

1. [ロード バランサー] ブレードで 、**[負荷分散規則]** をクリックします。 

1. **[+ 追加]** をクリックします。

1. 次のように負荷分散規則のパラメーターを設定します。

   - **[名前]**: 負荷分散規則の名前。
   - **[Frontend IP address (フロントエンド IP アドレス)]**: SQL Server FCI クラスターのネットワーク リソースの IP アドレスを使用します。 
   - **[ポート]**: SQL Server FCI の TCP ポートに設定します。 既定のインスタンス ポートは 1433 です。 
   - **[バックエンド ポート]**: この値には、**[フローティング IP (ダイレクト サーバー リターン)]** を有効にしたときの **[ポート]** の値と同じポートを使用します。
   - **[バックエンド プール]**: 先ほど構成したバックエンド プール名を使用します。 
   - **[Health probe (正常性のプローブ)]**: 先ほど構成した正常性プローブを使用します。
   - **[セッション永続化]**: なし。
   - **[アイドル タイムアウト (分)]**: 4。
   - **[フローティング IP (ダイレクト サーバー リターン)]**: 有効にします。

1. **[OK]**をクリックします。 

## <a name="step-6-configure-cluster-for-probe"></a>手順 6. プローブのクラスターを構成する

PowerShell でクラスターのプローブ ポート パラメーターを設定します。

クラスターのプローブ ポート パラメーターを設定するには、お使いの環境から次のスクリプトで変数を更新します。 

  ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "IP Address Resource Name" # the IP Address cluster resource name.
   $ILBIP = "<10.0.0.x>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <59999>
   
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```


## <a name="step-7-test-fci-failover"></a>手順 7. FCI フェールオーバーをテストする

FCI のフェールオーバーをテストして、クラスターの機能を検証します。 次の手順を実行します。

1. SQL Server FCI クラスターのノードの 1 つに RDP で接続します。

1. **フェールオーバー クラスター マネージャー**を開きます。 **[ロール]** をクリックします。 SQL Server FCI ロールを所有しているノードを確認します。 

1. SQL Server FCI ロールを右クリックします。 

1. **[移動]** をクリックし、**[最適なノード]** をクリックします。

**フェールオーバー クラスター マネージャー**で、ロールとそのリソースがオフラインになったことが示されます。 リソースは移動し、もう一方のノードでオンラインになります。 

### <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにログインします。 **SQL Server Management Studio** を開き、SQL Server FCI 名に接続します。 

>[!NOTE]
>必要に応じて、[SQL Server Management Studio をダウンロード](http://msdn.microsoft.com/library/mt238290.aspx)できます。

## <a name="limitations"></a>制限事項
Azure 仮想マシンでは、Microsoft の分散トランザクション コーディネーター (DTC) はFCI 上でサポートされていません。これは、RPC ポートがロード バランサーによってサポートされていないためです。

## <a name="see-also"></a>関連項目

[リモート デスクトップでの S2D のセットアップ (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment) 

[記憶域スペース ダイレクトを使用するハイパーコンバージド ソリューション](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[記憶域スペース ダイレクトの概要](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server での S2D のサポート](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)


