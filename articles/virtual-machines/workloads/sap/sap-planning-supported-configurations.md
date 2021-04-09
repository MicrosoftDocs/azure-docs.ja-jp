---
title: SAP on Azure:Azure VM でサポートされるシナリオ
description: Azure Virtual Machines でサポートされる SAP ワークロードに関するシナリオ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670927"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ
Azure での SAP NetWeaver、Business One、`Hybris`、または S/4HANA システム アーキテクチャの設計により、さまざまなアーキテクチャやツールで、スケーラブルで、効率性、可用性に優れたデプロイを実現するためのさまざまな機会が提供されます。 使用されているオペレーティング システムまたは DBMS によっては、制限があります。 また、オンプレミスでサポートされているすべてのシナリオが、Azure でも同じようにサポートされているわけではありません。 このドキュメントでは、サポートされていない非高可用性構成と高可用性構成、および Azure VM だけを使用するアーキテクチャについて説明します。 [HANA Large Instances](./hana-overview-architecture.md) でサポートされているシナリオについては、「[HANA L インスタンスのサポートされるシナリオ](./hana-supported-scenario.md)」をご覧ください。 


## <a name="2-tier-configuration"></a>2 層構成
SAP 2 層構成は、同じサーバーまたは VM ユニットで実行される SAP DBMS とアプリケーション レイヤーの複合レイヤーで構築されるものと見なされます。 2 番目の層は、ユーザー インターフェイス レイヤーと見なされます。 2 層構成の場合、DBMS と SAP アプリケーション レイヤーによって Azure VM のリソースが共有されます。 そのため、リソースが競合しないような方法で、さまざまなコンポーネントを構成する必要があります。 また、VM のリソースをオーバーサブスクライブしないように注意する必要もあります。 そのような構成では、関連するさまざまな Azure コンポーネントの [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)より高い高可用性は提供されません。

このような構成を図に示すと次のようになります。

![単純な 2 層構成](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

運用環境と非運用環境の SQL Server、Oracle、DB2、maxDB、SAP ASE の DBMS システムに対して、Windows、Red Hat、SUSE、Oracle Linux でそのような構成がサポートされています。 SAP HANA が DBMS の場合は、そのような種類の構成は非運用環境でのみサポートされます。 これには、[Azure HANA Large Instances](./hana-overview-architecture.md) のデプロイのケースも含まれます。
Azure でサポートされているすべての OS/DBMS の組み合わせについて、この種類の構成がサポートされています。 ただし、DBMS コンポーネントと SAP コンポーネントにより、メモリ リソースと CPU リソースに対する競合が起き、使用可能な物理リソースを超えてしまうことがないように構成することは必須です。 これを行うには、DBMS で割り当てることができるメモリを制限する必要があります。 また、アプリケーション インスタンスの SAP 拡張メモリを制限する必要もあります。 また、VM 全体の CPU 使用率を監視し、コンポーネントによって CPU リソースが限界まで使用されていないことを確認する必要もあります。 

> [!NOTE]
> 運用 SAP システムの場合は、このドキュメントで後ほど説明するように、追加の高可用性と最終的なディザスター リカバリーの構成をお勧めします


## <a name="3-tier-configuration"></a>3 層構成
この構成では、SAP アプリケーション レイヤーと DBMS レイヤーを別の VM に分割します。 通常、これは、大規模なシステムで、SAP アプリケーション レイヤーのリソースの柔軟性を高くするために行います。 ほとんどの単純なセットアップでの高可用性は、関連するさまざまな Azure コンポーネントの [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)より高くはなりません。 

図で表すと次のようになります。

![単純な 3 層構成を示す図。](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

運用環境と非運用環境の SQL Server、Oracle、DB2、SAP HANA、maxDB、SAP ASE の DBMS システムに対して、Windows、Red Hat、SUSE、Oracle Linux で、この種の構成がサポートされています。 これは、[Azure HANA Large Instances](./hana-overview-architecture.md) に対する既定のデプロイ構成です。 単純化するため、SAP アプリケーション レイヤーの SAP セントラル サービスと SAP ダイアログ インスタンスは区別しませんでした。 この単純な 3 層構成では、SAP セントラル サービスに対する高可用性保護はありません。

> [!NOTE]
> 運用 SAP システムの場合は、このドキュメントで後ほど説明するように、追加の高可用性と最終的なディザスター リカバリーの構成をお勧めします


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>VM または HANA Large Instance ユニットごとに複数の DBMS インスタンス
この構成の種類では、Azure VM または HANA Large Instance ユニットごとに複数の DBMS インスタンスをホストします。 目的は、管理対象のオペレーティング システムを少なくし、コストを削減することです。 または、大規模な VM または HANA Large Instance ユニットのリソースを複数の DBMS インスタンス間で共有することにより、柔軟性と効率性を高めることが目的の場合もあります。 これまでは、これらの構成は非運用システムで用に主に示されてきました。

そのような構成は次のようになります。

![1 つのユニットに複数の DBMS インスタンス](./media/sap-planning-supported-configurations/multiple-database-instances.png)

この種類の DBMS のデプロイは、以下でサポートされています。

- Windows 上の SQL Server
- IBM DB2。 詳細については、「[複数インスタンス (Linux、UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)」を参照してください
- Oracle。 詳細については、[SAP サポート ノート #1778431](https://launchpad.support.sap.com/#/notes/1778431) および関連する SAP ノートを参照してください
- SAP HANA。1 つの VM で複数のインスタンス (このデプロイ方法は SAP では MCOS と呼ばれます) がサポートされます。 詳細については、1 つのホスト上の複数の SAP HANA システム (MCOS) に関する SAP の記事 (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html) を参照してください。

1 つのホスト上で複数のデータベース インスタンスを実行する場合は、異なるインスタンスがリソースに対して競合しないようにして、VM の物理リソースの制限を超えないようにする必要があります。 これは特に、VM を共有するインスタンスの 1 つが割り当てることのできる量に上限を設ける必要があるメモリの場合に大事です。 また、異なるデータベース インスタンスで利用できる CPU リソースにも当てはまります。 前述のすべての DBMS には、インスタンス レベルでメモリ割り当てと CPU リソースを制限できる構成があります。
このような構成を Azure VM でサポートするには、異なるインスタンスによって管理されるデータベースのデータ ファイルとログおよび再実行ログ ファイルに使用されるディスクまたはボリュームを分離することが期待されます。 つまり、異なる DBMS インスタンスによって管理されるデータベースのデータ ファイルまたはログ ファイルと再実行ログ ファイルで、同じディスクまたはボリュームが共有されないようにします。 

HANA Large Instances のディスク構成は、構成済みで提供されます。詳細についてた、「[HANA Large Instances のサポートされているシナリオ](./hana-supported-scenario.md#single-node-mcos)」を参照してください。 

> [!NOTE]
> 運用 SAP システムの場合は、このドキュメントで後ほど説明するように、追加の高可用性と最終的なディザスター リカバリーの構成をお勧めします。 複数の DBMS インスタンスで 1 つの VM を使用することは、このドキュメントで後ほど説明する高可用性構成ではサポートされていません。


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>1 つの VM での複数の SAP ダイアログ インスタンス
多くの場合、複数のダイアログ インスタンスが、ベア メタル サーバーまたはプライベート クラウドで実行されている VM にデプロイされました。 このような構成の理由は、特定の SAP ダイアログ インスタンスを特定のワークロード、ビジネス機能、またはワークロードの種類に合わせて調整することでした。 それらのインスタンスを別々の VM に分離しない理由は、オペレーティング システムのメンテナンスと運用のためでした。 また、VM をホストまたは運用する企業の多くは、運用および管理される VM ごとに月額料金を要求します。 Azure では、1 つの VM で複数の SAP ダイアログ インスタンスをホストするシナリオは、オペレーティング システムが Windows、Red Hat、SUSE、Oracle Linux である運用環境と非運用環境でサポートされています。 1 つの VM で複数の SAP アプリケーション サーバー インスタンスを実行する場合は、Windows と最新の Linux カーネルで使用できる SAP カーネル パラメーター PHYS_MEMSIZE を設定する必要があります。 また、SAP 拡張メモリの自動拡張が実装されている Windows などのオペレーティング システムでは、SAP 拡張メモリの拡張を制限することもお勧めします。 これは、SAP プロファイル パラメーター `em/max_size_MB` で行うことができます。

Azure VM 内で複数の SAP ダイアログ インスタンスが実行される 3 層構成は、次のようになります。

![Azure VM 内で複数の SAP ダイアログ インスタンスが実行される 3 層構成を示す図。](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

単純化するため、SAP アプリケーション レイヤーの SAP セントラル サービスと SAP ダイアログ インスタンスは区別しませんでした。 この単純な 3 層構成では、SAP セントラル サービスに対する高可用性保護はありません。 運用システムでは、SAP セントラル サービスを保護しないままにしないことをお勧めします。 SAP セントラル サービスに関するいわゆるマルチ SID 構成と、そのようなマルチ SID 構成の高可用性の詳細については、このドキュメントの後のセクションを参照してください。

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS レイヤーの高可用性保護
SAP 運用システムをデプロイするときは、ホット スタンバイの種類の高可用性構成を検討する必要があります。 特に、完全なパフォーマンスとスケーラビリティを実現するには事前にデータをメモリに読み込む必要がある SAP HANA では、Azure Service Healing (サービス復旧) は高可用性のための最適な手段ではありません。 

一般に、Microsoft では、docs.microsoft.com の SAP ワークロード セクションに記載されている高可用性構成とソフトウェア パッケージのみがサポートされています。 SAP ノート [#1928533](https://launchpad.support.sap.com/#/notes/1928533) でも同じ内容を読むことができます。 Microsoft では、Microsoft と SAP ワークロードのドキュメントに記載されていない他の高可用性サードパーティ製ソフトウェア フレームワークのサポートを提供していません。 そのような場合、高可用性フレームワークのサードパーティ サプライヤーは、サポート プロセスにお客様として関与する必要がある高可用性構成のサポート パーティです。 この記事では、例外について説明します。 

一般に、Microsoft でサポートされる Azure VM または HANA Large Instances ユニットでの高可用性構成は制限されています。 HANA Large Instances でサポートされるシナリオについては、「[HANA L インスタンスのサポートされるシナリオ](./hana-supported-scenario.md)」をご覧ください。

Azure VM では、次の高可用性構成が DBMS レベルでサポートされています。

- SUSE および Red Hat 上の Linux Pacemaker に基づく SAP HANA システム レプリケーション。 詳細については以下の記事を参照してください。
    - [SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)
    - [Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)
- SUSE および Red Hat 上の [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) を使用する SAP HANA スケールアウト n+m 構成。 詳細については以下の記事を参照してください。
    - [SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- Windows スケールアウト ファイル サービスに基づく SQL Server フェールオーバー クラスター。 ただし、運用システムでは、クラスタリングではなく SQL Server Always On を使用することをお勧めします。 SQL Server Always On では、個別のストレージを使用して可用性を向上させることができます。 詳細については、次の記事を参照してください。 
    - [Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンスを構成する](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On は、Windows オペレーティング システムで Azure 上の SQL Server に対してサポートされています。 これは、Azure での運用 SQL Server インスタンスに対する既定の推奨事項です。 詳細については、次の記事を参照してください。
    - [Azure Virtual Machines での SQL Server Always On 可用性グループの概要](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)。
    - [さまざまな地域に存在する Azure 仮想マシンに Always On 可用性グループを構成します](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)｡
    - [Azure の AlwaysOn 可用性グループに使用するロード バランサーの構成](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)。
- Windows および Oracle Linux 用の Oracle Data Guard。 Oracle Linux の詳細については、次の記事を参照してください。
    - [Azure Linux 仮想マシンで Oracle Data Guard を実装する](../oracle/configure-oracle-dataguard.md)
- SUSE および RHEL 上の IBM DB2 HADR。Pacemaker を使用する SUSE と RHEL の詳細なドキュメントについては、以下を参照してください。
    - [Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server 上の Azure VM での IBM Db2 LUW の高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)
- 以下のドキュメントで詳しく説明されている SAP ASE と SAP maxDB の構成。
    - [SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sapase.md)
    - [Azure VM 上の SAP MaxDB、liveCache、Content Server のデプロイ](./dbms_guide_maxdb.md)
- HANA Large Instances の高可用性シナリオについては、以下で詳しく説明されています。
    - [HANA L インスタンスのサポートされるシナリオ - HSR と STONITH を使用した高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [HANA L インスタンスのサポートされるシナリオ - ホストの自動フェールオーバー (1+1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 上で説明したシナリオのいずれでも、1 つの VM で複数の DBMS インスタンスの構成はサポートされていません。 各ケースでは、VM ごとにデプロイできるデータベース インスタンスは 1 つだけで、説明されている高可用性の方法で保護することができます。 Windows または Pacemaker の同じフェールオーバー クラスターで複数の DBMS インスタンスを保護することは、現時点ではサポートされて **いません**。 また、Oracle Data Guard は、VM デプロイごとに 1 つのインスタンスのケースに対してのみサポートされます。 

さまざまなデータベース システムで、1 つの DBMS インスタンスで複数のデータベースをホストできます。 SAP HANA の場合と同様に、複数のデータベースを複数のデータベース コンテナー (MDC) でホストできます。 これらのマルチデータベース構成が 1 つのフェールオーバー クラスター リソース内で動作している場合、これらの構成はサポートされます。 サポートされない構成は、複数のクラスター リソースが必要になる場合です。 1 つの SQL Server インスタンスの下に複数の SQL Server 可用性グループを定義する構成の場合です。


![DBMS の HA 構成](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

DBMS やオペレーティング システムによっては、Azure ロード バランサーなどのコンポーネントがソリューション アーキテクチャの一部として、必要な場合と、必要でない場合があります。 

特に、maxDB では、ストレージ構成が異なる必要があります。 maxDB では、高可用性構成のためにデータ ファイルとログ ファイルを共有ストレージに配置する必要があります。 maxDB の場合にのみ、高可用性のために共有ストレージがサポートされます。 他のすべての DBMS では、ノードごとに個別のストレージ スタックがサポートされている唯一のディスク構成です。

他の高可用性フレームワークが存在することがわかっており、Microsoft Azure でも動作することがわかっています。 ただし、Microsoft ではそれらのフレームワークをテストしていません。 それらのフレームワークを使用して高可用性構成を構築する場合は、そのソフトウェアのプロバイダーと協力して次のことを行う必要があります。

- デプロイ アーキテクチャを開発する
- アーキテクチャをデプロイする
- アーキテクチャをサポートする

> [!IMPORTANT]
> Microsoft Azure Marketplace には、Azure ネイティブ ストレージ上でストレージ ソリューションを提供するさまざまなソフト アプライアンスが用意されています。 これらのソフト アプライアンスを使用して、スタンバイ ノードが必要な SAP HANA スケールアウト デプロイで理論的に使用できる NFS 共有を作成することもできます。 さまざまな理由により、これらのストレージ ソフト アプライアンスは、Microsoft と SAP on Azure による DBMS デプロイのいずれでもサポートされていません。 SMB 共有への DBMS のデプロイは、現時点ではサポートされていません。 NFS 共有への DBMS のデプロイは、[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) の NFS 4.1 共有に制限されています。


## <a name="high-availability-for-sap-central-service"></a>SAP セントラル サービスの高可用性
SAP セントラル サービスは、SAP 構成の 2 番目の単一障害点です。 そのため、これらのセントラル サービス プロセスも保護する必要があります。 SAP ワークロードに対しては次のようなオファーがサポートされ、ドキュメント化されています。

- sapmnt とグローバル トランスポート ディレクトリ用に Windows スケールアウト ファイル サービスを使用する Windows フェールオーバー クラスターサーバー。 詳細については、次の記事を参照してください。
    - [Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-file-share.md)
    - [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](./sap-high-availability-infrastructure-wsfc-file-share.md)
- sapmnt とグローバル トランスポート ディレクトリ用に [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) に基づく SMB 共有を使用する Windows フェールオーバー クラスターサーバー。 詳細については以下の記事を参照してください。
    - [SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](./high-availability-guide-windows-netapp-files-smb.md)
- SIOS `Datakeeper` に基づく Windows フェールオーバー クラスター サーバー。 Microsoft によって文書化されていますが、このソリューションを使用するときに SIOS サポートを利用できるように、SIOS とのサポート関係が必要です。 詳細については、次の記事を参照してください。
    - [Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [SAP ASCS/SCS 用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- ファイル レプリケーションのために 2 つの SUSE VM と `drdb` を使用して高可用性 NFS 共有が作成されている SUSE オペレーティング システム上の Pacemaker。 詳細については以下のドキュメントを参照してください
    - [SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) によって提供される NFS 共有を利用する SUSE オペレーティング システム上の Pacemaker。 詳細については以下を参照してください
    - [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)
- `glusterfs` クラスターでホストされた NFS 共有を使用する Red Hat オペレーティング システム上の Pacemaker。 詳細については次の記事を参照してください
    - [Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](./high-availability-guide-rhel.md)
    - [Red Hat Enterprise Linux for SAP NetWeaver における Azure VM 上の `GlusterFS`](./high-availability-guide-rhel-glusterfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) でホストされた NFS 共有を使用する Red Hat オペレーティング システム上の Pacemaker。 詳細については、次の記事を参照してください
    - [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](./high-availability-guide-rhel-netapp-files.md)

この一覧のソリューションでは、`Datakeeper` 製品をサポートし、問題が発生した場合に直接 SIOS と連携できるよう、SIOS とのサポート関係が必要です。 Windows、Red Hat、SUSE OS のライセンス方法によっては、示されている高可用性構成の完全なサポートのため、OS プロバイダーとのサポート契約が必要になる場合もあります。

この構成は、次のように表すこともできます。

![DBMS と ASCS HA の構成](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

図の右側には、高可用性の SAP セントラル サービスが示されています。 問題が発生した場合にフェールオーバーできるフェールオーバー クラスター フレームワークで SAP セントラル サービスを保護するだけでなく、高可用性の NFS または SMB 共有、または単一の VM の存在に関係なく sapmnt とグローバル トランスポート ディレクトリを使用できるようにするための Windows 共有ディスクが必要になります。 Windows フェールオーバー クラスター サーバーや Pacemaker など、一部のソリューションでは、正常なノードにトラフィックを送信またはリダイレクトするために Azure ロード バランサーが必要になります。

示されている一覧では、Oracle Linux オペレーティング システムについては説明されていません。 Oracle Linux では、クラスター フレームワークとして Pacemaker がサポートされていません。 SAP システムを Oracle Linux にデプロイし、Oracle Linux 用の高可用性フレームワークが必要な場合は、サードパーティのサプライヤーと協力する必要があります。 サプライヤーの 1 つは SIOS で、その Protection Suite for Linux は SAP on Azure でサポートされています。 詳細については、SAP ノート「[#1662610 - Support details for SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610)」(#1662610 - SIOS の Protection Suite for Linux のサポートの詳細) を参照してください。



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>上記の SAP セントラル サービスのシナリオでサポートされているストレージ
SAP セントラル サービス クラスターのシナリオで使用できる品質の高可用性 NFS または SMB 共有が提供されるのは Azure Storage の種類のサブセットのみなので、サポートされているストレージの種類の一覧は次のとおりです。

- Windows スケールアウト ファイル サーバーを使用する Windows フェールオーバー クラスター サーバーは、Azure NetApp Files を除くすべてのネイティブ Azure Storage の種類にデプロイできます。 ただし、スループットと IOPS のサービス レベル アグリーメントが優れているため、Premium Storage を利用することをお勧めします。
- Azure NetApp Files 上の Windows フェールオーバー クラスター サーバーと SMB は、Azure NetApp Files でサポートされています。 Azure Files サービスでの SMB 共有は、現時点ではサポートされて **いません**。
- SIOS `Datakeeper` に基づく Windows フェールオーバー クラスター サーバーと Windows 共有ディスクは、Azure NetApp Files を除くすべてのネイティブ Azure Storage の種類にデプロイできます。 ただし、スループットと IOPS のサービス レベル アグリーメントが優れているため、Premium Storage を利用することをお勧めします。
- Azure NetApp Files 上の NFS 共有を使用する SUSE または Red Hat Pacemaker は、Azure NetApp Files でサポートされています。 
- 2 つの VM 間に `drdb` 構成を使用する SUSE Pacemaker は、Azure NetApp Files を除くネイティブの Azure Storage の種類を使用してサポートされています。 ただし、スループットと IOPS のサービス レベル アグリーメントが優れているため、Premium Storage を利用することをお勧めします。
- NFS 共有を提供するために `glusterfs` を使用する Red Hat Pacemaker は、Azure NetApp Files を除くネイティブの Azure Storage の種類を使用してサポートされています。 ただし、スループットと IOPS のサービス レベル アグリーメントが優れているため、Premium Storage を利用することをお勧めします。

> [!IMPORTANT]
> Microsoft Azure Marketplace には、Azure ネイティブ ストレージ上でストレージ ソリューションを提供するさまざまなソフト アプライアンスが用意されています。 これらのソフトアプライアンスは、NFS または SMB 共有の作成にも使用でき、理論的にはフェールオーバー クラスター化された SAP セントラル サービスでも使用することができます。 Microsoft では、これらのソリューションは SAP ワークロードに対して直接はサポートしていません。 このようなソリューションを使用して NFS または SMB 共有を作成する場合は、ストレージ ソフト アプライアンスのソフトウェアを所有しているサードパーティから、SAP セントラル サービス構成のサポートを受ける必要があります。


## <a name="multi-sid-sap-central-services-failover-clusters"></a>マルチ SID の SAP セントラル サービス フェールオーバー クラスター
大規模な SAP ランドスケープで必要な VM の数を減らすため、SAP では、複数の異なる SAP システムの SAP セントラル サービス インスタンスを、フェールオーバー クラスター構成で実行できます。 NetWeaver または S/4HANA の運用システムが 30 以上ある場合について考えます。 マルチ SID クラスタリングを使用しない場合、これらの構成では、30 以上の Windows または Pacemaker フェールオーバー クラスター構成で、60 以上の VM が必要になります。 それに加えて、DBMS フェールオーバー クラスターが必要です。 フェールオーバー クラスター構成の 2 つのノードに複数の SAP セントラル サービスをデプロイすると、VM の数を大幅に減らすことができます。 ただし、単一の 2 ノード クラスター構成に複数の SAP セントラル サービス インスタンスをデプロイすると、いくつかの欠点もあります。 クラスター構成内の 1 つの VM に関する問題が、複数の SAP システムにも当てはまります。 クラスター構成で実行されているゲスト OS をメンテナンスするには、複数の運用 SAP システムが影響を受けるため、より多くの調整が必要です。 SAP LaMa のようなツールでは、システム複製プロセスでマルチ SID クラスタリングがサポートされていません。

Azure では、マルチ SID クラスター構成は、ENSA1 と ENSA2 がある Windows オペレーティング システムでサポートされています。 古いエンキュー レプリケーション サービス アーキテクチャ (ENSA1) と新しいアーキテクチャ (ENSA2) を 1 つのマルチ SID クラスターで組み合わせないことをお勧めします。 このようなアーキテクチャの詳細については、次の記事をご覧ください

- [Azure で Windows Server フェールオーバー クラスタリングと共有ディスクを使用する SAP ASCS/SCS インスタンスのマルチ SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Azure での Windows Server フェールオーバー クラスタリングとファイル共有による SAP ASCS/SCS インスタンスのマルチ SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

SUSE では、Pacemaker に基づくマルチ SID クラスターもサポートされています。 ここまでの構成は以下に対してサポートされています。

- 最大 5 つの SAP ASCS/SCS インスタンス
- 古いエンキュー レプリケーション サービス アーキテクチャ (ENSA1)
- 2 ノード Pacemaker クラスター構成

この構成については、「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](./high-availability-guide-suse-multi-sid.md)」を参照してください

エンキュー レプリケーション サーバーを使用するマルチ SID クラスターは次の図のようになります

![エンキュー レプリケーション サーバーを使用するマルチ SID クラスターを示す図。](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA のスケールアウト シナリオ
SAP HANA スケールアウト シナリオは、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に列記されている HANA 認定 Azure VM のサブセットについてサポートされています。 "Clustering" 列が "Yes" になっているすべての VM は、OLAP または S/4HANA のスケールアウトに使用できます。スタンバイを使用しない構成は、次の Azure Storage の種類でサポートされます。 

- Azure Premium Storage (/hana/log ボリューム用の Azure 書き込みアクセラレータを含む)
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

スタンバイ ノードを使用する OLAP または S/4HANA の SAP HANA スケールアウト構成は、Azure NetApp Files でホストされる NFS 共有でのみサポートされています。

スタンバイ ノードがある場合とない場合の正確なストレージ構成の詳細については、次の記事を参照してください。

- [SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md) 
- [SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP サポート ノート #2080991](https://launchpad.support.sap.com/#/notes/2080991)

HANA Large Instances でサポートされる HANA スケールアウト構成の詳細については、次のドキュメントを参照してください。

- [スタンバイを使用する HANA Large Instances スケールアウトでサポートされるシナリオ](./hana-supported-scenario.md#scale-out-with-standby)
- [スタンバイを使用しない HANA Large Instances スケールアウトでサポートされるシナリオ](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>ディザスター リカバリーのシナリオ
さまざまなディザスター リカバリー シナリオがサポートされています。 ディザスター アーキテクチャは、グリッドを終了する完全な Azure リージョンを補正する必要があるアーキテクチャと定義されます。 つまり、SAP ランドスケープを実行するためのターゲットとして、ディザスター リカバリー ターゲットを別の Azure リージョンにする必要があります。 方法と構成は、DBMS レイヤーと非 DBMS レイヤーで異なります。 

### <a name="dbms-layer"></a>DBMS レイヤー
DBMS レイヤーでは、Always On、Oracle Data Guard、DB2 HADR、SAP ASE Always On、HANA システム レプリケーションなどの DBMS ネイティブ レプリケーション メカニズムを使用する構成がサポートされています。 このような場合のレプリケーション ストリームは非同期であることが必須です。一方、単一の Azure リージョン内にデプロイされる一般的な高可用性シナリオでは同期です。 このようなサポートされる DBMS ディザスター リカバリー構成の一般的な例については、「[Azure リージョンの枠を越えた SAP HANA の可用性](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)」を参照してください。 そのセクションの 2 番目の図では、例として HANA のシナリオが説明されています。 SAP アプリケーションでサポートされている主なデータベースはすべて、このようなシナリオでデプロイできます。

小さい VM ではワークロードのトラフィックがいっぱいになることはないため、ディザスター リカバリー リージョンのターゲット インスタンスとしてそのような VM の使用がサポートされています。 その場合は、以下の点に注意してください。

- 小さい VM の種類では、小さい VM より多くのディスクを接続できません
- 小さい VM では、ネットワークとストレージのスループットが少なくなります
- 1 つの Azure 可用性セットに異なる VM が収集されているときに VM ファミリ間でサイズを変更すると、または VM の M シリーズ ファミリと Mv2 ファミリの間でサイズの変更を行うと、問題になることがあります
- 最小限の遅延で変更のストリームを受信できるデータベース インスタンスの CPU とメモリの消費量、および最小限の遅延でこれらの変更をデータに適用するのに十分な CPU とメモリ リソース  

さまざまな VM サイズの制限について詳しくは、[こちら](../../sizes.md)を参照してください 

DR ターゲットのデプロイ方法としてもう 1 つサポートされているのは、非運用 SAP インスタンスの非運用 DBMS インスタンスが実行されている VM に 2 つ目の DBMS インスタンスをインストールする方法です。 この方法は、DR シナリオのメイン インスタンスとして機能する必要がある特定のターゲット インスタンスに必要なメモリ、CPU リソース、ネットワーク帯域幅、ストレージ帯域幅を把握する必要があるため、少々困難になる場合があります。 特に HANA では、データが DR ターゲット インスタンスに事前に読み込まれないように、共有ホスト上で DR ターゲットとして機能するインスタンスを構成することを強くお勧めします。

HANA Large Instance の DR シナリオについては、次のドキュメントを参照してください。

- [ストレージ レプリケーションを使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [ストレージ レプリケーションを使用する DR (多目的) を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [ストレージ レプリケーションを使用する DR (多目的) を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [HSR と DR を使用した高可用性とストレージ レプリケーション](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [ストレージ レプリケーションを使用しする DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [HSR を使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [単一ノードの HSR から DR (コスト最適化)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [HSR の高可用性とディザスター リカバリー](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [HSR の高可用性とディザスター リカバリー (コスト最適化)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [HSR を使用する DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> SAP ワークロード下での DBMS のデプロイについて、[Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) の使用はテストされていません。 そのため、現時点では、SAP システムの DBMS レイヤーではサポートされていません。 一覧で示されていない Microsoft および SAP による他のレプリケーション方法は、サポートされていません。 異なる Azure リージョン間で SAP システムの DBMS レイヤーをレプリケートするためにサードパーティ製ソフトウェアを使用する場合は、ソフトウェアのベンダーによってサポートされている必要があり、Microsoft と SAP のサポート チャネルではサポートされません。 
 
## <a name="non-dbms-layer"></a>非 DBMS レイヤー
SAP アプリケーション レイヤーおよび最終的に必要な共有やストレージの場所については、次の 2 つの主要なシナリオがお客様によって利用されています。

- 2 番目の Azure リージョンのディザスター リカバリー ターゲットは、どのような運用目的または非運用目的にも使用されていません。 このシナリオでは、ディザスター リカバリー ターゲットとして機能する VM をデプロイしないのが理想的であり、運用 SAP アプリケーション レイヤーのイメージとイメージに対する変更は、ディザスター リカバリー リージョンにレプリケートされます。 そのようなタスクを実行できる機能は、[Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md) です。 Azure Site Recovery では、このような Azure から Azure へのレプリケーション シナリオがサポートされています。 
- ディザスター リカバリー ターゲットは、非運用環境のシステムによって実際に使用されている VM です。 通常、SAP ランドスケープ全体は、運用システムと非運用システムが 2 つの異なる Azure リージョンに分かれています。 お客様のデプロイの多くでは、運用システムと同等の非運用システムが使用されます。 運用アプリケーション インスタンスが、アプリケーション レイヤーの非運用システムに事前にインストールされています。 フェールオーバーが発生した場合、非運用インスタンスがシャットダウンされ、運用 VM の仮想名が非運用 VM に移動されて (DNS で新しい IP アドレスが割り当てられた後)、インストール済みの運用インスタンスが起動されます

### <a name="sap-central-services-clusters"></a>SAP セントラル サービス クラスター
共有ディスク (Windows)、SMB 共有 (Windows)、または NFS 共有を使用する SAP セントラル サービス クラスターは、レプリケートが少し困難です。 Windows 側では、Windows ストレージ レプリケーションをソリューションとして使用できます。 Linux では、rsync が実用的なソリューションです。



## <a name="non-supported-scenario"></a>サポートされていないシナリオ
Azure アーキテクチャで SAP ワークロードに対してサポートされていない一連のシナリオがあります。 **サポートされていない** とは、SAP と Microsoft ではこれらの構成をサポートできず、そのようなアーキテクチャを確立するためにソフトウェアを提供した最終的なサードパーティに任せる必要があることを意味します。 次の 2 つのカテゴリがあります。

- ストレージ ソフト アプライアンス: Azure Marketplace でいくつかのストレージ ソフト アプライアンスが提供されています。 一部のベンダーでは、Azure 上で SAP ソフトウェアに関連してそれらのストレージ ソフト アプライアンスを使用する方法について、独自のドキュメントが提供されています。 そのようなストレージ ソフト アプライアンスが含まれる構成またはデプロイのサポートは、それらのストレージ ソフト アプライアンスのベンダーによって提供される必要があります。 このことは、[SAP サポート ノート #2015553](https://launchpad.support.sap.com/#/notes/2015553) にも記載されています
- 高可用性フレームワーク: Azure 上の SAP ワークロード用にサポートされている高可用性フレームワークは、Pacemaker と Windows Server フェールオーバー クラスターのみです。 前述のように、SIOS `Datakeeper` のソリューションについては、Microsoft によって説明および文書化されています。 ただし、SIOS `Datakeeper` のコンポーネントは、それらのコンポーネントを提供するベンダーとして SIOS を通してサポートされる必要があります。 さまざまな SAP ノートには、他の認定された高可用性フレームワークの一覧も記載されています。 それらの一部は、サードパーティ ベンダーによって Azure についても認定されています。 ただし、それらの製品を使用した構成のサポートは、製品のベンダーから提供される必要があります。 ベンダーが異なると、SAP サポートへの統合プロセスも異なります。 Azure にデプロイされる SAP 構成で製品を使用することを決定する前に、特定のベンダーに最適なサポート プロセスを明確にする必要があります。
- 共有ディスク上にデータベース ファイルが存在する共有ディスク クラスターは、maxDB を除き、サポートされていません。 他のすべてのデータベースで高可用性シナリオを構成するためにサポートされているソリューションは、SMB 共有や NFS 共有または共有ディスクではなく、別のストレージ場所を使用することです

次のような他のシナリオはサポートされていません。

- SAP の一般的なアーキテクチャで SAP アプリケーション層と SAP DBMS 層の間のネットワーク待機時間が長くなるようなデプロイ シナリオ (NetWeaver、S/4HANA、`Hybris` など)。 これには次のものが含まれます
    - 一方の層をオンプレミスにデプロイし、他の層を Azure にデプロイする場合
    - システムの SAP アプリケーション層を、DBMS 層とは別の Azure リージョンにデプロイする場合
    - 一方の層を Azure に併置されているデータセンターにデプロイし、他の層を Azure にデプロイする場合。ただし、そのようなアーキテクチャ パターンが Azure ネイティブ サービスによって提供されている場合を除く
    - SAP アプリケーション層と DBMS レイヤーの間にネットワーク仮想アプライアンスをデプロイする場合
    - SAP DBMS 層または SAP グローバル トランスポート ディレクトリ用に、Azure データセンターに併置されているデータセンターでホストされているストレージを利用する場合
    - 2 つのレイヤーを 2 つの異なるクラウド ベンダーでデプロイする場合。 たとえば、DBMS 層を Oracle クラウド インフラストラクチャにデプロイし、アプリケーション層を Azure にデプロイする場合
- 複数インスタンスの HANA Pacemaker クラスター構成
- Windows でサポートされている SAP データベース用に、ANF 上の SOFS または SMB による共有ディスクを使用する Windows クラスター構成。 代わりに、特定のデータベースのネイティブ高可用性レプリケーションを使用し、個別のストレージ スタックを使用することをお勧めします
- ANF 上の NFS 共有にあるデータベース ファイルを使用する、Linux でサポートされている SAP データベースのデプロイ。ただし、SAP HANA を除く
- Windows と Oracle Linux 以外のゲスト OS への Oracle DBMS のデプロイ。 [SAP サポート ノート #2039619](https://launchpad.support.sap.com/#/notes/2039619) も参照してください

次のような、テストされていないため経験がないシナリオ:

- DBMS レイヤーの VM をレプリケートする Azure Site Recovery。 結果として、可能性のあるディザスター リカバリー構成としては、データベース ネイティブの非同期レプリケーション機能を利用することをお勧めします
 

## <a name="next-steps"></a>次の手順
次の手順については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」を参照してください




  