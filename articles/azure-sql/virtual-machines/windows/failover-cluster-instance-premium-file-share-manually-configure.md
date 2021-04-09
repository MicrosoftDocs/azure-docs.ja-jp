---
title: Premium ファイル共有を使用して FCI を作成する
description: Premium ファイル共有 (PFS) を使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
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
ms.openlocfilehash: 80fe9f03f2c57eab8527e553153f3e65315a54bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034848"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Premium ファイル共有を使用して FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[Premium ファイル共有](../../../storage/files/storage-how-to-create-file-share.md)を使用して Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。

Premium ファイル共有は、記憶域スペース ダイレクト (SSD) によってバックアップされる、継続的に待機時間の短いファイル共有であり、Windows Server 2012 以降上の SQL Server 2012 以降のフェールオーバー クラスター インスタンスとの使用が完全にサポートされています。 Premium ファイル共有を使用すると、柔軟性が向上し、ダウンタイムなしでファイル共有のサイズを変更したり拡張したりすることができます。

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

## <a name="prerequisites"></a>前提条件

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)内または異なる[可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)内の [2 つ以上の準備済みの Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。
- データ ファイル用のデータベースのストレージ クォータに基づいて、クラスター化されたドライブとして使用される [Premium ファイル共有](../../../storage/files/storage-how-to-create-file-share.md)。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="mount-premium-file-share"></a>Premium ファイル共有をマウントする

1. [Azure portal](https://portal.azure.com) にサインインします。 そして、ストレージ アカウントに移動します。
1. **[ファイル サービス]** の **[ファイル共有]** に移動し、SQL ストレージに使用する Premium ファイル共有を選択します。
1. **[接続]** を選択して、ファイル共有の接続文字列を表示します。
1. ドロップダウン リストで使用するドライブ文字を選択し、両方のコード ブロックをメモ帳にコピーします。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="ファイル共有接続ポータルから両方の PowerShell コマンドをコピーする":::

1. リモート デスクトップ プロトコル (RDP) を使用して、SQL Server FCI がサービス アカウントに使用するアカウントで SQL Server VM に接続します。
1. 管理 PowerShell コマンド コンソールを開きます。
1. ポータルで作業していたときに保存したコマンドを実行します。
1. エクスプローラーまたは **[ファイル名を指定して実行]** ダイアログ ボックス (Windows + R キーを押す) を使用して、共有に移動します。 ネットワークパス `\\storageaccountname.file.core.windows.net\filesharename` を使用します。 たとえば、`\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare` のように指定します。

1. 新しく接続されたファイル共有に、SQL データ ファイルを配置するフォルダーを少なくとも 1 つ作成します。
1. クラスターに参加する各 SQL Server VM で、この手順を繰り返します。

  > [!IMPORTANT]
  > - バックアップ ファイル用に別のファイル共有を使用して、この共有の 1 秒間の入出力操作数 (IOPS) と領域の容量をデータとログ ファイル用に確保することを検討してください。 バックアップ ファイルには、Premium または Standard のいずれのファイル共有も使用できます。
  > - Windows 2012 R2 以前を使用している場合は、同じ手順に従って、ファイル共有監視として使用するファイル共有をマウントします。 
  > 


## <a name="add-windows-cluster-feature"></a>Windows クラスター機能を追加する

1. ローカル管理者のメンバーであり、かつ Active Directory でオブジェクトを作成するアクセス許可を持つドメイン アカウントを使用して、RDP で最初の仮想マシンに接続する。 このアカウントを使用して、構成を進めます。

1. [各仮想マシンにフェールオーバー クラスタリングを追加します](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

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

## <a name="validate-cluster"></a>クラスターを検証する

UI または PowerShell を使用して、クラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンで次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. 次に示すように、 **[テストの選択]** で、 **[ストレージ]** と **[記憶域スペース ダイレクト]** を除くすべてのテストを選択します。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="クラスター検証テストを選択する":::

1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。

**構成の検証** ウィザードにより、検証テストが実行されます。

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[コア クラスター リソースの移動]**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::


## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターを構成したら、SQL Server FCI を作成できます。

1. RDP を使用して最初の仮想マシンに接続します。

1. **[フェールオーバー クラスター マネージャー]** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 

1. **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択し、ウィザードの指示に従って SQL Server FCI をインストールします。

   FCI データ ディレクトリは、Premium ファイル共有上に存在する必要があります。 共有の完全パスを `\\storageaccountname.file.core.windows.net\filesharename\foldername` の形式で入力します。 ファイル サーバーをデータ ディレクトリとして指定したことを通知する警告が表示されます。 この警告は想定されています。 ファイル共有を永続化したときに RDP 経由で VM にアクセスするために使用したユーザー アカウントが、潜在的なエラーを回避するために SQL Server サービスで使用されているのと確実に同じアカウントであるようにします。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="ファイル共有を SQL データ ディレクトリとして使用する":::

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. セットアップで FCI が最初のノードにインストールされたら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開き、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server で Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 これらのいずれかのイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 詳細については、「 [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)」 (SQL Server Management Studio (SSMS) のダウンロード) を参照してください。

1. SQL Server フェールオーバー クラスター インスタンスに追加する他のすべてのノードで、以上の手順を繰り返します。 

## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP への登録

ポータルから SQL Server VM を管理するには、それを[軽量管理モード](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)で SQL IaaS Agent 拡張機能 (RP) に登録します。このモードは、現時点では、FCI と Azure VM 上の SQL Server でサポートされている唯一のモードです。 

PowerShell を使用して軽量モードで SQL Server VM を登録します (-LicenseType は `PAYG` または `AHUB` にできます)。

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>接続の構成 

現在のプライマリ ノードに適切にトラフィックをルーティングするには、お使いの環境に適した接続オプションを構成します。 [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) を作成できます。あるいは、SQL Server 2019 CU2 (以降) と Windows Server 2016 (以降) を使用している場合、代わりに[分散ネットワーク名](failover-cluster-instance-distributed-network-name-dnn-configure.md)機能を使用できます。 

クラスター接続オプションの詳細については、[HADR 接続を Azure VM 上の SQL Server にルーティングする方法](hadr-cluster-best-practices.md#connectivity)に関する記事をご覧ください。 

## <a name="limitations"></a>制限事項

- Microsoft 分散トランザクション コーディネーター (MSDTC) は、Windows Server 2016 以前ではサポートされていません。 
- Filestream は、Premium ファイル共有のフェールオーバー クラスターではサポートされていません。 Filestream を使用するには、[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)または [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)を代わりに使用して、クラスターをデプロイします。
- [軽量管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)での SQL IaaS Agent 拡張機能への登録のみがサポートされています。 

## <a name="next-steps"></a>次のステップ

[仮想ネットワーク名と Azure ロード バランサー](failover-cluster-instance-vnn-azure-load-balancer-configure.md)または[分散ネットワーク名 (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) を使用した FCI への接続をまだ構成していない場合は、構成してください。 


Premium ファイル共有がお客様に適した FCI 記憶域ソリューションでない場合は、代わりに [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)または[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)を使用して FCI を作成することを検討してください。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスター構成のベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

詳細については、次を参照してください。 
- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)