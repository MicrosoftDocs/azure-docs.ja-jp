---
title: 記憶域スペース ダイレクトで FCI を作成する
description: 記憶域スペース ダイレクトを使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: aa19cf6b59b1efa4b14501fbf64e319da3e4c0b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048643"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>記憶域スペース ダイレクトで FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[記憶域スペース ダイレクト](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を使用して、Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 記憶域スペース ダイレクトは、Windows クラスター内のノード (Azure VM) 間でストレージ (データ ディスク) を同期するソフトウェア ベースの仮想記憶域ネットワーク (VSAN) として機能します。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 


## <a name="overview"></a>概要 

[記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) では、コンバージド型とハイパーコンバージド型の 2 種類のアーキテクチャがサポートされています。 ハイパーコンバージド インフラストラクチャでは、SQL Server FCI ノードごとにストレージが存在するように、クラスター化されたアプリケーションをホストしている同じサーバーにストレージが配置されます。 

次の図では、Azure VM 上の SQL Server でハイパーコンバージド記憶域スペース ダイレクトを使用する完全なソリューションを示します。 

![ハイパーコンバージド記憶域スペース ダイレクトを使用する完全なソリューションの図](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

上の図では、次のリソースが同じリソース グループ内に示されています。

- Windows Server フェールオーバー クラスター内の 2 つの仮想マシン。 仮想マシンがフェールオーバー クラスター内にある場合、仮想マシンは *クラスター ノード* または *ノード* とも呼ばれます。
- 各仮想マシンには、2 つ以上のデータ ディスクがあります。
- 記憶域スペース ダイレクトは、データ ディスク上のデータを同期し、同期されたストレージを記憶域プールとして提供します。
- 記憶域プールは、クラスターの共有ボリューム (CSV) をフェールオーバー クラスターに提供します。
- SQL Server FCI のクラスター ロールは、CSV をデータ ドライブ用に使用します。
- SQL Server FCI の IP アドレスを保持するための Azure ロード バランサー。
- Azure 可用性セットにより、すべてのリソースが保持されます。

   > [!NOTE]
   > このソリューション全体を、Azure でテンプレートから作成できます。 テンプレートの例は、GitHub の[Azure クイック スタート テンプレート](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)のページから使用できます。 この例は、特定のワークロード向けに設計およびテストされたものではありません。 このテンプレートを実行すれば、ドメインに接続された記憶域スペース ダイレクト ストレージで SQL Server FCI を作成できます。 このテンプレートは、評価のうえ、目的に応じた変更が可能です。


## <a name="prerequisites"></a>前提条件

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料](https://azure.microsoft.com/free/)で開始できます。 
- [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)内の [2 つ以上の準備済みの Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps)。 


## <a name="add-the-windows-cluster-feature"></a>Windows クラスター機能を追加する

1. ローカル管理者のメンバーであり、Active Directory でオブジェクトを作成するアクセス許可を持つドメイン アカウントで、リモート デスクトップ プロトコル (RDP) を使用して最初の仮想マシンに接続します。 このアカウントを使用して、構成を進めます。

1. 各仮想マシンにフェールオーバー クラスタリングを追加します。

   UI からフェールオーバー クラスタリングをインストールするには、両方の仮想マシンで次のようにします。

   1. **[サーバー マネージャー]** で、 **[管理]** 、 **[役割と機能の追加]** の順に選択します。
   1. **[役割と機能の追加]** ウィザードで、 **[機能の選択]** ページが表示されるまで **[次へ]** を選択します。
   1. **[機能の選択]** で **[フェールオーバー クラスタリング]** を選択します。 必要なすべての機能と管理ツールを含めます。 
   1. **[機能の追加]** を選択します。
   1. **[次へ]** を選択し、 **[完了]** を選択して、機能をインストールします。

   PowerShell を使用してフェールオーバー クラスタリングをインストールするには、いずれかの仮想マシン上の管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

次の手順の詳細については、「手順 3: 記憶域スペース ダイレクトを構成する」セクション ([Windows Server 2016 で記憶域スペース ダイレクトを使用するハイパーコンバージド ソリューション](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct)に関する記事) の手順を参照してください。


## <a name="validate-the-cluster"></a>クラスターを検証する

UI または PowerShell を使用して、クラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンで次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. 次に示すように、 **[テストの選択]** で、 **[ストレージ]** を除くすべてのテストを選択します。

   ![クラスター検証テストを選択する](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。

    **構成の検証** ウィザードにより、検証テストが実行されます。

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

クラスターの検証後、フェールオーバー クラスターを作成します。


## <a name="create-failover-cluster"></a>フェールオーバー クラスターを作成する

フェールオーバー クラスターを作成するには、以下が必要です。

- クラスター ノードになる仮想マシンの名前。
- フェールオーバー クラスターの名前。
- フェールオーバー クラスターの IP アドレス。 クラスター ノードと同じ Azure 仮想ネットワークおよびサブネットでは使用されていない IP アドレスを使用することができます。


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

次の PowerShell スクリプトは、Windows Server 2012 から Windows Server 2016 用のフェールオーバー クラスターを作成します。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

次の PowerShell スクリプトは、Windows Server 2019 用のフェールオーバー クラスターを作成します。  ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

詳細については、「[フェールオーバー クラスター:クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」に関するセクションを確認してください。

---


## <a name="configure-quorum"></a>クォーラムを構成する

ビジネス ニーズに最も適したクォーラム ソリューションを構成します。 [ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)、または[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)を構成できます。 詳細については、[SQL Server VM でのクォーラム](hadr-cluster-best-practices.md#quorum)に関する記事をご覧ください。 

## <a name="add-storage"></a>ストレージを追加する

記憶域スペース ダイレクト用のディスクは空にする必要があります。 パーティションまたはその他のデータを含めることはできません。 ディスクをクリーニングするには、「[記憶域スペース ダイレクトの展開](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives)」の手順に従います。

1. [記憶域スペース ダイレクトを有効にします](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   次の PowerShell スクリプトを実行すると、記憶域スペース ダイレクトが有効化されます。  

   ```powershell
   Enable-ClusterS2D
   ```

   **[フェールオーバー クラスター マネージャー]** に、記憶域プールが表示されるようになります。

1. [ボリュームを作成します](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes)。

   記憶域スペース ダイレクトは、記憶域プールをユーザーが有効化した場合に、記憶域プールを自動的に作成します。 これでボリュームを作成する準備が整いました。 PowerShell コマンドレット `New-Volume` は、ボリュームの作成プロセスを自動化します。 このプロセスには、書式設定、クラスターへのボリュームの追加、および CSV の作成が含まれます。 次の例では、800 ギガバイト (GB) の CSV が作成されます。

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   前記のコマンドを実行すると、800 GB のボリュームがクラスター リソースとしてマウントされます。 ボリュームは `C:\ClusterStorage\Volume1\` にあります。

   このスクリーンショットでは、記憶域スペース ダイレクトが有効な CSV が示されています。

   ![記憶域スペース ダイレクトが有効なクラスター共有ボリュームのスクリーンショット](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[コア クラスター リソースの移動]**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::

## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。

1. RDP を使用して最初の仮想マシンに接続します。

1. **フェールオーバー クラスター マネージャー** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択します。 ウィザードの指示に従って、SQL Server FCI をインストールします。

   FCI のデータ ディレクトリは、クラスター化されたストレージ上にある必要があります。 記憶域スペース ダイレクトでは、これは共有ディスクではなく、各サーバー上のボリュームのマウント ポイントです。 記憶域スペース ダイレクトは、両方のノード間でボリュームを同期します。 ボリュームは、CSV としてクラスターに表示されます。 データ ディレクトリとして CSV のマウント ポイントを使用します。

   ![データ ディレクトリ](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. セットアップで FCI が最初のノードにインストールされたら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開きます。 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server を含む Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 これらのいずれかのイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 詳細については、「 [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)」 (SQL Server Management Studio (SSMS) のダウンロード) を参照してください。
   >


## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP への登録

ポータルから SQL Server VM を管理するには、それを[軽量管理モード](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)で SQL IaaS Agent 拡張機能 (RP) に登録します。このモードは、現時点では、FCI と Azure VM 上の SQL Server でサポートされている唯一のモードです。 


PowerShell を使用して軽量モードで SQL Server VM を登録します。  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>接続の構成 

現在のプライマリ ノードに適切にトラフィックをルーティングするには、お使いの環境に適した接続オプションを構成します。 [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) を作成できます。あるいは、SQL Server 2019 CU2 (以降) と Windows Server 2016 (以降) を使用している場合、代わりに[分散ネットワーク名](failover-cluster-instance-distributed-network-name-dnn-configure.md)機能を使用できます。 

クラスター接続オプションの詳細については、[HADR 接続を Azure VM 上の SQL Server にルーティングする方法](hadr-cluster-best-practices.md#connectivity)に関する記事をご覧ください。 

## <a name="limitations"></a>制限事項

- Azure Virtual Machines では、CSV および [Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) 上のストレージを備えた Windows Server 2019 で、 Microsoft 分散トランザクション コーディネーター (MSDTC) がサポートされています。
- NTFS でフォーマットされたディスクとして接続されているディスクは、ストレージがクラスターに追加されるときに、ディスク適格性オプションがオフにされている場合にのみ、記憶域スペース ダイレクトで使用できます。 
- [軽量管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)での SQL IaaS Agent 拡張機能への登録のみがサポートされています。

## <a name="next-steps"></a>次のステップ

[仮想ネットワーク名と Azure ロード バランサー](failover-cluster-instance-vnn-azure-load-balancer-configure.md)または[分散ネットワーク名 (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) を使用した FCI への接続をまだ構成していない場合は、構成してください。 

記憶域スペース ダイレクトがお客様に適した FCI ストレージ ソリューションでない場合は、代わりに [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)または [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)を使用して FCI を作成することを検討してください。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスター構成のベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

詳細については、次を参照してください。 
- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)