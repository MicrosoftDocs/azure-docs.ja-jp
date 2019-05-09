---
title: SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項 | Microsoft Docs
description: SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54511ac4dfdc05ec1880695b1ae2360f0b5e8162
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328369"
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


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

このガイドは Microsoft Azure への SAP ソフトウェアの実装およびデプロイに関するドキュメントの一部です。 このガイドを読む前に、「[計画および実装ガイド][planning-guide]」をお読みください。 このドキュメントでは、サービスとしての Azure インフラストラクチャ (IaaS) 機能を使用した Microsoft Azure Virtual Machines (VM) 上での SAP 関連の DBMS システムの一般的なデプロイの側面について説明します。

特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Note を補足する内容となっています。

このドキュメントでは、Azure VM で SAP 関連 DBMS システムを実行する際の考慮事項について説明します。 この章では、特定の DBMS システムについてはほとんど言及していません。 特定の DBMS システムについては、このドキュメントの後で説明します。

## <a name="definitions-upfront"></a>用語の定義
このドキュメントでは、次の用語を使用します。

* IaaS:サービスとしてのインフラストラクチャ。
* PaaS:サービスとしてのプラットフォーム。
* SaaS:サービスとしてのソフトウェア。
* SAP コンポーネント:個々の SAP アプリケーション (ECC、BW、Solution Manager、EP など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
* SAP 環境: 開発、QAS、トレーニング、DR、実稼働などのビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
* SAP ランドスケープ:この用語は、お客様の IT 環境内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
* SAP システム:DBMS 層とアプリケーション レイヤーを組み合わせたシステム (SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなど)。Azure のデプロイでは、オンプレミスと Azure 間でこれら 2 つの層を分割することはできません。 その結果、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 ただし、SAP ランドスケープの異なるシステムを Azure またはオンプレミスにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。
* クロスプレミス:オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または ExpressRoute 接続を使用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 この種のデプロイメントは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、サブスクリプションの Azure 資産に拡張されます。 この拡張により。VM はオンプレミス ドメインの一部になることができます。 オンプレミス ドメインのドメイン ユーザーはこのサーバーにアクセスできます。また、その VM のサービス (DBMS サービスなど) を実行できます。 オンプレミスにデプロイした VM 間、および Azure にデプロイした VM 間の通信および名前解決は可能です。 これは、Azure 上に SAP 資産をデプロイする場合の最も一般的なシナリオです。 詳細については、「[VPN ゲートウェイの計画と設計](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)」を参照してください。

> [!NOTE]
> SAP システムを実行する Azure Virtual Machines がオンプレミス ドメインのメンバーである SAP システムのクロスプレミス デプロイは、運用 SAP システムの場合にサポートされます。 クロスプレミス構成では、Azure に SAP ランドスケープの一部または全部をデプロイする場合にサポートされます。 Azure で完全な SAP ランドスケープを実行する場合でも、これらの VM をオンプレミス ドメインと AD/LDAP の一部に含める必要があります。 ハイブリッド IT シナリオについて記載した以前のバージョンのドキュメントでは、*ハイブリッド*という用語はオンプレミスと Azure 間のクロスプレミス接続が存在するという事実に基づいていました。 このケースでは、*ハイブリッド*とは Azure の VM がオンプレミス Active Directory の一部であることも意味します。
>
>

一部の Microsoft ドキュメントでは、クロスプレミス シナリオ、特に DBMS HA 構成の場合に少し異なる記述をしています。 SAP 関連ドキュメントでは、クロスプレミス シナリオはサイト間接続またはプライベート [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 接続を使用することを表し、SAP ランドスケープがオンプレミスと Azure 間で分散されるという事実を表します。

## <a name="resources"></a>リソース
Azure 上の SAP ワークロードに関するさまざまな記事が公開されています。 [Azure での SAP ワークロード作業の開始](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)に関する記事の関心のある分野を選択することをお勧めします。

次の SAP Note は、このドキュメントで扱う領域に関する SAP on Azure に関連します。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure:Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] |SAP on Microsoft Azure:Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1409604] |Virtualization on Windows:Enhanced Monitoring (Windows での仮想化: 拡張された監視機能) |
| [2191498] |SAP on Linux with Azure:Enhanced Monitoring (Azure を使用した Linux での仮想化: 拡張された監視機能) |
| [2039619] |SAP Applications on Microsoft Azure using the Oracle Database:Supported Products and Versions (Oracle データベースを使用した Microsoft Azure 上の SAP アプリケーション: サポートされている製品とバージョン) |
| [2233094] |DB6:SAP Applications on Azure Using IBM DB2 for Linux, UNIX, and Windows - Additional Information (DB6: Linux、UNIX、および Windows 向けの IBM DB2 を使用した Azure 上の SAP アプリケーション - 追加情報) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] |SUSE LINUX Enterprise Server 12:インストールに関する注記 |
| [2002167] |Red Hat Enterprise Linux 7.x:Installation and Upgrade (Red Hat Enterprise Linux 7.x: インストールとアップグレード) |
| [2069760] |Oracle Linux 7.x SAP のインストールとアップグレード |
| [1597355] |Linux のスワップ領域に関する推奨事項 |
| [2171857] |Oracle Database 12c - Linux でサポートされているファイル システム |
| [1114181] |Oracle Database 11g - Linux でサポートされているファイル システム |


Linux に関するすべての SAP ノートが記載された、[SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) もご覧ください。

Microsoft Azure のアーキテクチャと Microsoft Azure Virtual Machines のデプロイおよび操作方法に関する実用的な知識が必要です。 詳細については、[Azure に関するドキュメント](https://docs.microsoft.com/azure/)を参照してください。

IaaS の説明では、一般的に、Windows、Linux、および DBMS のインストールと構成は、オンプレミスに設置する仮想マシンまたはベア メタル マシンと本質的に同じです。 ただし、Azure IaaS を利用する場合、アーキテクチャおよびシステム管理の実装の決定については異なる部分があります。 このドキュメントの目的は、Azure IaaS を使用する場合に知っておくべき特定のアーキテクチャおよびシステム管理の相違について説明することです。


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS デプロイの VM のストレージの構造
以降の章に進むにあたって、「[デプロイ ガイド][deployment-guide]」の[この章][deployment-guide-3]で説明した内容を理解しておく必要があります。 この章を読む前に、別の VM シリーズとその相違点、Standard ストレージと Premium ストレージの違いについて理解しておく必要があります。 次に

Azure VM 用の Azure Storage については、以下の記事の内容を理解している必要があります。

- [Azure Windows VM のマネージド ディスクの概要](../../windows/managed-disks-overview.md)
- [Azure Linux VM のマネージド ディスクの概要](../../linux/managed-disks-overview.md)

基本的な構成では、通常、オペレーティング システム、DBMS、および最終的に導入する SAP バイナリとデータベース ファイルとを分けたデプロイ構造にすることをお勧めしています。 このため、Microsoft は、Azure Virtual Machines で実行する SAP システムに、オペレーティング システムとともにインストールされるベース VHD (またはディスク)、データベース管理システムの実行可能ファイル、および SAP の実行可能ファイルを含めることを推奨しています。 DBMS のデータとログ ファイルは別のディスク ファイルの Azure Storage (Standard または Premium Storage) に格納され、元の Azure オペレーティング システム イメージ VM に論理ディスクとして接続されます。 特に Linux のデプロイでは、記載されている推奨事項以外の推奨事項があります。 特に SAP HANA については注意してください。

ディスクのレイアウトを計画するときは、次の項目間で最適なバランスを取る必要があります。

* データ ファイルの数。
* ファイルが含まれているディスクの数。
* 1 つのディスクの IOPS クォータ。
* ディスクごとのデータ スループット。
* VM サイズに対して追加できるデータ ディスクの数。
* VM が提供できるストレージの全体的なスループット。
* さまざまな種類の Azure Storage で考えられる待機時間。
* VM の SLA

Azure はデータ ディスクごとに IOPS クォータを適用します。 Azure Standard Storage と Premium Storage でホストされているディスクの場合、これらのクォータは異なります。 また、I/O 待機時間は、異なる種類の 2 つのストレージの間で異なります。 Premium Storage では、I/O 待機時間が短縮されます。 各 VM タイプでは接続できるデータ ディスクの数に限りがあります。 別の制限として、Azure Premium Storage を利用できるのは特定の VM タイプのみです。 この結果、特定の VM タイプを決定する場合、CPU とメモリの要件だけではなく、一般的にディスクの数または Premium Storage ディスクの種類によって拡大縮小される IOPS、待機時間、ディスクのスループット要件によっても左右される場合があります。 特に Premium Storage では各ディスクで実現する必要がある IOPS とスループットの値によってディスクのサイズも決まる場合があります。

> [!NOTE]
> DBMS デプロイメントの場合は、データ、トランザクション ログ、または再実行ファイルの種類にかかわらず、Premium Storage を使用することを強くお勧めします。 このため、展開するシステムが運用環境システムか非運用システムかは重要ではありません。

> [!NOTE]
> Azure 独自の[単一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) のメリットを十分活用するためには、ベース VHD を含め、アタッチされているすべてのディスクが Azure Premium Storage タイプである必要があります。

> [!NOTE]
> SAP データベースが配置されているストレージ ハードウェアが、Azure データセンターに隣接して併置されているサード パーティ データセンター内にある場合は、その SAP データベースのメイン データベース ファイル (データおよびログ ファイル) をホストすることがサポートされません。 SAP ワークロードの場合、SAP データベースのデータおよびトランザクション ログ ファイル用にサポートされるストレージは、ネイティブ Azure サービスとして表現されるストレージに限られます。

データベース ファイルやログ/再実行ファイルの配置および使用する Azure Storage の種類は、IOPS、待機時間、およびスループットの要件に従って定義する必要があります。 十分な IOPS を確保するためには、複数のディスクを使用するか、大容量の Premium Storage ディスクを使用する必要がある場合があります。 複数のディスクを使用する場合、データ ファイルまたはログ/再実行ファイルが含まれたディスク全体にソフトウェア ストライプを構築します。 この場合、基になる Premium Storage ディスクの IOPS およびディスク スループット SLA、または Azure Standard Storage ディスクの達成可能な最大 IOPS が累積された結果としてストライプ セットが構築されます。

既に述べたように、IOPS の要件が 1 つの VHD で提供可能な容量を超えている場合は、複数の VHD にわたるデータベース ファイルで必要な IOPS の数のバランスを取る必要があります。 複数のディスクにかかる IOPS 負荷を分散する最も簡単な方法は、異なるディスク間でソフトウェア ストライプを構築することです。 そして、ソフトウェア ストライプから分割した LUNS に SAP DBMS のデータ ファイルの数を設定します。 ストライプ内のディスクの数は、IOP の需要、ディスク スループットの需要、およびボリュームの需要によって決まります。


- - -
> ![Windows][Logo_Windows] Windows
>
> Windows 記憶域スペースを使用して複数の Azure VHD にわたってこのようなストライプ セットを作成することをお勧めします。 Windows Server 2012 R2 または Windows Server 2016 以上を使用することを推奨します。
>
> ![Linux][Logo_Linux] Linux
>
> Linux でのソフトウェア RAID の構築がサポートされているのは、MDADM および LVM (論理ボリューム マネージャー) のみです。 詳細については、次の記事を参照してください。
>
> - MDADM を使用して[ Linux 上にソフトウェア RAID を構成する](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - LVM を使用して[ Azure 内の Linux VM 上に LVM を構成する](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

- - -

> [!NOTE]
> Azure Storage には 3 つの VHD イメージが保存されているため、ストライピングをしながら冗長性を構成する意味はありません。 ストライプを構成するだけで、I/O は複数の VHD にわたって分散されます。
>

### <a name="managed-or-non-managed-disks"></a>マネージド ディスクまたは非マネージド ディスクを使用する場合
Azure ストレージ アカウントは、管理の構成要素であるだけでなく、制限事項の対象です。 Azure Standard Storage アカウントと Azure Premium Storage アカウントでは、制限事項が異なります。 機能と制限事項の詳細は、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)」記事に記載されています。

Azure Standard Storage の場合、ストレージ アカウントあたりの IOPS の制限がありますので注意してください (「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)」記事内の「**合計要求レート**」と記載されている行)。 また、最初は Azure サブスクリプションあたりのストレージ アカウントが 100 に制限されています。 このため、複数のストレージ アカウントにわたる大規模な SAP ランドスケープの場合、これらのストレージ アカウントの制限に達しないよう、VHD を分散する必要があります。 1000 を超える VHD が搭載された数百台の仮想マシンでこの作業を行うのは困難な作業です。

DBMS デプロイに Azure Standard Storage と SAP ワークロードを併用することは推奨されないため、Azure Standard Storage に関する参照事項と推奨事項は、この短い[記事](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)に記載されている事項に限定されます。

複数の Azure Storage アカウントにわたって VHD を計画しデプロイする管理作業を回避するため、Microsoft は、2017 年に[マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)と呼ばれる製品を発表しました。 マネージド ディスクは Azure Standard Storage と Azure Premium Storage で使用できます。 マネージド ディスク以外のディスクと比較したマネージド ディスクの主なメリットの一覧:

- マネージド ディスクでは、Azure はデプロイ時に自動的に複数のストレージ アカウントにわたって異なる VHD を分散するため、データ量、I/O スループットと IOPS の観点から、Azure Storage アカウントの上限を超えることがありません。
- Azure Storage はマネージド ディスクを使用することにより、Azure 可用性セットの概念に従い、VM が 可用性セットの一部である場合は、ベース VHD と VM にアタッチされたディスクを異なるフォールトおよび更新ドメインにデプロイします。


> [!IMPORTANT]
> Azure マネージド ディスクの利点を考えると、DBMS デプロイメントと SAP のデプロイや SAP デプロイに Azure マネージド ディスクを使用することを強くお勧めします。
>

非管理対象ディスクからマネージド ディスクに変換するには、以下の記事を参照してください。

- [Windows 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Linux 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM とデータ ディスクのキャッシュ
ディスクを VM にマウントする場合、Azure Storage に配置されたこれらのディスクと VM 間の I/O トラフィックをキャッシュするかどうかを選択できます。 Azure Standard および Premium Storage は、この種類のキャッシュに 2 つのテクノロジを使用します。

以下に示すキャッシュの推奨事項は、次のような Standard DBMS の I/O 特性を前提にしています。

- これは、ほとんどの場合、データベースのデータ ファイルに対するワークロードの読み込みです。 これらの読み取りは、DBMS システムのパフォーマンスにとって重要です。
- データ ファイルに対する書き込みは、チェックポイントと一定のストリームを使用してバーストで実行されます。 それにもかかわらず、1 日の平均書き込み量は、読み取り量よりも少なくなります。 データ ファイルからの読み取りとは反対に、これらの書き込みは非同期で実行され、ユーザー トランザクションに悪影響を与えません。
- トランザクション ログまたは再実行ファイルからの読み取りはほとんどありません。 例外は、トランザクション ログ バックアップを実行するときのサイズの大きな I/O です。
- トランザクションまたは再実行ログ ファイルの主な負荷は、書き込みです。 ワークロードの性質に応じて、I/O サイズを 4 KB まで小さくしたり、1 MB 以上の I/O サイズにできます。
- すべての書き込みを信頼性の高い方法でディスク上に保存する必要があります。

Azure Standard Storage で可能なキャッシュの種類は次のとおりです。

* なし
* 読み取り
* 読み取り/書き込み

一定の決まったパフォーマンスを得られるように、**DBMS 関連データ ファイル、ログ/再実行ファイル、テーブル スペースを含むすべてのディスクに対する Azure Standard Storage のキャッシュを "NONE" に設定する**必要があります。 ベース VHD のキャッシュは既定のままにしておくことができます。

Azure Premium Storage については、次のキャッシュ オプションが存在します。

* なし
* 読み取り
* 読み取り/書き込み
* なし + 書き込みアクセラレータ (Azure M シリーズ VM の場合のみ)
* 読み取り + 書き込みアクセラレータ (Azure M シリーズ VM の場合のみ)

Azure Premium Storage の推奨事項は、SAP データベースの**データ ファイルには [読み取りキャッシュ]** を利用し、**ログ ファイルのディスには [キャッシュなし]** を選択することです。

M シリーズの展開では、DBMS のデプロイに Azure 書き込みアクセラレータを使用することを強くお勧めします。 Azure 書き込みアクセラレータのデプロイの詳細および制限事項については、「[Azure 書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)」ドキュメントを参照してください。


### <a name="azure-non-persistent-disks"></a>Azure の非永続的ディスク
Azure VM は、VM をデプロイした後に非永続ディスクを提供します。 VM が再起動された場合、非永続ドライブ上のすべてのコンテンツは消去されます。そのため、データ ファイルとデータベースのログ/再実行ファイルが、どんな状況でも、非永続ドライブ上にないことが条件になります。 例外として、データベースによっては、tempdb や temp tablespaces などにこれらの非永続ドライブが適している場合があります。 ただし、非永続ドライブのスループットは、その VM ファミリーで制限されているために、A シリーズ VM にはこれらのドライブを使用しないでください。 詳しくは、「[Understanding the temporary drive on Windows VMs in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)」(Azure での Windows VM 上の一時ドライブ) をご覧ください

- - -
> ![Windows][Logo_Windows] Windows
>
> Azure VM の D:\ ドライブは、Azure の計算ノード上のいくつかのローカル ディスクによってサポートされる非永続ドライブです。 非永続であるということは、VM が再起動されると、D:\ ドライブ上のコンテンツに加えられた変更が失われることを意味します。 "変更" とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどを指します。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM は、Azure の計算ノード上のローカル ディスクによってサポートされる非永続ドライブである /mnt/resource ドライブを自動的にマウントします。 非永続であるということは、VM が再起動されると、/mnt/resource ドライブ上のコンテンツに加えられた変更が失われることを意味します。 変更とは、保存されたファイル、作成されたディレクトリ、インストールされたアプリケーションなどを指します。
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage の回復性
Microsoft Azure Storage は、ベース VHD (OS を含む) とアタッチされたディスクまたは BLOB を 3 つ以上の個別のストレージ ノードに格納します。 これは、ローカル冗長ストレージ (LRS) と呼ばれます。 LRS は、Azure 内のすべての種類のストレージの既定値です。

この他にいつくかの冗長方法があり、それらすべてが「[Azure Storage のデータ レプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」記事で説明されています。

> [!NOTE]
>DBMS VM とデータベースおよび、ログ/再実行ファイルを保存するディスクのストレージとして推奨される種類である Azure Premium Storage で、使用可能な方法は LRS のみです。 このため、他の Azure Region または他の Azure Availability Zone へのデータベース データ レプリケーションを可能にするには、SQL Server Always On、Oracle Data Guard または HANA システム レプリケーションなどのデータベース方法を構成する必要があります。


> [!NOTE]
> DBMS デプロイの場合、Azure Standard Storage で使用可能な Geo Redundant Storage は、パフォーマンスへの影響が大きいことと、VM にアタッチされた複数の VHD にわたる書き込み順序に従わないためお勧めしません。 複数の VHD にわたる書き込み順序に従わないため、ソース VM 側で複数の VHD にわたってデータベースやログ/再実行ファイルが分散している場合 (ほどんどの場合に該当)、レプリケーション ターゲット側のデータベースの整合性が取れない可能性が高くなります。



## <a name="vm-node-resiliency"></a>VM ノードの回復性
Azure Platform では、VM に複数の異なる SLA を提供します。 詳細については、最新リリースの [Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) を参照してください。 DBMS 層は、SAP システムで重要となる可用性部分であることが通常であるため、可用性セット、可用性ゾーン、およびメンテナンス イベントの概念を理解しておく必要があります。 これらの概念すべてを説明している記事は「[Azure での Windows 仮想マシンの可用性の管理](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)」と「[Linux 仮想マシンの可用性管理](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)」です。

SAP ワークロードを使用する運用環境 DBMS シナリオで推奨される最小要件は、以下のとおりです。

- 同じ Azure リージョンにある個別の可用性セット内の 2 つの VM にデプロイすることです。
- これら 2 つの VM は、同じ Azure VNet で実行され、同じサブネットからの NIC にアタッチされます。
- 2 つ目の VM のホット スタンバイを維持するためにデータベース方法を使用します。 方法としては、SQL Server Alwayson、Oracle Data Guard、または HANA システム レプリケーションを使用することができます。

さらに、3 番目の別の Azure リージョンで VM を展開して、別の Azure リージョンに非同期レプリカを指定するデータベースと同じ方法を使用することができます。

この[チュートリアル](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)では、Azure 可用性セットを設定する方法について説明します。



## <a name="azure-network-considerations"></a>Azure ネットワークに関する考慮事項
大規模な SAP のデプロイでは、VNet の構成に [Azure 仮想データ センター](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)のブルー プリントを使用し、組織のさまざまな部分にアクセス許可とロール割り当てを使用することをお勧めします。

これには、何百もの顧客へのデプロイ事例から生まれた、ベスト プラクティスがあります。

- SAP アプリケーションがデプロイされる VNet からインターネットにアクセスしない。
- データベース VM は、アプリケーション レイヤーと同じ VNet で実行されている。
- VNet 内の VM 内では、プライベート IP アドレスは静的に割り当てられている。 「[Azure における IP アドレスの種類と割り当て方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)」記事を参照してください。
- DBMS VM を出入りする際のルーティングの制限は、ローカルの DBMS VM にインストールされているファイアウォールでは設定**されません**。 代わりに、トラフィック ルーティングは [Azure ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) を使用して定義されます。
- DBMS VM へのトラフィックを分離し隔離するために、異なる NIC を VM に割り当てます。 ここでは、すべての NIC に異なる IP アドレスが割り当てられ、すべての NIC は、ここでも NSG ルールが異なる VNet サブネットに割り当てられます。 ネットワーク トラフィックの隔離または分離は単にルーティングの方法であって、ネットワーク スループットのクォータの設定はできないことに注意してください。

> [!NOTE]
> Azure を使用して個々の vNIC に静的 IP アドレスを割り当てる必要があります。 ゲスト OS 内の静的 IP アドレスを vNIC に割り当てないでください。 Azure Backup Service のような一部の Azure サービスは、少なくともプライマリ vNIC が、静的 IP アドレスではなく、DHCP に設定されていることに依存します。 「[Azure 仮想マシンのバックアップのトラブルシューティング](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)」というドキュメントも参照してください。 複数の静的 IP アドレスを VM に割り当てる必要がある場合は、複数の vNIC を VM に割り当てる必要があります。
>


> [!IMPORTANT]
> 機能やパフォーマンス上の理由から、SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤー間の通信パスで [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) を構成することはサポートされていません。 SAP アプリケーション レイヤーと DBMS レイヤー間の通信は直接的な通信である必要があります。 Azure ASG および NSG の規則で直接通信が許可されている限り、この制限に[これらの ASG および NSG の規則](https://docs.microsoft.com/azure/virtual-network/security-overview)は含まれません。 NVA がサポートされないその他のシナリオが、Linux Pacemaker クラスター ノードを表す Azure VM と SBD デバイス間の通信パス内にあります (「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」を参照)。 また、Azure VM と Windows Server SOFS セットアップ間の通信パス内にもあります (「[Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)」を参照)。 通信パスに NVA があると、2 つの通信パートナー間のネットワーク待ち時間が倍増し、SAP アプリケーション レイヤーと DBMS レイヤー間のクリティカル パスのスループットが制限される場合があります。 顧客に見られる一部のシナリオでは、Linux Pacemaker クラスター ノード間の通信で NVA を通じて SBD デバイスと通信する必要がある場合、NVA が原因で Pacemaker Linux クラスターが失敗することがあります。
>

> [!IMPORTANT]
> サポートされて**いない**もう 1 つの設計は、SAP アプリケーション レイヤーと DBMS レイヤーを、相互に[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)されていない異なる Azure 仮想ネットワークに分離することです。 異なる Azure 仮想ネットワークを使用するのではなく、Azure 仮想ネットワーク内のサブネットを使用して、SAP アプリケーション レイヤーと DBMS レイヤーを分離することをお勧めします。 推奨事項に従わず、代わりに 2 つのレイヤーを異なる仮想ネットワークに分離する場合は、2 つの仮想ネットワークを[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)する必要があります。 2 つの[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)された Azure 仮想ネットワーク間のネットワーク トラフィックは転送コストの影響を受ける点に注意してください。 SAP アプリケーション レイヤーと DBMS レイヤー間で交換されるデータが膨大でテラバイト規模であり、SAP アプリケーション レイヤーと DBMS レイヤーが 2 つのピアリングされた Azure 仮想ネットワーク間で分離されている場合、相当のコストが蓄積される可能性があります。

Azure 可用性セット内の 2 つの VM に加え、SAP アプリケーション レイヤーに異なるルーティングと、2 つの DBMS VM への管理および運用トラフィックを使用する、大まかなダイアグラムは以下のようになります。

![2 つのサブネット内の 2 つの VM のダイアグラム](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>トラフィックをリダイレクトするための Azure ロード バランサー
SQL Server Always On または HANA システム レプリケーションなどの機能でプライベート仮想 IP アドレスを使用するには、Azure Load Balancer の構成が必要です。 Azure Load Balancer では、プローブ ポートを経由してアクティブな DBMS ノードを特定し、そのアクティブなデータベース ノードにのみトラフィックを転送します。 データベース ノードでフェールオーバーが発生した場合は、SAP アプリケーションを再構成する必要はありません。 最も一般的な SAP アプリケーション アーキテクチャを使用して、プライベート仮想 IP アドレスに再接続されます。 その一方で、Azure load balancer はノードのフェールオーバーに反応してプライベート仮想 IP アドレスへのトラフィックを 2 番目のノードにリダイレクトします。

Azure では 2 つの異なる[ロード バランサーの SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) を提供します。 Basic SKU と Standard SKU です。 Azure Availability Zones 全体にデプロイする場合を除き、Basic のロード バランサー SKU を問題なく使用できます。

DBMS VM と SAP アプリケーション レイヤー間のトラフィックは、常に Azure Load Balancer を介してルーティングされますか。 答えは、ロード バランサーの構成方法によって異なります。 現時点では、DBMS VM への着信トラフィックは、常に Azure Load Balancer を介してルーティングされます。 DBMS VM からアプリケーション レイヤー VM への送信トラフィック ルートは、Azure Load Balancer の構成によって異なります。 ロード バランサーでは、DirectServerReturn のオプションが提供されます。 このオプションを設定した場合、DBMS VM から SAP アプリケーション レイヤーに転送されたトラフィックは、Azure Load Balancer を介してルーティングされま**せん**。 アプリケーション レイヤーに直接転送されます。 DirectServerReturn が設定されていない場合、SAP アプリケーション レイヤーへの返送トラフィックは、Azure Load Balancer を介してルーティングされます。

SAP アプリケーション レイヤーと DBMS 層の間にこの 2 つの層の間のネットワーク待機時間を短縮するために Azure Load Balancer を配置し、DirectServerReturn を設定することをお勧めします。

このような設定の例は、[この記事](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)の SQL server Always On に関する説明で公開されています。

Azure 内の SAP インフラストラクチャ デプロイの参照として公開済みの GitHub の JSON テンプレートを使用することを選択した場合、この [SAP 3 層 システム用](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)テンプレートを調査する必要があります。 このテンプレートでも、Azure Load Balancer の正しい設定を調べることができます。

### <a name="azure-accelerated-networking"></a>Azure Accelerated Networking
Azure VM 間のネットワーク待機時間をさらに短縮するには、SAP ワークロード用の Azure VM をデプロイするときに [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) オプションを選択することを強くお勧めします。 SAP アプリケーション レイヤーと SAP DBMS 層の場合は特に推奨されます。

> [!NOTE]
> VM の種類によっては、Accelerated Networking をサポートしていない場合もあります。 Accelerated Networking をサポートする VM の種類の一覧が参照記事に記載されています。
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Windows については、[高速ネットワークでの Windows 仮想マシンの作成](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)に関する記事を参照して、Accelerated Networking の概念と、Accelerated Networking を使用して VM をデプロイする方法について理解してください。
>
> ![Linux][Logo_Linux] Linux
>
> Linux については、Linux ディストリビューションの詳細が記載されている、[Accelerated Networking を使用した Linux 仮想マシンの作成](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)に関する記事を参照してください。
>
>

- - -

> [!NOTE]
> SUSE、Red Hat、および Oracle Linux の場合、Accelerated Networking は最近のリリースでサポートされています。 SLES 12 SP2、RHEL 7.2 などの以前のリリースでは、Accelerated Networking はサポートされていません。
>


## <a name="deployment-of-host-monitoring"></a>ホスト監視のデプロイ
Azure Virtual Machines の運用環境で SAP アプリケーションを使用する場合、SAP には Azure Virtual Machines を実行している物理ホストからホスト監視データを取得する機能が必要です。 SAPOSCOL および SAP Host Agent でこの機能を有効にする、特定の SAP Host Agent パッチ レベルが必要になります。 正確なパッチ レベルは SAP Note [1409604]に記載されています。

ホストのデータを SAPOSCOL および SAP Host Agent に配信するコンポーネントのデプロイと、それらのコンポーネントのライフサイクル管理の詳細については、「[デプロイ ガイド][deployment-guide]」を参照してください。


## <a name="next-steps"></a>次の手順
特定の DBMS については、以下の記事を参照してください。

- [SAP ワークロードのための SQL Server Azure Virtual Machines DBMS のデプロイ](dbms_guide_sqlserver.md)
- [SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ](dbms_guide_oracle.md)
- [SAP ワークロードのための IBM DB2 Azure Virtual Machines DBMS のデプロイ](dbms_guide_ibm.md)
- [SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](dbms_guide_sapase.md)
- [Azure での SAP MaxDB、Live Cache、Content Server のデプロイ](dbms_guide_maxdb.md)
- [SAP HANA on Azure 運用ガイド](hana-vm-operations.md)
- [Azure 仮想マシンの SAP HANA の高可用性](sap-hana-availability-overview.md)
- [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)

