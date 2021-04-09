---
title: SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項 | Microsoft Docs
description: SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/20/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: e978baa556f1bc1cfea0ccd5abbbf92dc77b1622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504016"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



このガイドは、Microsoft Azure 上で SAP ソフトウェアを実装およびデプロイする方法に関するドキュメントの一部です。 このガイドを読む前に、[計画および実装ガイド][planning-guide]と、計画ガイドに記載されている記事をお読みください。 このドキュメントでは、Azure のサービスとしてのインフラストラクチャ (IaaS) 機能を使用して Microsoft Azure 仮想マシン (VM) 上で SAP 関連の DBMS システムをデプロイする場合の一般的な側面について説明します。

特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Note を補足する内容となっています。

このドキュメントでは、Azure VM で SAP 関連 DBMS システムを実行する際の考慮事項について説明します。 この章では、特定の DBMS システムについてはほとんど言及していません。 特定の DBMS システムについては、このドキュメントの後で説明します。

## <a name="definitions"></a>定義
このドキュメントでは、次の用語を使用します。

* **IaaS**:サービスとしてのインフラストラクチャ。
* **PaaS**:サービスとしてのプラットフォーム。
* **SaaS**:サービスとしてのソフトウェア。
* **SAP コンポーネント**:個々の SAP アプリケーション (ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager、Enterprise Portal (EP) など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、Business Objects などの非 NetWeaver ベース アプリケーションに基づいて作成できます。
* **SAP 環境**:開発、品質保証、トレーニング、ディザスター リカバリー、運用など、ビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
* **SAP ランドスケープ**:この用語は、お客様の IT 環境内にある SAP 資産全体を指します。 SAP ランドスケープには、運用環境と非運用環境のすべてが含まれます。
* **SAP システム**:DBMS レイヤーと (SAP ERP 開発システム、SAP Business Warehouse テスト システム、SAP CRM 運用システムなどの) アプリケーション レイヤーの組み合わせ。 Azure のデプロイでは、この 2 つのレイヤーをオンプレミスと Azure に分けることはできません。 その結果、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 SAP ランドスケープの異なるシステムを Azure またはオンプレミスにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。
* **クロスプレミス**:オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または Azure ExpressRoute 接続を利用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 この種のデプロイは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 

    この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、サブスクリプションの Azure 資産に拡張されます。 この拡張により、VM をオンプレミス ドメインに含めることができます。 オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM 上でサービスを実行できます (DBMS サービスなど)。 オンプレミスにデプロイした VM 間、および Azure にデプロイした VM 間の通信および名前解決は可能です。 これは、Azure 上に SAP 資産をデプロイするために最も一般的に使用されるシナリオです。 詳細については、「[VPN ゲートウェイの計画と設計](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)」を参照してください。

> [!NOTE]
> SAP システムのクロスプレミス デプロイは、SAP システムを実行する Azure 仮想マシンがオンプレミス ドメインのメンバーとなっていて、運用 SAP システムでサポートされるデプロイです。 クロスプレミス構成では、Azure に SAP ランドスケープの一部または全部をデプロイする場合にサポートされます。 Azure で完全な SAP ランドスケープを実行する場合でも、これらの VM をオンプレミス ドメインと Active Directory/LDAP の一部に含める必要があります。 
>
> ドキュメントの以前のバージョンでは、ハイブリッド IT シナリオについて言及されていました。 "*ハイブリッド*" という用語は、オンプレミスと Azure の間にクロスプレミス接続があるという事実に基づいています。 このケースでは、ハイブリッドとは Azure の VM がオンプレミス Active Directory の一部であることも意味します。
>
>

一部の Microsoft ドキュメントでは、クロスプレミス シナリオについて、特に DBMS 高可用性構成の場合に、少し異なる記述をしています。 SAP 関連ドキュメントでは、クロスプレミス シナリオは、要するに、サイト間接続またはプライベート [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 接続であり、オンプレミスと Azure の間で分散された SAP ランドスケープです。

## <a name="resources"></a>リソース
Azure 上の SAP ワークロードに関するさまざまな記事があります。 [Azure 上の SAP ワークロードの作業の開始](./get-started.md)に関するページをまず確認してから、興味のある領域を選択してください。

次の SAP Note は、このドキュメントで扱う領域に関する SAP on Azure に関連したものです。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP applications on Azure: Supported products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] |SAP on Microsoft Azure:Support prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Troubleshooting enhanced Azure monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2178632] |Key monitoring metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1409604] |Virtualization on Windows:Enhanced monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能) |
| [2191498] |SAP on Linux with Azure:Enhanced monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能) |
| [2039619] |SAP applications on Microsoft Azure using the Oracle database:Supported products and versions (Oracle データベースを使用した Microsoft Azure 上の SAP アプリケーション: サポートされている製品とバージョン) |
| [2233094] |DB6:SAP applications on Azure using IBM DB2 for Linux, UNIX, and Windows: 関連情報 |
| [2243692] |Linux on Microsoft Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] |SUSE LINUX Enterprise Server 12:インストールに関する注記 |
| [2002167] |Red Hat Enterprise Linux 7.x:インストールとアップグレード |
| [2069760] |Oracle Linux 7.x SAP installation and upgrade (Oracle Linux 7.x SAP のインストールとアップグレード) |
| [1597355] |Linux のスワップ領域に関する推奨事項 |
| [2171857] |Oracle Database 12c: File system support on Linux (Oracle Database 11g: Linux でのファイル システムのサポート) |
| [1114181] |Oracle Database 11g: File system support on Linux (Oracle Database 11g: Linux でのファイル システムのサポート) |


Linux に関するすべての SAP Note については、[SAP コミュニティ wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) を参照してください。

Microsoft Azure のアーキテクチャと Microsoft Azure 仮想マシンのデプロイおよび操作方法に関する実用的な知識が必要です。 詳細については、[Azure のドキュメント](../../../index.yml)を参照してください。

一般的に、Windows、Linux、および DBMS のインストールと構成は、オンプレミスに設置する仮想マシンまたはベア メタル マシンと本質的に同じです。 Azure IaaS を使用する場合、アーキテクチャおよびシステム管理の実装の決定については異なる部分があります。 このドキュメントでは、Azure IaaS を使用する場合に知っておくべき特定のアーキテクチャおよびシステム管理の相違について説明します。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS デプロイ用の VM のストレージ構造
この章に進むにあたって、以下に記載されている情報を読んで理解しておいてください。

- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)
- [SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)
- [Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)
- [Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](./sap-planning-supported-configurations.md) 

この章を読む前に、別の VM シリーズについて把握し、Standard Storage と Premium Storage の違いについて理解しておく必要があります。 

Azure ブロック ストレージについては、Azure Managed Disks の使用を強くお勧めします。 Azure Managed Disks の詳細については、[Azure VM のマネージド ディスクの概要](../../managed-disks-overview.md)に関する記事を参照してください。

基本的な構成では、通常、オペレーティング システム、DBMS、および最終的に導入する SAP バイナリとデータベース ファイルとを分けたデプロイ構造にすることをお勧めしています。 以前の推奨事項を変更する場合は、次の項目に個別の Azure ディスクを使用することをお勧めします。

- オペレーティング システム (ベース VHD または OS VHD)
- データベース管理システムの実行可能ファイル
- /usr/sap のような SAP 実行可能ファイル

これらのコンポーネントを 3 つの異なる Azure ディスクに分離する構成を使用すると、DBMS または SAP 実行可能ファイルによる過剰なログまたはダンプの書き込みによって OS ディスクのディスク クォータが妨げられないため、回復性が向上します。 

DBMS のデータおよびトランザクションまたは再実行ログ ファイルは、Azure でサポートされているブロック ストレージまたは Azure NetApp Files に格納されます。 これらは別個のディスクに格納され、元の Azure オペレーティング システム イメージ VM に論理ディスクとして接続されます。 Linux のデプロイでは、特に SAP HANA に関して、異なる推奨事項がドキュメント化されています。 シナリオに応じたさまざまな種類のストレージの機能とサポートについては、「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」を参照してください。 

ディスクのレイアウトを計画するときは、次の項目間で最適なバランスを取る必要があります。

* データ ファイルの数。
* ファイルが含まれているディスクの数。
* 1 つのディスクまたは NFS 共有の IOPS クォータ。
* ディスクまたは NFS 共有ごとのデータ スループット。
* VM サイズに対して追加できるデータ ディスクの数。
* VM が提供できるストレージまたはネットワークの全体的なスループット。
* さまざまな種類の Azure Storage で考えられる待機時間。
* VM の SLA。

Azure によってデータ ディスクまたは NFS 共有ごとに IOPS クォータが適用されます。 これらのクォータは、別の Azure ブロック ストレージ ソリューションまたは共有でホストされているディスクでは異なります。 また、I/O 待機時間は、これらの異なる種類のストレージ間でも異なります。 

VM の種類ごとに、接続できるデータ ディスクの数が制限されています。 もう 1 つの制限は、たとえば Premium Storage を使用できるのは、特定の種類の VM のみであることです。 通常は、CPU とメモリの要件に基づいて、使用する特定の VM の種類を決定します。 また、通常、Premium Storage ディスクのディスク数や種類に応じてスケーリングされる IOPS、待ち時間、およびディスク スループットの要件も考慮する必要があります。 特に Premium Storage では、各ディスクで実現する必要がある IOPS とスループットの値によってディスクのサイズも決まる場合があります。

> [!NOTE]
> DBMS デプロイの場合は、すべてのデータ、トランザクション ログ、または再実行のファイルに対して、Azure Premium Storage、Ultra ディスク、または Azure NetApp Files ベースの NFS 共有 (SAP HANA 専用) を使用することをお勧めします。 デプロイするシステムが運用環境システムか非運用システムかは重要ではありません。

> [!NOTE]
> Azure の[単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) を活用するには、接続されているすべてのディスクの種類が Azure Premium Storage または Azure Ultra ディスクである必要があります。これには、ベース VHD (Azure Premium Storage) が含まれます。

> [!NOTE]
> SAP データベースが配置されているストレージ ハードウェアが、Azure データセンターに隣接して併置されているサードパーティ データセンター内にある場合は、その SAP データベースのメイン データベース ファイル (データ ファイルやログ ファイルなど) をホストすることはできません。 Azure VM でホストされているソフトウェア アプライアンスを通じて提供されるストレージも、このユース ケースではサポートされていません。 SAP DBMS ワークロードの場合、SAP データベースのデータおよびトランザクション ログ ファイル用にサポートされるストレージは、一般的にはネイティブ Azure サービスとして表現されるストレージに限られます。 異なる DBMS では、それぞれ異なる種類の Azure Storage がサポートされる場合があります。 詳細については、「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」の記事を参照してください。

データベース ファイル、ログおよび再実行ファイルの配置と、使用する Azure Storage の種類は、IOPS、待ち時間、スループットの要件に従って決まります。 特に Azure Premium Storage で十分な IOPS を確保するには、複数のディスクを使用するか、大容量の Premium Storage ディスクを使用しなければならない場合があります。 複数のディスクを使用する場合は、データ ファイルか、ログおよび redo ファイルが含まれるディスク全体にソフトウェア ストライプを構築します。 この場合、結果のストライプ セットでは、基になる Premium Storage ディスクの IOPS およびディスク スループット SLA、または Standard Storage ディスクの達成可能な最大 IOPS が累積的になります。

IOPS の要件が 1 つの VHD で提供可能な容量を超えている場合は、複数の VHD にわたるデータベース ファイルで必要な IOPS の数のバランスを取ります。 IOPS 負荷を複数のディスクに分散する最も簡単な方法は、異なるディスク間でソフトウェア ストライプを構築することです。 そして、ソフトウェア ストライプから分割した LUN に SAP DBMS のデータ ファイルの数を設定します。 ストライプ内のディスクの数は、IOPS の需要、ディスク スループットの需要、およびボリュームの需要によって決まります。


---
> ![Windows ストレージ ストライピング][Logo_Windows] Windows
>
> Windows 記憶域スペースを使用して複数の Azure VHD にわたってストライプ セットを作成することをお勧めします。 Windows Server 2012 R2 または Windows Server 2016 以上を使用してください。
>
> ![Linux ストレージ ストライピング][Logo_Linux] Linux
>
> Linux 上でのソフトウェア RAID の構築がサポートされているのは、MDADM および論理ボリューム マネージャー (LVM) のみです。 詳細については、次を参照してください。
>
> - MDADM を使用して [Linux 上にソフトウェア RAID を構成する](/previous-versions/azure/virtual-machines/linux/configure-raid)
> - LVM を使用して[ Azure 内の Linux VM 上に LVM を構成する](/previous-versions/azure/virtual-machines/linux/configure-lvm)
>
>

---

Azure Ultra ディスクの場合、ディスクのサイズに依存しない IOPS およびディスク スループットを定義できるため、ストライピングは必要ありません。


> [!NOTE]
> Azure Storage には VHD の 3 つのイメージが保存されるため、ストライプ化するときに冗長性を構成することには意味がありません。 必要なのは、I/O が異なる VHD に分散されるようにストライピングを構成することだけです。
>

### <a name="managed-or-nonmanaged-disks"></a>マネージド ディスクか非マネージド ディスクか
Azure ストレージ アカウントは、管理の構成要素であり、制限の対象でもあります。 Standard Storage アカウントと Premium Storage アカウントでは、制限事項が異なります。 機能と制限については、[Azure Storage のスケーラビリティとパフォーマンスのターゲット](../../../storage/common/scalability-targets-standard-account.md)に関するページをご覧ください。

Standard Storage の場合、ストレージ アカウントごとに IOPS の制限があることに注意してください。 [Azure Storage のスケーラビリティとパフォーマンスのターゲット](../../../storage/common/scalability-targets-standard-account.md)に関する記事で、**合計要求レート** を含む行を参照してください。 Azure サブスクリプションあたりのストレージ アカウント数には初期制限もあります。 複数のストレージ アカウントにわたる大規模な SAP ランドスケープの場合は、これらのストレージ アカウントの制限に達しないよう、VHD を分散します。 1,000 を超える VHD が搭載された数百台の仮想マシンでこの作業を行うのは困難です。

SAP ワークロードと共に DBMS デプロイに Standard Storage を使用することはお勧めしません。 したがって、Standard Storage の参照と推奨事項は、この短い[記事](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)に限定されています。

複数の Azure ストレージ アカウントにわたって VHD を計画しデプロイする管理作業を回避するため、Microsoft は、2017 年に [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を発表しました。 マネージド ディスクは、Standard Storage と Premium Storage で使用できます。 非マネージド ディスクと比較したマネージド ディスクの主なメリットは次のとおりです。

- マネージド ディスクの場合、デプロイ時に Azure によって、異なるストレージ アカウントの間で異なる VHD が自動的に分散されます。 これにより、データ量、I/O スループット、および IOPS に関するストレージ アカウントの制限に達することがなくなります。
- Azure Storage は、マネージド ディスクを使用して、Azure 可用性セットの概念に対応します。 VM が Azure 可用性セットの一部である場合、VM のベース VHD と接続ディスクは異なる障害ドメインと更新ドメインにデプロイされます。


> [!IMPORTANT]
> Azure Managed Disks の利点を考えると、一般の DBMS デプロイと SAP デプロイには Azure Managed Disks を使用することを強くお勧めします。
>

アンマネージド ディスクからマネージド ディスクに変換するには、以下を参照してください。

- [Windows 仮想マシンをアンマネージド ディスクからマネージド ディスクに変換する](../../windows/convert-unmanaged-to-managed-disks.md)。
- [Linux 仮想マシンをアンマネージド ディスクからマネージド ディスクに変換する](../../linux/convert-unmanaged-to-managed-disks.md)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM とデータ ディスクのキャッシュ
ディスクを VM にマウントする場合、Azure ストレージに配置されたこれらのディスクと VM 間の I/O トラフィックをキャッシュするかどうかを選択できます。

以下に示す推奨事項は、次のような標準 DBMS の I/O 特性を前提にしています。

- これは、ほとんどの場合、データベースのデータ ファイルに対する読み取りワークロードです。 これらの読み取りは、DBMS システムのパフォーマンスにとって重要です。
- データ ファイルに対する書き込みは、チェックポイントまたは一定のストリームに基づいてバーストで実行されます。 1 日の平均書き込み量は、読み取り量よりも少なくなります。 データ ファイルからの読み取りとは反対に、これらの書き込みは非同期で実行され、ユーザー トランザクションに悪影響を与えません。
- トランザクション ログまたは再実行ファイルからの読み取りはほとんどありません。 例外は、トランザクション ログ バックアップを実行するときのサイズの大きな I/O です。
- トランザクションまたは redo ログ ファイルに対する主な負荷は、書き込みです。 ワークロードの性質に応じて、I/O サイズを 4 KB まで小さくしたり、1 MB 以上にしたりできます。
- すべての書き込みを信頼性の高い方法でディスク上に保存する必要があります。

Standard Storage で可能なキャッシュの種類は次のとおりです。

* なし
* Read
* 読み取り/書き込み

一定の決まったパフォーマンスを得られるようにするには、DBMS 関連のデータ ファイル、ログおよび redo ファイル、およびテーブル スペースを含むすべてのディスクに対する Standard Storage のキャッシュを "**なし**" に設定します。 ベース VHD のキャッシュは既定のままにしておくことができます。

Azure Premium Storage については、次のキャッシュ オプションが存在します。

* なし
* Read
* 読み取り/書き込み
* なし + 書き込みアクセラレータ (Azure M シリーズ VM の場合のみ)
* 読み取り + 書き込みアクセラレータ (Azure M シリーズ VM の場合のみ)

Premium Storage の場合は、SAP データベースの **データ ファイルに読み取りキャッシュ** を使用し、**ログ ファイルのディスクにキャッシュなし** を選択することをお勧めします。

M シリーズのデプロイでは、DBMS のデプロイに Azure 書き込みアクセラレータを使用することをお勧めします。 Azure 書き込みアクセラレータの詳細、制限、およびデプロイについては、「[書き込みアクセラレータを有効にする](../../how-to-enable-write-accelerator.md)」を参照してください。

Ultra ディスクと Azure NetApp Files では、キャッシュ オプションは提供されません。


### <a name="azure-nonpersistent-disks"></a>Azure の非永続的ディスク
Azure VM は、VM がデプロイされた後に非永続ディスクを提供します。 VM が再起動された場合、これらのドライブ上のすべてのコンテンツは消去されます。データ ファイルのほか、データベースのログおよび redo ファイルは、どんな状況でもこれらのドライブに保存してはいけません。 例外として、データベースによっては、tempdb や temp テーブルスペースなどにこれらの非永続ドライブが適している場合があります。 これらのドライブを A シリーズ VM に使用することは避けてください。これらの非永続ドライブのスループットは、この VM ファミリで制限されているためです。 

詳細については、[Azure の Windows VM 上の一時ドライブの解説](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)に関するページを参照してください。

---
> ![Windows の非永続ディスク][Logo_Windows] Windows
>
> Azure VM の D ドライブは、Azure の計算ノード上のいくつかのローカル ディスクによってサポートされる非永続ドライブです。 これは非永続的であるため、ドライブ D の内容に加えられた変更は、VM を再起動すると失われます。 "変更" には、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションが含まれます。
>
> ![Linux の非永続ディスク][Logo_Linux] Linux
>
> Linux Azure VM は、Azure の計算ノード上のローカル ディスクによってサポートされる非永続ドライブであるドライブを /mnt/resource に自動的にマウントします。 これは非永続的であるため、/mnt/resource 内のコンテンツに加えられた変更は、VM を再起動すると失われます。 "変更" には、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションが含まれます。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage の回復性
Microsoft Azure Storage は、ベース VHD を、OS のほか、接続されているディスクまたは BLOB と共に、3 つ以上の別個のストレージ ノードに格納します。 この種類のストレージは、ローカル冗長ストレージ (LRS) と呼ばれます。 LRS は、Azure 内のすべての種類のストレージの既定値です。

これ以外にも冗長化の方法があります。 詳細については、「[Azure Storage のレプリケーション](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」をご覧ください。

> [!NOTE]
> Azure Premium Storage、Ultra ディスク、および Azure NetApp Files (SAP HANA 専用) は、DBMS VM と、データベース、ログ、および再実行のファイルを格納するディスクに推奨される種類のストレージです。 これらの種類のストレージで使用できる冗長化の方法は LRS のみです。 そのため、別の Azure リージョンまたは可用性ゾーンへのデータベース データのレプリケーションを有効にするようにデータベースの方式を構成する必要があります。 データベースの方式には、SQL Server Always On、Oracle Data Guard、および HANA システム レプリケーションがあります。


> [!NOTE]
> DBMS デプロイの場合、Standard Storage に geo 冗長ストレージ (GRS) を使用することは推奨されません。 GRS はパフォーマンスに深刻な影響を及ぼすほか、VM に接続されている異なる VHD 間で書き込み順序を考慮しません。 異なる VHD 間で書き込み順序が考慮されないと、レプリケーション ターゲット側でデータベースの不整合が生じる可能性があります。 この状況は、ソース VM 側でよくあるように、データベースとログおよび redo ファイルが複数の VHD に分散している場合に発生します。



## <a name="vm-node-resiliency"></a>VM ノードの回復性
Azure では、VM に複数の異なる SLA を提供しています。 詳細については、「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)」の最新リリースを参照してください。 DBMS レイヤーは、SAP システムにおける可用性にとって非常に重要であるため、可用性セット、Availability Zones、およびメンテナンス イベントについて理解しておく必要があります。 これらの概念の詳細については、「[Azure での Windows 仮想マシンの可用性の管理](../../availability.md)」および [Azure での Linux 仮想マシンの可用性の管理](../../availability.md)に関するページを参照してください。

SAP ワークロードを含む運用 DBMS シナリオの最小推奨事項は、次のとおりです。

- 同じ Azure リージョン内の別個の可用性セットに 2 つの VM をデプロイします。
- これら 2 つの VM を同じ Azure 仮想ネットワーク内で実行し、同じサブネットの NIC を接続します。
- 2 つ目の VM のホット スタンバイを維持するためにデータベース方法を使用します。 方法としては、SQL Server Alwayson、Oracle Data Guard、または HANA システム レプリケーションを使用することができます。

別の Azure リージョンに 3 番目の VM をデプロイし、同じデータベース方式を使用して別の Azure リージョンに非同期レプリカを提供することもできます。

Azure 可用性セットの設定方法については、[このチュートリアル](../../windows/tutorial-availability-sets.md)を参照してください。



## <a name="azure-network-considerations"></a>Azure ネットワークに関する考慮事項
大規模な SAP のデプロイでは、[Azure 仮想データセンター](/azure/architecture/vdc/networking-virtual-datacenter)のブループリントを使用します。 これを、仮想ネットワークの構成や、組織のさまざまな部分に対するアクセス許可とロールの割り当てに使用します。

これらのベスト プラクティスは、数百件もの顧客デプロイの結果です。

- SAP アプリケーションがデプロイされている仮想ネットワークは、インターネットにアクセスできません。
- データベース VM は、アプリケーション層と同じ仮想ネットワーク内で実行され、SAP アプリケーション層とは別のサブネットに分離されます。
- 仮想ネットワーク内の VM には、プライベート IP アドレスが静的に割り当てられます。 詳しくは、「[Azure における IP アドレスの種類と割り当て方法](../../../virtual-network/public-ip-addresses.md)」をご覧ください。
- DBMS VM を出入りする際のルーティングの制限は、ローカルの DBMS VM にインストールされているファイアウォールでは設定 "*されません*"。 代わりに、トラフィック ルーティングは[ネットワーク セキュリティ グループ (NSG)](../../../virtual-network/network-security-groups-overview.md) を使用して定義されます。
- DBMS VM へのトラフィックを分離および隔離するには、異なる NIC を VM に割り当てます。 各 NIC は異なる IP アドレスを取得し、異なる仮想ネットワーク サブネットに割り当てられます。 サブネットごとに異なる NSG ルールがあります。 ネットワーク トラフィックを分離または隔離するのは、ルーティングのための 1 つの手段です。 ネットワーク スループットのクォータを設定するためには使用されません。

> [!NOTE]
> Azure を介して静的 IP アドレスを割り当てるということは、それらを個々の仮想 NIC に割り当てるということです。 ゲスト OS 内の静的 IP アドレスを仮想 NIC に割り当てないでください。 Azure Backup のような一部の Azure サービスは、少なくともプライマリ仮想 NIC が静的 IP アドレスではなく DHCP に設定されているという事実に依存します。 詳細については、「[Azure 仮想マシンのバックアップのトラブルシューティング](../../../backup/backup-azure-vms-troubleshoot.md#networking)」を参照してください。 複数の静的 IP アドレスを 1 つの VM に割り当てるには、複数の仮想 NIC を VM に割り当てます。
>


> [!WARNING]
> SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤーと SAP アプリケーションの間の通信パスに[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)を構成することはサポートされていません。 この制限は、機能およびパフォーマンス上の理由からです。 SAP アプリケーション レイヤーと DBMS レイヤーの間の通信パスは、直接的なものである必要があります。 [アプリケーション セキュリティ グループ (ASG) および NSG ルール](../../../virtual-network/network-security-groups-overview.md)で直接通信パスが許可されている場合、この制限にこれらの Azure ASG および NSG ルールは含まれません。 
>
> ネットワーク仮想アプライアンスがサポートされないその他のシナリオは次のとおりです。
>
> * 「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-suse.md)」で説明されている、Linux Pacemaker クラスター ノードを表す Azure VM と SBD デバイスの間の通信パス。
> * 「[Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-file-share.md)」に従って設定された、Azure VM と Windows Server スケールアウト ファイル サーバー (SOFS) の間の通信パス。 
>
> 通信パスにネットワーク仮想アプライアンスがあると、2 つの通信パートナー間のネットワーク待ち時間が容易に倍増する可能性があります。 また、SAP アプリケーション レイヤーと DBMS レイヤーの間のクリティカル パスのスループットが制限される場合もあります。 一部の顧客シナリオでは、ネットワーク仮想アプライアンスが原因で Pacemaker Linux クラスターに障害が発生することがあります。 Linux Pacemaker クラスター ノード間の通信において、ネットワーク仮想アプライアンス経由で SBD デバイスと通信する場合があります。
>

> [!IMPORTANT]
> サポートされて "*いない*" もう 1 つの設計は、SAP アプリケーション レイヤーと DBMS レイヤーを、相互に [ピアリング](../../../virtual-network/virtual-network-peering-overview.md)されていない異なる Azure 仮想ネットワークに分離することです。 異なる Azure 仮想ネットワークを使用するのではなく、Azure 仮想ネットワーク内のサブネットを使用して SAP アプリケーション レイヤーと DBMS レイヤーを分離することをお勧めします。 
>
> 推奨事項に従わず、代わりに 2 つのレイヤーを異なる仮想ネットワークに分離する場合は、2 つの仮想ネットワークを[ピアリング](../../../virtual-network/virtual-network-peering-overview.md)する必要があります。 
>
> 2 つの[ピアリング](../../../virtual-network/virtual-network-peering-overview.md)された Azure 仮想ネットワーク間のネットワーク トラフィックは転送コストの影響を受ける点に注意してください。 SAP アプリケーション レイヤーと DBMS レイヤーの間で、数テラバイトもの膨大な量のデータが交換されます。 SAP アプリケーション レイヤーと DBMS レイヤーが 2 つのピアリングされた Azure 仮想ネットワーク間で分離されている場合、相当なコストが蓄積される可能性があります。

Azure 可用性セット内または 2 つの Azure Availability Zones 間で、運用 DBMS デプロイに 2 つの VM を使用します。 また、SAP アプリケーション レイヤーと、2 つの DBMS VM への管理トラフィックおよび運用トラフィック用に別個のルーティングを使用します。 次の図を参照してください。

![2 つのサブネット内の 2 つの VM のダイアグラム](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer を使用してトラフィックをリダイレクトする
SQL Server Always On または HANA システム レプリケーションなどの機能でプライベート仮想 IP アドレスを使用するには、Azure Load Balancer の構成が必要です。 ロード バランサーは、プローブ ポートを使用して、アクティブな DBMS ノードを特定し、そのアクティブなデータベース ノードにのみトラフィックをルーティングします。 

データベース ノードでフェールオーバーが発生した場合、SAP アプリケーションを再構成する必要はありません。 代わりに、最も一般的な SAP アプリケーション アーキテクチャにより、プライベート仮想 IP アドレスに再接続されます。 その一方で、ロード バランサーはノードのフェールオーバーに反応して、プライベート仮想 IP アドレスへのトラフィックを 2 番目のノードにリダイレクトします。

Azure では 2 つの異なる[ロード バランサーの SKU](../../../load-balancer/load-balancer-overview.md) を提供します。Basic SKU と Standard SKU です。 セットアップと機能の利点に基づいて、Azure ロード バランサーの Standard SKU を使用する必要があります。 Standard バージョンのロード バランサーの大きな利点の 1 つは、データ トラフィックがロード バランサー自体を経由してルーティングされないことです。

内部ロード バランサーを構成する方法の例については、[チュートリアル: Azure Virtual Machines での SQL Server 可用性グループの手動構成](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)に関するページを参照してください。

> [!NOTE]
> パブリック IP アドレスのアクセスに関連する Basic SKU と Standard SKU の動作には違いがあります。 パブリック IP アドレスにアクセスするために Standard SKU の制限事項を回避する方法については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」のドキュメントを参照してください。


### <a name="azure-accelerated-networking"></a>Azure Accelerated Networking
Azure VM 間のネットワーク待ち時間をさらに短縮するには、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)を選択することをお勧めします。 これを、(SAP アプリケーション レイヤーと SAP DBMS レイヤーの場合は特に) SAP ワークロード用の Azure VM をデプロイするときに使用します。

> [!NOTE]
> すべての種類の VM で高速ネットワークがサポートされるわけではありません。 高速ネットワークをサポートする VM の種類の一覧が前の記事に記載されています。
>

---
> ![Windows 高速ネットワーク][Logo_Windows] Windows
>
> Windows 用の高速ネットワークを使用して VM をデプロイする方法については、「[高速ネットワークを使った Windows 仮想マシンの作成](../../../virtual-network/create-vm-accelerated-networking-powershell.md)」を参照してください。
>
> ![Linux 高速ネットワーク][Logo_Linux] Linux
>
> Linux ディストリビューションの詳細については、「[高速ネットワークを使った Linux 仮想マシンの作成](../../../virtual-network/create-vm-accelerated-networking-cli.md)」を参照してください。
>
>

---

> [!NOTE]
> SUSE、Red Hat、および Oracle Linux の場合、Accelerated Networking は最近のリリースでサポートされています。 SLES 12 SP2、RHEL 7.2 などの以前のリリースでは、Azure 高速ネットワークはサポートされていません。
>


## <a name="deployment-of-host-monitoring"></a>ホスト監視のデプロイ
Azure 仮想マシンの運用環境で SAP アプリケーションを使用する場合、SAP には Azure 仮想マシンを実行している物理ホストからホスト監視データを取得する機能が必要です。 SAPOSCOL および SAP Host Agent でこの機能を有効にする、特定の SAP Host Agent パッチ レベルが必要になります。 正確なパッチ レベルは SAP Note [1409604]に記載されています。

ホストのデータを SAPOSCOL および SAP Host Agent に配信するコンポーネントのデプロイと、それらのコンポーネントのライフサイクル管理の詳細については、[デプロイ ガイド][deployment-guide]を参照してください。


## <a name="next-steps"></a>次のステップ
特定の DBMS については、以下を参照してください。

- [SAP ワークロードのための SQL Server Azure Virtual Machines DBMS のデプロイ](dbms_guide_sqlserver.md)
- [SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ](dbms_guide_oracle.md)
- [SAP ワークロードのための IBM DB2 Azure Virtual Machines DBMS のデプロイ](dbms_guide_ibm.md)
- [SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](dbms_guide_sapase.md)
- [Azure での SAP MaxDB、Live Cache、Content Server のデプロイ](dbms_guide_maxdb.md)
- [SAP HANA on Azure 運用ガイド](hana-vm-operations.md)
- [Azure 仮想マシンの SAP HANA の高可用性](sap-hana-availability-overview.md)
- [Azure 仮想マシン上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)