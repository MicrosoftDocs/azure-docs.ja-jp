---
title: "Azure Site Recovery を使用した Azure へのレプリケーションの前提条件 | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して VM や物理マシンを Azure にレプリケートするための前提条件について学習します。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ccc2b53e0824042c0f07b9fe63e8777aa68c6dc1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---

#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Site Recovery を使用してオンプレミスから Azure へのレプリケーションを行うための前提条件

> [!div class="op_single_selector"]
> * [Azure から Azure へのレプリケート](site-recovery-azure-to-azure-prereq.md)
> * [オンプレミスから Azure へのレプリケート](site-recovery-prereq.md)

Azure Site Recovery は、別の Azure リージョンへの Azure 仮想マシン (VM) のレプリケーションを調整することにより、ビジネス継続性とディザスター リカバリー (BCDR) 戦略のサポートを支援します。 Site Recovery はまた、オンプレミスの物理サーバーと VM を、クラウド (Azure) またはセカンダリ データセンターにレプリケートします。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持できます。 プライマリ ロケーションが通常の動作に戻ったときに、その場所にフェールバックすることができます。 Site Recovery の詳細については、「[Site Recovery とは](site-recovery-overview.md)」を参照してください。

この記事では、オンプレミスのマシンから Azure への Site Recovery レプリケーションを開始するために必要な前提条件について要約します。

コメントがありましたら、この記事の末尾に投稿できます。 また、技術的な質問を [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で行うことができます。

## <a name="azure-requirements"></a>Azure の要件

**要件** | **詳細**
--- | ---
**Azure アカウント** | [Microsoft Azure アカウント](http://azure.microsoft.com/)。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
**Site Recovery サービス** | Azure Site Recovery サービスの価格の詳細については、「[Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」を参照してください。 |
**Azure Storage** | レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 ストレージ アカウントは、Azure Recovery Services コンテナーと同じリージョンに存在する必要があります。 フェールオーバーの発生時に Azure VM が作成されます。<br/><br/> Azure VM フェールオーバーに使用するリソース モデルに応じて、[Azure Resource Manager デプロイメント モデル](../storage/common/storage-create-storage-account.md)または[クラシック デプロイメント モデル](../storage/common/storage-create-storage-account.md)を使用してアカウントをセットアップできます。<br/><br/>[geo 冗長ストレージ](../storage/common/storage-redundancy.md#geo-redundant-storage)またはローカル冗長ストレージを使うことができます。 geo 冗長ストレージをお勧めします。 geo 冗長ストレージを使用すると、地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できます。<br/><br/> 標準の Azure ストレージ アカウントを使用するか、Azure Premium Storage を使用することができます。 [Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage) は通常、常に高い I/O パフォーマンスと短い待機時間を必要とする VM に使用されます。 Premium Storage を使用すると、VM は I/O 集中型ワークロードをホストできます。 レプリケートしたデータに Premium Storage を使用する場合は、標準のストレージ アカウントも必要です。 Standard Storage アカウントは、オンプレミス データに対する進行中の変更をキャプチャするレプリケーション ログを保存します。<br/><br/>
**ストレージの制限事項** | Site Recovery で使用するストレージ アカウントは、別のリソース グループに移動することはできません。また、別のサブスクリプションに移動することも、別のサブスクリプションで使用することもできません。<br/><br/> 現在、インド中部およびインド南部での Premium Storage アカウントへのレプリケーションはサポートされていません。
**Azure ネットワーク** | フェールオーバー後に Azure VM の接続先となる Azure ネットワークが必要です。 Azure ネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。<br/><br/> Azure Portal では、[Resource Manager デプロイメント モデル](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック デプロイメント モデル](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を使用して Azure ネットワークを作成できます。<br/><br/> System Center Virtual Machine Manager (VMM) から Azure にレプリケートする場合は、VMM VM ネットワークと Azure ネットワークの間のネットワーク マッピングをセットアップする必要があります。 これにより、Azure VM はフェールオーバー後、確実に適切なネットワークに接続されます。
**ネットワークの制限** | Site Recovery で使用するネットワーク アカウントは、別のリソース グループに移動することはできません。また、別のサブスクリプションに移動することも、別のサブスクリプションで使用することもできません。
**ネットワーク マッピング** | Microsoft Hyper-V VM を VMM クラウド内でレプリケートする場合は、ネットワーク マッピングをセットアップする必要があります。 これにより、Azure VM はフェールオーバー後に作成されたときに、確実に適切なネットワークに接続されます。

> [!NOTE]
> 以下のセクションでは、お客様の環境のさまざまなコンポーネントの前提条件について説明します。 特定の構成のサポートについて詳しくは、[サポート マトリックス](site-recovery-support-matrix.md)を参照してください。
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>VMware VM または Windows/Linux 物理サーバーの Azure へのディザスター リカバリー
VMware VM または Windows/Linux 物理サーバーのディザスター リカバリーには、以下のコンポーネントが必要です。 これらは、「[Azure の要件](#azure-requirements)」に記載されているコンポーネントへの追加分です。


### <a name="configuration-server-or-additional-process-server"></a>構成サーバーまたは追加のプロセス サーバー

オンプレミスのマシンを構成サーバーとしてセットアップし、オンプレミス サイトと Azure 間の通信を調整します。 また、オンプレミスのマシンは、データ レプリケーションも管理します。 <br/></br>

*   **VMware vCenter または vSphere ホストの前提条件**

    | **コンポーネント** | **要件** |
    | --- | --- |
    | **vSphere** | 1 つ以上の VMware vSphere ハイパーバイザーが必要です。<br/><br/>ハイパーバイザーでは、最新の更新プログラムが適用された vSphere バージョン 6.0、5.5、または 5.1 が実行されている必要があります。<br/><br/>vSphere ホストと vCenter サーバーはいずれも、プロセス サーバーと同じネットワーク内にあることが推奨されます。 このネットワークは、専用のプロセス サーバーをセットアップした場合を除き、構成サーバーがあるネットワークです。 |
    | **vCenter** | vSphere ホストを管理する VMware vCenter サーバーをデプロイすることをお勧めします。 このサーバーでは、最新の更新プログラムが適用された vCenter バージョン 6.0 または 5.5 が実行されている必要があります。<br/><br/>**制限事項**: Site Recovery では、vCenter vMotion のインスタンス間のレプリケーションはサポートされていません。 再保護操作後のマスター ターゲット VM では、Storage DRS と Storage vMotion もサポートされていません。||

* **レプリケーション対象のマシンの前提条件**

    | **コンポーネント** | **要件** |
    | --- | --- |
    | **オンプレミスのマシン** (VMware VM) | レプリケーション対象の VM では、VMware ツールがインストールされ、実行されている必要があります。<br/><br/> VM は、Azure VM を作成するための [Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たしていなければなりません。<br/><br/>保護対象の各マシンのディスク容量は、1,023 GB より大きくすることはできません。 <br/><br/>コンポーネントのインストール用として、インストール ドライブに 2 GB 以上の空き領域が必要です。<br/><br/>マルチ VM 整合性を有効にする場合は、ポート 20004 を VM のローカル ファイアウォールで開く必要があります。<br/><br/>マシン名は 1 文字から 63 文字の長さでなければなりません (文字、数字、およびハイフンを使用できます)。 名前の先頭と末尾は文字または数字でなければなりません。 <br/><br/>Azure 上の名前は、マシンのレプリケーションを有効にした後で変更できます。<br/><br/> |
    | **Windows マシン** (物理または VMware) | マシンでは、サポートされている以下のいずれかの 64 ビット オペレーティング システムが実行されている必要があります。 <br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>- Windows Server 2008 R2 (SP1 以降のバージョンが適用済み)<br/><br/> オペレーティング システムは、C ドライブにインストールされている必要があります。OS ディスクは、ダイナミック ディスクではなく、Windows ベーシック ディスクでなければなりません。 データ ディスクはダイナミックでもかまいません。<br/><br/>|
    | **Linux マシン** (物理または VMware) | マシンでは、サポートされている以下のいずれかの 64 ビット オペレーティング システムが実行されている必要があります。 <br/>- Red Hat Enterprise Linux 5.2 から 5.11、6.1 から 6.9、7.0 から 7.3<br/>- CentOS: 5.2 から 5.11、6.1 から 6.9、7.0 から 7.3<br/>- Ubuntu 14.04 LTS サーバー (Ubuntu でサポートされているカーネル バージョンのリストについては、[サポートされているオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)に関するページを参照)<br/>- Ubuntu 16.04 LTS サーバー (Ubuntu でサポートされているカーネル バージョンのリストについては、[サポートされているオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)に関するページを参照)<br/>-  Debian 7 または Debian 8<br/>-  Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.5、6.4<br/>- SUSE Linux Enterprise Server 11 SP4 または SUSE Linux Enterprise Server 11 SP3<br/><br/>保護対象のマシン上の /etc/hosts ファイルには、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。<br/><br/>フェールオーバー後、Secure Shell (SSH) クライアントを使用して Linux が実行されている Azure VM に接続する場合は、保護対象のマシン上の SSH サービスがシステム起動時に自動的に開始されるように設定されていることを確認します。 また、保護対象のマシンへの SSH 接続がファイアウォール規則で許可されていることも確認します。<br/><br/>ホスト名、マウント ポイント、デバイス名、および Linux システム パスとファイル名 (例えば /etc/ や /usr) は英語のみ使用できます。<br/><br/>以下のディレクトリ (個別のパーティションまたはファイル システムとしてセットアップされている場合) はすべて、ソース サーバー上の同じディスク (OS ディスク) 上に存在する必要があります。<br/>- / (root)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>現在、メタデータ チェックサムなどの XFS v5 機能は、XFS ファイル システム上の Site Recovery ではサポートされていません。 XFS ファイル システムで v5 機能を使用していないことを確認してください。 xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 **ftype** が **1** に設定されている場合は、XFS v5 機能が使用されています。<br/><br/>Red Hat Enterprise Linux 7 サーバーと CentOS 7 サーバーでは、lsof ユーティリティをインストールして使用可能にする必要があります。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Azure への Hyper-V VM のディザスター リカバリー (VMM なし)

VMM クラウドにある Hyper-V のディザスター リカバリーには、以下のコンポーネントが必要です。 これらは、「[Azure の要件](#azure-requirements)」に記載されているコンポーネントへの追加分です。

| **前提条件** | **詳細** |
| --- | --- |
| **Hyper-V ホスト** |1 つ以上のオンプレミスのサーバーで、最新の更新プログラムと Hyper-V ロールが有効になっている Windows Server 2012 R2、または Microsoft Hyper-V Server 2012 R2 が実行されている必要があります。<br/><br/>Hyper-V サーバーには 1 つ以上の VM が必要です。<br/><br/>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続する必要があります。<br/><br/>Hyper-V サーバーには、サポート技術情報の記事 [2961977](https://support.microsoft.com/kb/2961977) に記載されている修正プログラムが適用されていなければなりません。
|**プロバイダーとエージェント**| Site Recovery のデプロイ時に、Azure Site Recovery Provider をインストールします。 プロバイダーをインストールすると、保護対象の VM を実行している各 Hyper-V サーバーに Azure Recovery Services エージェントもインストールされます。 <br/><br/>Site Recovery コンテナー内のすべての Hyper-V サーバーに、同じバージョンのプロバイダーとエージェントがインストールされていなければなりません。<br/><br/>プロバイダーは、インターネット経由で Site Recovery に接続する必要があります。 トラフィックを直接送信することも、プロキシ経由で送信することもできます。 HTTPS ベースのプロキシはサポートされません。 プロキシ サーバーでは、次の URL へのアクセスを許可する必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)、および HTTPS (ポート 443) を許可します。<br/><br/> サブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (アクセス制御と ID 管理に使用されます)。


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM クラウド内にある Hyper-V VM の、Azure へのディザスター リカバリー

VMM クラウドにある Hyper-V のディザスター リカバリーには、以下のコンポーネントが必要です。 これらは、「[Azure の要件](#azure-requirements)」に記載されているコンポーネントへの追加分です。

| **前提条件** | **詳細** |
| --- | --- |
| **Virtual Machine Manager** |1 つ以上の VMM サーバーが、System Center 2012 R2 以降のバージョンで実行されている必要があります。 各 VMM サーバーに 1 つ以上のクラウドが構成されている必要があります。 <br/><br/>クラウドには以下が必要です。<br/>- 1 つ以上の VMM ホスト グループ。<br/>- 各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。<br/><br/>VMM クラウドの設定について詳しくは、[Virtual Machine Manager 2012 でクラウドを作成する方法](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)に関するページをご覧ください。 |
| **Hyper-V** |Hyper-V ホスト サーバーでは、Hyper-V ロールが有効になっている Windows Server 2012 R2 以降、または Microsoft Hyper-V Server 2012 R2 が実行されている必要があります。 最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーには 1 つ以上の VM が必要です。<br/><br/> レプリケート対象の VM を含む Hyper-V ホスト サーバーまたはクラスターは、VMM クラウド内で管理する必要があります。<br/><br/>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続する必要があります。<br/><br/>Hyper-V サーバーには、サポート技術情報の記事 [2961977](https://support.microsoft.com/kb/2961977) に記載されている修正プログラムが適用されていなければなりません。<br/><br/>Azure へのデータ レプリケーションを行うには、Hyper-V ホスト サーバーがインターネットにアクセスできることが必要です。 |
| **プロバイダーとエージェント** |Azure Site Recovery のデプロイ時に、Azure Site Recovery Provider を VMM サーバーにインストールします。 Recovery Services エージェントを Hyper-V ホストにインストールします。 プロバイダーとエージェントは、インターネット経由で直接に、またはプロキシを使用して、Azure に接続する必要があります。 HTTPS ベースのプロキシはサポートされていません。 VMM サーバーおよび Hyper-V ホスト上のプロキシ サーバーでは、以下へのアクセスを許可する必要があります。 <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>VMM サーバーに IP アドレスベースのファイアウォール規則がある場合は、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)および HTTPS (ポート 443) を許可します。<br/><br/>サブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (アクセス制御と ID 管理に使用されます)。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>レプリケーション対象のマシンの前提条件

| **コンポーネント** | **詳細** |
| --- | --- |
| **保護対象の VM** | Site Recovery では、[Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) でサポートされているすべてのオペレーティング システムがサポートされます。<br/><br/>VM は、Azure VM を作成するための [Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たしていなければなりません。 マシン名は 1 文字から 63 文字の長さでなければなりません (文字、数字、およびハイフンを使用できます)。 名前の先頭と末尾は文字または数字でなければなりません。 <br/><br/>VM 名は、VM のレプリケーションを有効にした後で変更できます。 <br/><br/> 保護対象の各マシンのディスク容量は、1,023 GB より大きくすることはできません。 VM では最大 16 個のディスク (最大 16 TB) を使用できます。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>VMM クラウド内にある Hyper-V VM の、お客様所有サイトへのディザスター リカバリー

VMM クラウドにある Hyper-V の、お客様所有サイトへのディザスター リカバリーには、以下のコンポーネントが必要です。 これらは、「[Azure の要件](#azure-requirements)」に記載されているコンポーネントへの追加分です。

| **コンポーネント** | **詳細** |
| --- | --- |
| **Virtual Machine Manager** |  プライマリ サイトとセカンダリ サイトの両方に VMM サーバーをデプロイすることをお勧めします。<br/><br/> [単一の VMM サーバー上のクラウド間でレプリケート](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)できます。 1 つの VMM サーバー上のクラウド間でレプリケートを行うには、VMM サーバーで少なくとも 2 つのクラウドが構成されている必要があります。<br/><br/> VMM サーバーでは、最新の更新プログラムが適用された System Center 2012 SP1 以降が実行されている必要があります。<br/><br/> 各 VMM サーバーには 1 つ以上のクラウドが必要です。 すべてのクラウドには Hyper-V キャパシティ プロファイルが設定されている必要があります。 <br/><br/>クラウドには 1 つ以上の VMM ホスト グループが必要です。 VMM クラウドのセットアップについて詳しくは、[Azure Site Recovery のデプロイの準備](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)に関するページを参照してください。 |
| **Hyper-V** | Hyper-V サーバーでは、Hyper-V ロールが有効になっている Windows Server 2012 以降が実行され、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーには 1 つ以上の VM が必要です。<br/><br/>  Hyper-V ホスト サーバーは、プライマリおよびセカンダリの VMM クラウド内のホスト グループに存在する必要があります。<br/><br/> Hyper-V を Windows Server 2012 R2 上のクラスターで実行する場合は、サポート技術情報の記事 [2961977](https://support.microsoft.com/kb/2961977) に記載されている更新プログラムをインストールすることをお勧めします。<br/><br/> Windows Server 2012 上のクラスターで Hyper-V を実行し、静的 IP アドレス ベースのクラスターが存在する場合、クラスター ブローカーは自動的に作成されません。 クラスター ブローカーを手動で構成する必要があります。 クラスター ブローカーについて詳しくは、[クラスター間レプリケーションのためのレプリカ ブローカー ロールの構成](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)に関するページを参照してください。 |
| **プロバイダー** | Site Recovery のデプロイ時に、Azure Site Recovery Provider を VMM サーバーにインストールします。 プロバイダーは、HTTPS (ポート 443) 経由で Azure Site Recovery と通信し、レプリケーションを調整します。 データのレプリケーションは、LAN または VPN 接続を経由してプライマリとセカンダリの Hyper-V サーバー間で実行されます。<br/><br/> VMM サーバーで実行されているプロバイダーには、以下の URL へのアクセス権が必要です。<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Site Recovery Provider は、VMM サーバーから [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)へのファイアウォール通信、および HTTPS (ポート 443) プロトコルを許可する必要があります。 |


## <a name="url-access"></a>URL アクセス
これらの URL は、VMware、VMM、および Hyper-V ホスト サーバーから使用可能なでなければなりません。

|**URL** | **VMM から VMM** | **VMM から Azure** | **Hyper-V から Azure** | **VMware から Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | ALLOW | 許可 | 許可 | 許可 |
|``*.backup.windowsazure.com`` | 必要なし | 許可 | 許可 | 許可 |
|``*.hypervrecoverymanager.windowsazure.com`` | 許可 | 許可 | 許可 | 許可 |
|``*.store.core.windows.net`` | 許可 | 許可 | 許可 | 許可 |
|``*.blob.core.windows.net`` | 必要なし | 許可 | 許可 | 許可 |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 必要なし | 必要なし | 必要なし | SQL のダウンロードを許可 |
|``time.windows.com`` | ALLOW | 許可 | 許可 | 許可|
|``time.nist.gov`` | 許可 | 許可 | 許可 | ALLOW |

