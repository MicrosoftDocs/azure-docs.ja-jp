---
title: Azure における SAP HANA インフラストラクチャの構成と運用 | Microsoft Docs
description: Azure 仮想マシンにデプロイされている SAP HANA システムの運用ガイドです。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b1591f4f1e96bbb2bffb80a2c652963faa5dca5b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077641"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure における SAP HANA インフラストラクチャの構成と運用
このドキュメントは、Azure インフラストラクチャの構成と Azure のネイティブ仮想マシン (VM) にデプロイされている SAP HANA システムの運用に関するガイダンスを提供します。 また、ドキュメントには、M128 の VM SKU 向けの SAP HANA スケールアウトの構成情報が含まれます。 このドキュメントは、以下の内容を含む標準の SAP ドキュメントを代替するものではありません。

- [SAP 管理ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP インストール ガイド](https://service.sap.com/instguides)
- [SAP ノート](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>前提条件
このガイドを使用するには、次の Azure コンポーネントに関する基本的な知識が必要です。

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure ネットワーキングおよび仮想ネットワーク](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure 上の SAP NetWeaver や他の SAP コンポーネントについて詳しくは、[Azure のドキュメント](https://docs.microsoft.com/azure/) に関するページの [Azure 上の SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) に関するセクションをご覧ください。

## <a name="basic-setup-considerations"></a>基本設定の考慮事項
次のセクションでは、Azure VM に SAP HANA システムをデプロイする際の基本的なセットアップの考慮事項について説明します。

### <a name="connect-into-azure-virtual-machines"></a>Azure 仮想マシンに接続する
[Azure 仮想マシンの計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)のページで説明しているように、Azure VM に接続するには 2 つの基本的な方法があります。

- ジャンプ VM または SAP HANA を実行する VM 上で、インターネットとパブリック エンドポイントを介して接続します。
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) または Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 経由で接続します。

VPN または ExpressRoute 経由でのサイト対サイト接続は運用環境シナリオでは必須です。 このタイプの接続は、SAP ソフトウェアが使用されている場合に運用環境シナリオにフィードする非運用環境シナリオでも必要です。 次の図に、サイト間接続の例を示します。

![サイト間接続](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM の種類を選択する
運用環境シナリオで使用できる Azure VM の種類は、[IAAS の SAP マニュアル](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)に記載されています。 運用環境以外のシナリオでは、さまざまなネイティブの Azure VM の種類が使用できます。

>[!NOTE]
> 運用環境以外のシナリオでは、[SAP ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている VM の種類を使用します。 運用環境シナリオで Azure VM を使用する場合は、SAP が公開している[認定 IaaS プラットフォームの一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)で SAP HANA 認定 VM を確認してください。

以下を使用して VM を Azure にデプロイします。

- Azure Portal
- Azure PowerShell コマンドレット
- Azure CLI

[SAP Cloud platform](https://cal.sap.com/) を使って Azure VM サービスに完全にインストールされた SAP HANA プラットフォームをデプロイすることもできます。 インストール プロセスについては、「[Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)」をご覧いただくか、[こちら](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)でリリースされている自動化スクリプトを参照してください。

>[!IMPORTANT]
> M208xx_v2 VM を使用するためには、Azure VM イメージ ギャラリーから SUSE Linux イメージを選択するときに注意する必要があります。 詳細については、[メモリ最適化された仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)に関する記事をお読みください。 Mv2 ファミリ VM で HANA を使用する場合、Red Hat はまだサポートされていません。 現在の計画では、Mv2 VM ファミリで HANA を実行する Red Hat バージョンのサポートは 2019 年の第 4 四半期に提供されます。 
> 


### <a name="storage-configuration-for-sap-hana"></a>SAP HANA のストレージ構成
Azure の SAP HANA に使用するストレージ構成とストレージの種類については、「[SAP HANA Azure virtual machine storage configurations (SAP HANA Azure 仮想マシンのストレージ構成)](./hana-vm-operations-storage.md)」のドキュメントを参照してください。


### <a name="set-up-azure-virtual-networks"></a>Azure 仮想ネットワークをセットアップする
VPN または ExpressRoute を介して Azure に対するサイト対サイト接続がある場合は、最低 1 つの Azure 仮想ネットワークが仮想ゲートウェイ経由で VPN または ExpressRoute 回線に接続されている必要があります。 シンプルなデプロイでは、Virtual Gateway は、SAP HANA インスタンスもホストしている Azure 仮想ネットワーク (VNet) のサブネットにデプロイできます。 SAP HANA をインストールするには、Azure 仮想ネットワーク内に 2 つのサブネットを追加作成します。 1 つのサブネットは、SAP HANA インスタンスを実行する VM をホストします。 もう 1 つのサブネットは、ジャンプボックスまたは管理 VM を実行し、SAP HANA Studio、他の管理ソフトウェア、またはお使いのアプリケーション ソフトウェアをホストします。

> [!IMPORTANT]
> 機能やパフォーマンス上の理由から、SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤー間の通信パスで [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) を構成することはサポートされていません。 SAP アプリケーション レイヤーと DBMS レイヤー間の通信は直接的な通信である必要があります。 Azure ASG および NSG の規則で直接通信が許可されている限り、この制限に[これらの ASG および NSG の規則](https://docs.microsoft.com/azure/virtual-network/security-overview)は含まれません。 NVA がサポートされないその他のシナリオが、Linux Pacemaker クラスター ノードを表す Azure VM と SBD デバイス間の通信パス内にあります (「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」を参照)。 また、Azure VM と Windows Server SOFS セットアップ間の通信パス内にもあります (「[Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)」を参照)。 通信パスに NVA があると、2 つの通信パートナー間のネットワーク待ち時間が倍増し、SAP アプリケーション レイヤーと DBMS レイヤー間のクリティカル パスのスループットが制限される場合があります。 顧客に見られる一部のシナリオでは、Linux Pacemaker クラスター ノード間の通信で NVA を通じて SBD デバイスと通信する必要がある場合、NVA が原因で Pacemaker Linux クラスターが失敗することがあります。  
> 

> [!IMPORTANT]
> サポートされて**いない**もう 1 つの設計は、SAP アプリケーション レイヤーと DBMS レイヤーを、相互に[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)されていない異なる Azure 仮想ネットワークに分離することです。 異なる Azure 仮想ネットワークを使用するのではなく、Azure 仮想ネットワーク内のサブネットを使用して、SAP アプリケーション レイヤーと DBMS レイヤーを分離することをお勧めします。 推奨事項に従わず、代わりに 2 つのレイヤーを異なる仮想ネットワークに分離する場合は、2 つの仮想ネットワークを[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)する必要があります。 2 つの[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)された Azure 仮想ネットワーク間のネットワーク トラフィックは転送コストの影響を受ける点に注意してください。 SAP アプリケーション レイヤーと DBMS レイヤー間で交換されるデータが膨大でテラバイト規模であり、SAP アプリケーション レイヤーと DBMS レイヤーが 2 つのピアリングされた Azure 仮想ネットワーク間で分離されている場合、相当のコストが蓄積される可能性があります。 

SAP HANA を実行するために VM をインストールするとき、VM には以下が必要です。

- 2 つの仮想 NIC がインストールされていること。1 つの NIC は管理サブネットに接続し、もう 1 つの NIC はオンプレミス ネットワークまたは他のネットワークから Azure VM 内の SAP HANA インスタンスに接続します。
- 両方の仮想 NIC に対してデプロイされる静的プライベート IP アドレス。

> [!NOTE]
> Azure を使用して個々の vNIC に静的 IP アドレスを割り当てる必要があります。 ゲスト OS 内の静的 IP アドレスを vNIC に割り当てないでください。 Azure Backup Service のような一部の Azure サービスは、少なくともプライマリ vNIC が、静的 IP アドレスではなく、DHCP に設定されていることに依存します。 「[Azure 仮想マシンのバックアップのトラブルシューティング](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)」というドキュメントも参照してください。 複数の静的 IP アドレスを VM に割り当てる必要がある場合は、複数の vNIC を VM に割り当てる必要があります。
>
>

ただし、永続的なデプロイの場合は、Azure に仮想データセンター ネットワークを作成する必要があります。 このアーキテクチャでは、オンプレミスを別個の Azure VNet に接続する Azure VNet Gateway を切り離すことが推奨されています。 この別個の VNet では、オンプレミスまたはイントラネットのどちらかに流れるすべてのトラフィックがホストされます。 この手法によって、この別個のハブの VNet で Azure の仮想データセンターに流入するトラフィックの監査とログ記録を行うソフトウェアをデプロイすることが可能になります。 そこで、Azure のデプロイに送受信されるトラフィックに関連するすべてのソフトウェアと構成をホストする VNet を 1 つ配置します。

「[Azure 仮想データセンター:ネットワークの観点](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)」および「[Azure 仮想データセンターとエンタープライズ コントロール プレーン](https://docs.microsoft.com/azure/architecture/vdc/)」の記事に、仮想データセンターの手法と関連する Azure VNet 設計に関する詳細な情報が示されています。


>[!NOTE]
>[Azure VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用してハブの VNet とスポークの VNet 間を流れるトラフィックによって、追加の[コスト](https://azure.microsoft.com/pricing/details/virtual-network/)という課題が生じます。 これらのコストを基に、VNet ピアリングをバイパスするために、状況に応じて厳格なハブ/スポークのネットワーク設計の実行と、 'スポーク' に接続する複数の [Azure ExpressRoute ゲートウェイ](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)の実行の間に、妥協点を見出すことを検討する必要があります。 ただし、Azure ExpressRoute ゲートウェイも、追加の[コスト](https://azure.microsoft.com/pricing/details/vpn-gateway/)を発生させます。 また、ネットワーク トラフィックのログ記録、監査、および監視に使用するサードパーティ製のソフトウェアでも、追加のコストが発生する場合があります。 一方の側で生じる VNet ピアリングを通じたデータ交換のコストと、追加の Azure ExpressRoute ゲートウェイおよび追加のソフトウェア ライセンスによって生じるコストに応じて、VNet の代わりにサブネットを分離単位として使用して、1 つの VNet 内でマイクロセグメント化するという判断も可能です。


IP アドレスを割り当てるさまざまな方法の概要については、「[Azure における IP アドレスの種類と割り当て方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)」をご覧ください。 

SAP HANA を実行している VM では、割り当てられた静的 IP アドレスを使用する必要があります。 これは、HANA の一部の構成属性が IP アドレスを参照するためです。

[Azure ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) を使用して、SAP HANA インスタンスまたはジャンプボックスにルーティングされたトラフィックの送信先を示します。 NSG および今後は[アプリケーションのセキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)も、SAP HANA サブネットと管理サブネットに関連付けられます。

次の図は、ハブとスポークの VNet アーキテクチャに続く SAP HANA の大まかな展開スキーマの概要です。

![SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking.PNG)

サイト対サイト接続を利用せずに Azure に SAP HANA をデプロイするために、パブリック インターネットから SAP HANA インスタンスをシールドして、転送プロキシの背後に隠しておきたい場合があります。 この基本のシナリオでは、デプロイは Azure の組み込み DNS サービスに依存して、ホスト名を解決します。 公開 IP アドレスが使用されるより複雑なデプロイの場合には、Azure 組み込み DNS サーバーが特に重要です。 Azure NSG と [Azure NVA](https://azure.microsoft.com/solutions/network-appliances/) を使用して、Azure でインターネットから Azure VNet アーキテクチャへのルーティングを監視します。 次の図は、SAP HANA をデプロイする場合の大まかなスキーマを示しています。ハブとスポークの VNet アーキテクチャに、サイト対サイト接続はありません。
  
![サイト対サイト接続を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking2.PNG)
 

「[高可用性のネットワーク仮想アプライアンスをデプロイする](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)」の記事に、Azure NVA を使用して、ハブとスポークの VNet アーキテクチャを利用せずに、インターネットからのアクセスを制御および監視する方法に関する別の説明があります。


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>SAP HANA スケールアウト用に Azure インフラストラクチャを構成する
Microsoft では、SAP HANA スケールアウト構成で認定されている M シリーズの VM SKU を 1 つ用意しています。 VM の種類である M128 は、最大 16 ノードのスケールアウトに対して認定を取得済みです。 Azure VM における SAP HANA スケールアウト認定の変更については、[認定 IaaS プラットフォームの一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページを参照してください。

Azure VM でスケールアウト構成をデプロイするための最小の OS リリース要件は、次の通りです。

- SUSE Linux 12 SP3
- Red hat Linux 7.4

16 ノードのスケールアウト認定の内訳は、次のとおりです。

- 1 個のノードがマスター ノード
- 最大 15 個のノードがワーカー ノード

>[!NOTE]
>Azure VM スケールアウトのデプロイで、スタンバイ ノードを使用することはありません。
>

スタンバイ ノードを構成できない理由として、次の 2 つあります。

- Azure には、このポイントにネイティブな NFS サービスがありません。 結果として、NFS 共有はサードパーティの機能を利用して構成する必要があります。
- サードパーティの NFS 構成のうち、Azure にデプロイされたソリューションを使って、SAP HANA のストレージ待機時間の条件を達成できるものはありません。

結果として、 **/hana/data** および **/hana/log** ボリュームは共有できません。 単一ノードのこれらのボリュームを共有しないので、スケールアウト構成で SAP HANA スタンバイ ノードは使用されません。

結果として、スケールアウト構成では、単一ノードの基本設計は次のようになります。

![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA スケールアウトの VM ノードの基本構成は、次のようになります。

- **/hana/shared** の場合、SUSE Linux 12 SP3 に基づいて高可用性 NFS クラスターを構築します。 このクラスターでは、自分のスケールアウト構成および SAP NetWeaver または BW/4HANA Central Services の **/hana/shared** NFS 共有をホストします。 この構成を構築するための資料は、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」の記事で入手できます
- その他のディスク ボリュームはすべて、異なるノード間では共有**されず**、NFS に基づき**ません**。 非共有 **/hana/data** および **/hana/log** を使用したスケールアウト HANA インストールのインストール構成と手順については、このドキュメントで後述します。

>[!NOTE]
>これまでの各図に示した高可用性 NFS クラスターは、SUSE Linux のみでサポートされています。 Red Hat に基づく高可用性 NFS ソリューションについては、後で説明します。

ノードのボリュームのサイズ調整は、 **/hana/shared** 以外ではスケールアップと同じです。 M128 の VM SKU の場合、推奨されるサイズと種類は次の通りです。

| VM の SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | hana/log | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに **/hana/data** および **/hana/log** のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 また、Azure の書き込みアクセラレータを、 **/hana/log** ボリュームを形成するディスクに適用します。
 
ドキュメント『[SAP HANA TDI Storage Requirements (SAP HANA TDI のストレージ要件)](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』では、4 つのワーカー ノードごとに単一のワーカー ノードのメモリ サイズとして、スケールアウト用の **/hana/shared** ボリュームのサイズを定義する式に名前が付与されています。

約 2 TB メモリを備えた SAP HANA スケールアウトの認定 M128 Azure VM を利用していると仮定した場合、SAP の推奨事項は次のようにまとめることができます。

- 1 個のマスター ノードと最大 4 個のワーカー ノード。 **/hana/shared** ボリュームのサイズは 2 TB にする必要があります。 
- 1 個のマスター ノードと 5 個および 8 個のワーカー ノード。 **/hana/shared** のサイズは、4 TB になります。 
- 1 個のマスター ノードと 9 ～ 12 個のワーカー ノード。 **/hana/shared** には、6 TB のサイズが必要になります。 
- 1 個のマスター ノードと、12 ～ 15 個のワーカー ノードの使用。 **/hana/shared** ボリュームには、8 TB のサイズを提供することが求められます。

スケールアウト SAP HANA VM の単一ノード構成の図に示されているその他の重要な設計として、VNet またはそれ以上に優れたサブネット構成があります。 SAP では、HANA ノード間の通信から、クライアント/アプリケーションに向かうトラフィックを分離することを強く推奨しています。 図に示すように、2 つの異なる vNIC を VM にアタッチすることで、この目的が達成されます。 2 つの vNIC は別々のサブネットにあり、2 つの異なる IP アドレスを備えています。 NSG またはユーザー定義のルートを使用して規則をルーティングすることで、トラフィックの流れを制御します。

特に Azure には、特定の vNIC にサービスおよびクオータの品質を適用する手段や方法がありません。 そのため、クライアント/アプリケーションへの流れとイントラ ノード通信を分離することで、1 つのトラフィック ストリームがもう 1 方よりも優先されることにはなりません。 代わりに、分離によってスケールアウト構成のイントラノード通信がシールドされる点で、依然としてセキュリティ対策にはなります。  

>[!IMPORTANT]
>このドキュメントで説明しているように、SAP では、クライアント/アプリケーション側へのトラフィックとイントラノードのトラフィックを分離することを強く推奨しています。 そのため、先ほどの図に示したアーキテクチャを導入することが、強く推奨されます。
>

ネットワークの観点で最低限必要なネットワーク アーキテクチャは、次のようになります。

![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-networking-overview.PNG)

これまでサポートされてきた上限は、1 個のマスター ノードと、追加の 15 個のワーカー ノードです。

ストレージの観点では、ストレージ アーキテクチャは次のようになります。


![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-storage-overview.PNG)

**/hana/shared** ボリュームは、高可用性 NFS 共有の構成に配置されています。 それに対して、他のすべてのドライブは、個々の VM に 'ローカルに' マウントされます。 

### <a name="highly-available-nfs-share"></a>高可用性 NFS 共有
これまでの高可用性 NFS クラスターは、SUSE Linux だけと連動していました。 ドキュメント「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に、設定方法が示されています。 NFS クラスターを SAP HANA インスタンスを実行する Azure VNet 外のその他の HANA 構成と共有しない場合は、NFS クラスターを同じ VNet にインストールします。 NFS クラスターを固有のサブネットにインストールすることで、任意のトラフィックすべてがそのサブネットにアクセスできないようにします。 あるいは、そのサブネットへのトラフィックを、 **/hana/shared** ボリュームへのトラフィックを実行する VM の IP アドレスに制限することを検討してもかまいません。

**/hana/shared** トラフィックをルーティングする必要がある HANA スケールアウト VM の vNIC に関連する推奨事項を次に示します。

- **/hana/shared** へのトラフィック量は中程度なので、最低限の構成で、クライアント ネットワークに割り当てられている vNIC を経由してトラフィックをルーティングします。
- 最終的に、 **/hana/shared** へのトラフィックでは、SAP HANA スケールアウト構成のデプロイ先となる VNet に第 3 のサブネットをデプロイして、そのサブネットにホストされる第 3 の vNIC を割り当てます。 NFS 共有へのトラフィックでは、その第 3 の vNIC と、関連付けられている IP アドレスを使用します。 これで、別個のアクセス規則とルーティング規則を適用できるようになります。

>[!IMPORTANT]
>スケールアウトの手法で SAP HANA がデプロイされている VM と高可用性 NFS 間のネットワーク トラフィックは、いかなる状況でも、[NVA](https://azure.microsoft.com/solutions/network-appliances/) または類似の仮想アプライアンス経由ではルーティングされない可能性があります。 一方、Azure NSG は、このようなデバイスではありません。 SAP HANA を実行している VM から高可用性 NFS 共有にアクセスする場合、NVA または類似の仮想アプライアンスは確実に迂回されるように、ルーティング規則を確認してください。
> 

SAP HANA 構成間で高可用性 NFS クラスターを共有したい場合は、これらすべての HANA 構成を同じ VNet に移動します。 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>SAP HANA スケールアウトの Azure へのインストール
スケールアウト SAP 構成をインストールするには、次のおおまかな手順を実行する必要があります。

- 新しい Azure VNet インフラストラクチャをデプロイするか、または新しく適用する。
- Azure マネージド Premium Storage ボリュームを使用して、新しい VM をデプロイする。
- 高可用性 NFS クラスターを新しくデプロイするか、または既存のクラスターを適用する。
- VM 間のイントラノード通信などが、確実に[NVA](https://azure.microsoft.com/solutions/network-appliances/) 経由でルーティングされないネットワーク ルーティングを適用する。 VM と高可用性 NFS クラスター間のトラフィックについても、同じことが言えます。
- SAP HANA マスター ノードをインストールする。
- SAP HANA マスター ノードの構成パラメーターを適用する。
- SAP HANA ワーカー ノードのインストールを続行する。

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>スケールアウト構成での SAP HANA のインストール
Azure VM インフラストラクチャがデプロイされ、他のすべての準備が完了したら、次の手順に従って SAP HANA スケールアウト構成をインストールする必要があります。

- SAP のドキュメントに従って SAP HANA マスター ノードをインストールする
- **インストール後、global.ini ファイルを変更して、パラメーター 'basepath_shared = no' を global.ini に追加します**。 このパラメーターを使用すると、ノード間で共有される **/hana/data** および **/hana/log** ボリュームがなくてもスケールアウトで SAP HANA を実行できます。 詳細については、[SAP ノートの #2080991](https://launchpad.support.sap.com/#/notes/2080991) を参照してください。
- global.ini のパラメーターを変更した後、SAP HANA インスタンスを再起動します。
- その他のワーカー ノードを追加します。 <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> も参照してください。 インストール中またはローカル hdblcm などの使用後に、SAP HANA インターノード通信の内部ネットワークを指定します。 詳細なドキュメントについては、[SAP ノートの #2183363](https://launchpad.support.sap.com/#/notes/2183363) も参照してください。 

この設定ルーチン以降は、インストールしたスケールアウト構成では、 **/hana/data** および **/hana/log** の実行に非共有ディスクが使用されます。 一方、 **/hana/shared** ボリュームは、高可用性 NFS 共有に配置されます。


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure Virtual Machines 用の SAP HANA Dynamic Tiering 2.0

Microsoft Azure では、Azure M シリーズ VM の SAP HANA 認定だけでなく、SAP HANA Dynamic Tiering 2.0 もサポートされます (後述の SAP HANA Dynamic Tiering ドキュメントのリンクを参照)。 Azure Virtual Machine 内での SAP HANA Cockpit の使用など、製品のインストールや操作に違いはありませんが、Azure での公式サポートのために必須である、重要な点がいくつかあります。 これらの重要な点を以下に示します。 記事中では、Dynamic Tiering 2.0 というフル ネームの代わりに、"DT 2.0" という省略形が使用されます。

SAP BW や S4HANA では、SAP HANA Dynamic Tiering 2.0 はサポートされていません。 現在、主なユース ケースはネイティブの HANA アプリケーションです。


### <a name="overview"></a>概要

次の図に、Microsoft Azure での DT 2.0 サポートに関する概要を示します。 公式認定に準拠するために従う必要がある、一連の必須要件があります。

- DT 2.0 は専用の Azure VM にインストールする必要があります。 SAP HANA が実行されているのと同じ VM では実行されない場合があります
- SAP HANA VM と DT 2.0 VM は同じ Azure Vnet 内にデプロイする必要があります
- Azure 高速ネットワークを有効にして SAP HANA VM と DT 2.0 VM をデプロイする必要があります
- DT 2.0 VM のストレージの種類は Azure Premium Storage である必要があります
- DT 2.0 VM に複数の Azure ディスクをアタッチする必要があります
- Azure ディスク全体でストライピングを使用し、(lvm または mdadm で) ソフトウェア RAID/ストライプ ボリュームを作成する必要があります

詳細については、以降のセクションで説明します。

![SAP HANA DT 2.0 のアーキテクチャの概要](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 専用の Azure VM

Azure IaaS では、専用の VM でのみ DT 2.0 がサポートされます。 HANA インスタンスが実行されている同じ Azure VM で、DT 2.0 を実行することはできません。 最初は以下の 2 つの VM の種類を使用して、SAP HANA DT 2.0 を実行できます。

- M64-32ms 
- E32sv3 

VM の種類の説明については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)を参照してください。

コストを節約するために "ウォーム" データをオフロードするというのが、DT 2.0 の基本的な考え方の場合、対応する VM のサイズを使用するのが妥当です。 可能な組み合わせに関する厳密な規則はありません。 特定の顧客のワークロードによって異なります。

推奨される構成は次のとおりです。

| SAP HANA VM の種類 | DT 2.0 VM の種類 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


SAP HANA 認定の M シリーズ VM とサポート対象の DT 2.0 VM (M64-32ms および E32sv3) のすべての組み合わせが可能です。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure のネットワークと SAP HANA DT 2.0

専用の VM 上に DT 2.0 をインストールするには、DT 2.0 VM と、最小 10 Gb の SAP HANA VM の間にネットワーク スループットが必要です。 そのため、同じ Azure Vnet 内にすべての VM を配置し、Azure 高速ネットワークを有効にすることが必須です。

Azure 高速ネットワークの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)を参照してください。

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 用の VM ストレージ

DT 2.0 のベスト プラクティス ガイダンスに従い、ディスク IO スループットを物理コアあたり最小 50 MB/秒にする必要があります。 DT 2.0 でサポートされている 2 つの Azure VM の種類の仕様によると、VM の最大ディスク IO スループット制限は次のとおりです。

- E32sv3: 768 MB/秒 (キャッシュなし)。これは、物理コアあたり 48 MB/秒という比率を意味します
- M64-32ms:1000 MB/秒 (キャッシュなし)。これは、物理コアあたり 62.5 MB/秒という比率を意味します

DT 2.0 VM に複数の Azure ディスクをアタッチし、OS レベルでソフトウェア RAID (ストライピング) を作成して、VM あたりのディスク スループットの上限に達するようにする必要があります。 この点に関しては、単一の Azure ディスクで VM の上限に達するスループットを提供することはできません。 DT 2.0 を実行するには、Azure Premium Storage が必須です。 

- 利用可能な Azure ディスクの種類の詳細については、[こちら](../../windows/disks-types.md)を参照してください。
- mdadm でのソフトウェア RAID の作成の詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)を参照してください。
- 最大スループットのストライプ ボリュームを作成するための LVM の構成の詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)を参照してください。

サイズの要件に応じて、VM の最大スループットに達するようにするためのさまざまなオプションがあります。 ここでは、VM スループットの上限に達するように DT 2.0 VM の種類ごとに可能なデータ ボリューム ディスク構成を示します。 E32sv3 VM は、より小規模なワークロードのエントリ レベルと見なす必要があります。 十分な速度でないことがわかった場合、VM のサイズを M64-32ms に変更する必要がある可能性があります。
M64-32ms VM のメモリは多いため、IO 読み込みでは、特に読み取り集中型のワークロードの上限に達しない場合があります。 そのため、顧客固有のワークロードによっては、ストライプ セット内の少ないディスクで十分な場合があります。 しかし、安全策として、最大スループットを保証するために次のディスク構成が選択されています。


| VM の SKU | ディスク構成 1 | ディスク構成 2 | ディスク構成 3 | ディスク構成 4 | ディスク構成 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


特に読み取り負荷の高いワークロードの場合、IO パフォーマンスを高め、データベース ソフトウェアのデータ ボリュームに関する推奨事項に従って、Azure ホスト キャッシュの "読み取り専用" を有効にすることができます。 一方、トランザクション ログの場合は、Azure ホスト ディスク キャッシュを "なし" にする必要があります。 

ログ ボリュームのサイズについては、データ サイズの 15% のヒューリスティックを開始点とすることをお勧めします。 ログ ボリュームの作成は、コストとスループットの要件に応じて、さまざまな Azure ディスクの種類を使用して行うことができます。 ログ ボリュームに対しては、高 I/O スループットが必要です。  VM の種類として M64-32ms を使用している場合は、[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)を有効にすることを強くお勧めします。 Azure 書き込みアクセラレータにより、トランザクション ログに最適なディスク書き込み待ち時間が提供されます (M シリーズでのみ利用可能)。 VM の種類ごとのディスクの最大数など、考慮すべきいくつかの項目があります。 書き込みアクセラレータの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)を参照してください。


ログ ボリュームのサイズ変更に関するいくつかの例を次に示します。

| データ ボリュームのサイズとディスクの種類 | ログ ボリュームとディスクの種類の構成 1 | ログ ボリュームとディスクの種類の構成 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


SAP HANA のスケール アウトの場合と同様に、SAP HANA VM と DT 2.0 VM の間で /hana/shared ディレクトリを共有する必要があります。 高可用性 NFS サーバーとして機能する、専用の VM を使用する SAP HANA スケール アウトの場合と同じアーキテクチャをお勧めします。 共有バックアップ ボリュームを提供するために、同じ設計を使用することができます。 しかし、HA が必要であるか、または、バックアップ サーバーとして機能するために十分なストレージ容量がある専用の VM を使用するだけで十分であるかは、お客様が判断する必要があります。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 ドキュメントへのリンク 

- [SAP HANA Dynamic Tiering のインストールと更新に関するガイド](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamic Tiering のチュートリアルとリソース](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamic Tiering の PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 Dynamic Tiering の拡張](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM 上に SAP HANA をデプロイするための操作
次のセクションでは、Azure VM 上での SAP HANA システムのデプロイに関連する操作の一部について説明します。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM でのバックアップおよび復元操作
次のドキュメントで、SAP HANA デプロイをバックアップおよび復元する方法を説明しています。

- [SAP HANA のバックアップの概要](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA のファイルレベルのバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA のストレージ スナップショットのベンチマーク](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA を含む VM の起動および再起動
Azure パブリック クラウドの特長は、コンピューティングを利用した時間分しか課金されないということです。 たとえば、SAP HANA を実行している VM をシャットダウンすると、その間はストレージのコストのみが請求されます。 もう 1 つの特長は、初期デプロイで VM の静的な IP アドレスを指定すると使用できます。 SAP HANA を含む VM を再起動すると、その VM は以前の IP アドレスを使用して再起動します。 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP リモート サポートのために SAProuter を使用する
オンプレミスの場所と Azure の間にサイト対サイト接続があり、SAP コンポーネントを実行している場合は、既に SAProuter を実行している可能性があります。 その場合は、リモート サポートのために次の条件を満たしてください。

- SAP HANA をホストする VM のプライベート IP アドレスと静的 IP アドレスを SAProuter 構成で管理します。
- TCP/IP ポート 3299 を介したトラフィックを許可するように、HANA VM をホストするサブネットの NSG を構成します。

インターネット経由で Azure に接続しているときに、SAP HANA を実行する VM 用の SAP ルーターがない場合は、このコンポーネントをインストールする必要があります。 管理サブネット内の別の VM に SAProuter をインストールします。 次の図は、SAP HANA をデプロイする場合の大まかなスキーマです。サイト対サイト接続はなく、SAProuter があります。

![サイト対サイト接続と SAProuter を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking3.PNG)

SAProuter はジャンプボックス VM ではなく、個別の VM にインストールする必要があります。 個別の VM には静的 IP アドレスが必要です。 ご使用の SAProuter を SAP でホストされる SAProuter に接続するには、IP アドレスについて SAP にお問い合わせください。 (SAP でホストされる SAProuter は、VM にインストールした SAProuter インスタンスの相手側になります。)SAP に問い合わせた IP アドレスを使用して、ご使用の SAProuter インスタンスを構成します。 構成設定で必要なポートは TCP ポート 3299 のみです。

SAPRouter 経由でリモート サポート接続をセットアップし維持する方法について詳しくは、[SAP ドキュメント](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)をご覧ください。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure のネイティブ VM での SAP HANA の高可用性
SUSE Linux Enterprise Server for SAP Applications 12 SP1 以上を実行している場合は、STONITH デバイスを使用して Pacemaker クラスターを確立できます。 このデバイスを使用して、HANA システム レプリケーションによる同期レプリケーションと自動フェールオーバーを使用する SAP HANA 構成をセットアップできます。 セットアップ手順の詳細については、「[SAP HANA High Availability guide for Azure virtual machines (Azure 仮想マシンの SAP HANA 高可用性ガイド)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)」をご覧ください。
