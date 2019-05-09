---
title: Azure 仮想マシン (VM) 上で IBM Db2 HADR を設定する | Microsoft Docs
description: Azure 仮想マシン (VM) 上で IBM Db2 LUW の高可用性を実現します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565803"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性

[HADR 構成](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)の IBM Db2 LUW (Linux、UNIX、Windows) は、プライマリ データベース インスタンスを実行する 1 つのノードと、セカンダリ データベース インスタンスを実行する 1 つ以上のノードで構成されています。 プライマリ データベース インスタンスに対する変更は、実際の構成に応じて、同期的または非同期的にセカンダリ データベース インスタンスにレプリケートされます。 

この記事では、仮想マシンをデプロイして構成する方法、クラスター フレームワークをインストールする方法、さらに、HADR 構成で IBM Db2 LUW をインストールして構成する方法について説明します。 この記事では、HADR の IBM Db2 LUW をインストールして構成する方法や SAP ソフトウェアのインストールについては説明しません。 これらのタスクを遂行するために SAP と IBM のインストール マニュアルへの参照が掲載しています。 Azure 環境に固有の領域に重点が置かれています。 

SAP Note #[1928533] に記載されているように、サポートされる IBM Db2 バージョンは 10.5 以上です。

インストールに取り組む前に、まず、以下の SAP Note とドキュメントをお読みください。

| SAP Note | 説明 |
| --- | --- |
| [1928533] | SAP Applications on Azure:Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] | SAP on Microsoft Azure:Support prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [2178632] | Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [2191498] | SAP on Linux with Azure:Enhanced Monitoring (Azure を使用した Linux での仮想化: 拡張された監視機能) |
| [2243692] | Linux on Microsoft Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] |SUSE LINUX Enterprise Server 12:インストールに関する注記 |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2233094] |DB6:SAP Applications on Azure Using IBM Db2 for Linux, UNIX, and Windows - Additional Information (DB6: Linux、UNIX、および Windows 向けの IBM Db2 を使用した Azure 上の SAP アプリケーション - 追加情報) |
| [1612105] |DB6:FAQ on Db2 High Availability Disaster Recovery (HADR) (DB6: Db2 の HADR (High Availability Disaster Recovery) に関する FAQ) |


| ドキュメント | 
| --- |
| [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) には、Linux に必要な SAP Note がすべて掲載されています |
| [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関するガイド |
| [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide] (この記事) |
| [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関するガイド |
| [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server for SAP Applications 12 SP3 のベスト プラクティス ガイド][sles-for-sap-bp] |
| [SUSE Linux Enterprise High Availability Extension 12 SP3][sles-ha-guide] |
| [SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ][dbms-db2] |
| [IBM Db2 高可用性災害時リカバリー 11.1][db2-hadr-11.1] |
| [IBM Db2 高可用性災害時リカバリー R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>概要
高可用性を実現するには、HADR を搭載した IBM Db2 LUW を 2 台以上の Azure 仮想マシンにインストールします。このとき、これらの仮想マシンは、[Azure 可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)にデプロイするか、[Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) にデプロイします。 下の図は、データベース サーバーである 2 台の Azure VM の設定を示します。 どちらのデータベース サーバー Azure VM も、それぞれ独自のストレージが接続され、稼動しています。 HADR では、いずれか 1 つの Azure VM にある 1 つのデータベース インスタンスにプライマリ インスタンスの役割が与えられます。 すべてのクライアントは、このプライマリ インスタンスに接続されています。 データベース トランザクションにおけるすべての変更は、Db2 のトランザクション ログとしてローカルに永続化されます。 トランザクション ログ レコードは、ローカルに永続化されると、セカンダリ データベース サーバー (スタンバイ サーバーまたはスタンバイ インスタンス) 上のデータベース インスタンスに TCP/IP 経由で転送されます。 スタンバイ インスタンスは、転送されたトランザクション ログ レコードをロールフォワードすることによってローカル データベースを更新します。 そのためスタンバイ サーバーはプライマリ サーバーと同期された状態で維持されます。

HADR は、レプリケーション機能にすぎません。 障害の検出や自動引き継ぎ (フェールオーバー) の機能はありません。 スタンバイへの引き継ぎ (切り替え) は、データベース管理者が手動で開始する必要があります。 自動引き継ぎと障害検出を実現するには、Linux Pacemaker クラスタリング機能を使用できます。 Pacemaker は、2 つのデータベース サーバー/インスタンスを監視します。 プライマリ データベース サーバー/インスタンスがクラッシュすると、Pacemaker により、スタンバイ サーバーによる HADR の**自動**引き継ぎが開始されるほか、新しいプライマリ サーバーには確実に仮想 IP アドレスが割り当てられます。

![IBM Db2 高可用性の概要](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP アプリケーション サーバーがプライマリ データベースに接続するには、仮想ホスト名と仮想 IP アドレスが必要です。 フェールオーバーが発生した場合、SAP アプリケーション サーバーは新しいプライマリ データベース インスタンスに接続するようになります。 Azure 環境では、IBM Db2 の HADR の要件に対応した形で仮想 IP アドレスを使用するために [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) が必要です。 

次の図は、HADR を搭載した IBM Db2 LUW と Pacemaker が高可用性 SAP システムの設定にどのように組み込むかをわかりやすく紹介するために、IBM Db2 データベースを基盤とした SAP システムの高可用性の設定の概要を示したものです。 この記事では、IBM Db2 のみを取り上げており、SAP システムの他のコンポーネントの設定方法に関しては他の記事を紹介しています。

![IBM DB2 HA 環境の概要](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>必要な手順の概要
IBM Db2 構成をデプロイするには、次の手順が網羅されている必要があります。

  + 環境を計画する
  + VM をデプロイする
  + SUSE Linux を更新してファイルシステムを構成する
  + Pacemaker をインストールして構成する
  + [高可用性 NFS][nfs-ha] をインストールする
  + [別のクラスターに ASCS/ERS][ascs-ha] をインストールする 
  + 分散/高可用性オプション (SWPM) で IBM Db2 データベースをインストールする
  + セカンダリ データベース ノードとインスタンスをインストールまたは作成して HADR を構成する
  + HADR が動作していることを確認する
  + Pacemaker の構成を適用して IBM Db2 を制御する
  + Azure Load Balancer を構成する 
  + プライマリ アプリケーション サーバーとダイアログ アプリケーション サーバーをインストールする
  + SAP アプリケーション サーバーの構成を確認または調整する
  + フェールオーバーまたは引き継ぎテストを実行する



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR 搭載の IBM Db2 LUW をホストするための Azure インフラストラクチャを計画する

デプロイを実行する前に計画を立てます。 これは、HADR 搭載の Db2 の構成を Azure にデプロイするうえでの基盤を築く作業です。 IMB Db2 LUW (SAP 環境のデータベース部分) の計画には主に、次の要素を含める必要があります。

| トピック | 簡単な説明 |
| --- | --- |
| Azure のリソース グループの定義 | VM、VNet、Azure Load Balancer など各種リソースのデプロイ先となるリソース グループ。 既存のものでも、新規のものでもかまいません。 |
| 仮想ネットワークまたはサブネットの定義 | IBM Db2 の VM と Azure Load Balancer のデプロイ先。 既存のものでも、新たに作成したものでもかまいません。 |
| IBM Db2 LUW をホストしている仮想マシン | VM サイズ、ストレージ、ネットワーク、IP アドレス |
| IBM Db2 データベースの仮想ホスト名と仮想 IP| SAP アプリケーション サーバーの接続に使用される仮想 IP/ホスト名。 **db-virt-hostname**、**db-virt-ip** |
| Azure フェンス | Azure フェンスまたは SBD フェンス (強く推奨)。 スプリット ブレインの状況を回避するための方法が防止されます。 |
| SBD VM | SBD 仮想マシンのサイズ、ストレージ、ネットワーク |
| Azure Load Balancer | Basic または Standard (推奨) の使用、Db2 データベースのプローブ ポート (**probe-port**) (62500 を推奨) |
| 名前解決| その環境における名前解決のしくみ。 DNS サービスを強くお勧めします。 ローカルの hosts ファイルを使用できます。 |
    
Azure における Linux Pacemaker の使用の詳細については、次の記事を参照してください。

- [Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>SUSE Linux へのデプロイ

IBM Db2 LUW のリソース エージェントは、SUSE Linux Enterprise Server for SAP Applications に同梱されています。 このドキュメントで説明する設定では、SUSE Linux Server for SAP Applications の使用が必須です。 Azure Marketplace には、SUSE Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい Azure 仮想マシンのデプロイに使用できます。 Azure VM ギャラリーで VM イメージを選択する際は、SUSE から Azure ギャラリーを通じて提供されるさまざまなサポートまたはサービス モデルに注意してください。 

### <a name="hosts---dns-updates"></a>ホスト - DNS の更新
仮想ホスト名を含む、すべてのホスト名のリストを作成し、適切な IP アドレスからホスト名への解決ができるよう、DNS サーバーを更新します。 DNS サーバーが存在しない場合や DNS エントリを更新したり作成したりできない場合は、このシナリオに参加している各 VM のローカルの hosts ファイルを活用する必要があります。 hosts ファイルのエントリを使用する場合は、それらのエントリが SAP システム環境内のすべての VM に適用されるようにする必要があります。 ただし、推奨されるのは、Azure に理想的な形で拡張される DNS を使用することです。


### <a name="manual-deployment"></a>手動によるデプロイ

選択した OS が IBM Db2 LUW 用の IBM/SAP によってサポートされていることを確認してください。 Azure VM と Db2 リリースでサポートされている OS バージョンの一覧は、SAP Note [1928533] で確認できます。 Db2 リリースごとの OS リリースの一覧は、SAP 製品の可用性マトリックスにあります。 SLES 12 SP3 以上の使用を強くお勧めします。このバージョン以降の SUSE Linux で Azure に関連したパフォーマンスが向上しています。

1. リソース グループを作成または選択します
2. 仮想ネットワークとサブネットを作成または選択します
3. Azure 可用性セットを作成するか、可用性ゾーンにデプロイします。
    + 可用性セット - 最大更新ドメインを 2 に設定します
4. 仮想マシン 1 を作成します。
    + Azure ギャラリーにある SLES for SAP イメージを使用します。
    + 手順 3 で作成した Azure 可用性セットを選択するか、または可用性ゾーンを選択します。
5.  仮想マシン 2 を作成します。
    + Azure ギャラリーにある SLES for SAP イメージを使用します。
    + 手順 3 で作成した Azure 可用性セットを選択します。 または可用性ゾーンを選択します。これは、手順 3 と同じゾーンではありません。
6. これらの VM にデータ ディスクを追加します。記事「[SAP ワークロードのための IBM DB2 Azure Virtual Machines DBMS のデプロイ][dbms-db2]」にあるファイルシステムの設定に関する推奨事項を確認してください。

## <a name="create-the-pacemaker-cluster"></a>Pacemaker クラスターを作成する
    
「 [Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする][sles-pacemaker] 」の手順に従って、この IBM Db2 サーバー用に基本的な Pacemaker クラスターを作成します。 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>IBM Db2 LUW と SAP 環境をインストールする

IBM Db2 LUW をベースにした SAP 環境のインストールを開始する前に、以下を確認してください (この記事の冒頭にリンクが記載されています)。

+ Azure のドキュメント
+ SAP のドキュメント
+ IBM のドキュメント

NetWeaver ベースのアプリケーションを IBM Db2 LUW にインストールする方法については、SAP のインストール マニュアルを確認してください。

SAP ヘルプ ポータルに関するガイドは、[SAP インストール Guide Finder][sap-instfind] を使用して検索できます。

検索をフィルター処理することで、ガイドの数を絞り込むことができます。次のようにフィルターを設定してください。

+ [I want to]\(検索対象\): "Install a new system"
+ [My Database]\(使用データベース\): "IBM Db2 for Linux, Unix, and Windows"
+ SAP NetWeaver のバージョン、スタック構成、オペレーティング システムに関するその他のフィルター。

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR 搭載の IBM Db2 LUW を設定するためのインストールのヒント

プライマリ IBM Db2 LUW データベース インスタンスを次のように設定します。

- 高可用性または分散オプションを使用する
- SAP ASCS/ERS とデータベース インスタンスをインストールする
- 新しくインストールしたデータベースのバックアップを作成する


> [!IMPORTANT] 
> インストール中に [Database Communication port]\(データベース通信ポート\) の設定を書き留めておいてください。 これは、両方のデータベース インスタンスに対して同じポート番号である必要があります。

SAP の同種システム コピー手順を使用してスタンバイ データベース サーバーを設定する場合は、次の手順を実行します。

  - システム コピー オプションの [Target systems]\(ターゲット システム\) - [Distributed]\(分散\) - [Database instance]\(データベース インスタンス\) を使用します。
  - バックアップを使用してスタンバイ サーバーまたはインスタンス上にバックアップを復元できるよう、コピー方法として [Homogeneous System Copy]\(同種システム コピー\) を選択します。
  - 同種システム コピーのデータベースを復元する最後の手順に到達したら、インストーラーを終了します。 プライマリ ホストのバックアップからデータベースを復元します。 後続のインストール フェーズはすべて、プライマリ データベース サーバー上で実行済みです。
- IBM Db2 の HADR を設定します。

> [!NOTE]
> Azure と Pacemaker に固有のインストールまたは構成。 SAP Software Provisioning Manager を使用したインストール手順の中で、IBM Db2 LUW の高可用性に関して明らかに疑問が生じます。
>+ IBM Db2 pureScale は選択しない
>+ [Install IBM Tivoli System Automation for Multiplatforms]\(IBM Tivoli System Automation for Multiplatforms のインストール\) は選択しない
>+ [Generate cluster configration files]\(クラスター構成ファイルの生成\) は選択しない

> [!NOTE]
>Linux Pacemaker に SBD デバイスを使用している場合は、Db2 HADR のパラメーターを次のように設定してください
>+ HADR peer window duration (seconds) (HADR_PEER_WINDOW) = 300  
>+ HADR timeout value (HADR_TIMEOUT) = 60

> [!NOTE]
>Azure Pacemaker フェンス エージェントを使用する場合は:
>+ HADR peer window duration (seconds) (HADR_PEER_WINDOW) = 900  
>+ HADR timeout value (HADR_TIMEOUT) = 60

パラメーターは、最初のフェールオーバーまたは引き継ぎテストに基づいて推奨されます。 これらのパラメーター設定でテイクオーバーと引き継ぎが適切に機能するかどうかを必ずテストしてください。 個々の構成は異なる場合もあるため、これらのパラメーターには調整が必要になる可能性があります。 

> [!IMPORTANT]
> 通常起動を使用した HADR 構成の IBM Db2 に固有の情報: プライマリ データベース インスタンスを起動する前に、セカンダリまたはスタンバイ データベース インスタンスが稼動している必要があります。

デモンストレーションの目的上、このドキュメントに記載した手順では、データベースの SID を **PTR** としています。

##### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR チェック
HADR の構成後は、プライマリおよびスタンバイ ノードの状態は PEER および CONNECTED になります。

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker の構成

ノードの障害発生時に自動フェールオーバーに Pacemaker を使用する場合は、Db2 インスタンスと Pacemaker を適宜構成する必要があります。 このセクションでは、このタイプの構成について説明します。

以下の項目には、次のいずれかのプレフィックスが付いています。

- **[A]** - すべてのノードに当てはまる
- **[1]** - ノード 1 にのみ当てはまる 
- **[2]** - ノード 2 にのみ当てはまる

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]** Pacemaker の構成に関する前提条件:
1. ユーザー db2\<sid> で、db2stop を使用して両方のデータベース サーバーをシャットダウンします
2. db2\<sid> ユーザーのシェル環境を "/bin/ksh" に変更します (Yast ツールを使用する際に推奨)。 


### <a name="pacemaker-configuration"></a>Pacemaker の構成:

**[1]** IBM Db2 HADR 固有の Pacemaker の構成
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM Db2 リソースを作成する
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** IBM Db2 リソースを起動する (Pacemaker のメンテナンス モードを解除する)
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認する。 リソースがどのノードで実行されているかは重要ではありません。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodes configured
# <a name="5-resources-configured"></a>5 resources configured

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Full list of resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd):Started azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):     Started azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):    Started azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaves: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Pacemaker によってクラスター化された Db2 インスタンスの管理は、Pacemaker のツールを使用して行う必要があります。 db2 のコマンド (db2stop など) の使用は、Pacemaker によってリソースの障害として検出されます。 メンテナンスを行う場合は、ノードまたはリソースをメンテナンス モードにしてください。Pacemaker によるリソースの監視が中断され、通常の db2 管理コマンドを使用できます。


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer を構成する
[Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) の使用をお勧めします。

1. (Azure portal を使用して) Azure Load Balancer を構成します。 まず、フロントエンド IP プールを作成します。

   1. Azure portal で Azure Load Balancer を開き、**[フロントエンド IP プール]** を選択して、**[追加]** を選択します。
   2. 新規のフロントエンド IP プールの名前を入力します (例: **Db2-connection**)。
   3. **[割り当て]** を **[静的]** に設定し、最初に定義した IP アドレス **Virtual-IP** を入力します。
   4. **[OK]** を選択します。
   5. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

2. 次に、バックエンド プールを作成します。

   1. Azure portal で Azure Load Balancer を開き、**[バックエンド プール]** を選択して、**[追加]** を選択します。
   2. 新しいバックエンド プールの名前を入力します (例: **Db2-backend**)。
   3. **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。
   4. 手順 3 で作成した IBM Db2 データベースをホストする可用性セットまたは仮想マシンを選択します。
   5. IBM Db2 クラスターの仮想マシンを選択します。
   6. **[OK]** を選択します。

3. 3 つ目の手順は、正常性プローブの作成です。

   1. Azure portal で Azure Load Balancer を開き、**[正常性プローブ]** を選択して、**[追加]** を選択します。
   2. 新しい正常性プローブの名前を入力します (例: **Db2-hp**)。
   3. プロトコルとして **[TCP]** を選択し、ポート **62500** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、**[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
   4. **[OK]** を選択します。

4. 負荷分散規則を作成します。

   1. Azure portal で Azure Load Balancer を開き、**[負荷分散規則]** を選択して、**[追加]** を選択します。
   2. 新しいロード バランサー規則の名前を入力します (例: **Db2-SID**)。
   3. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **Db2-frontend**)。
   4. **[プロトコル]** の設定は **[TCP]** のままにして、ポート <*データベース通信ポート*> を入力します。
   5. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
   6. **Floating IP を有効にします**。
   7. **[OK]** を選択します。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>接続に仮想 IP を使用するよう SAP プロファイルに変更を加える
SAP アプリケーション レイヤーでは、Azure Load Balancer 用に定義、構成した仮想 IP アドレスを使用して、HADR 構成のプライマリ インスタンスに接続する必要があります。 次の変更が必要です。

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>プライマリおよびダイアログ アプリケーション サーバーをインストールする

Db2 HADR 構成に対してプライマリおよびダイアログ アプリケーション サーバーをインストールする際は、その構成用に選択した仮想ホスト名を使用する必要があります。 

Db2 HADR 構成を作成する前にインストールを行った場合は、前の段落で説明したように変更に加え、SAP Java スタックに対しては以降の変更を行う必要があります。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java または Java スタック システムの JDBC URL の確認

J2EE Config ツールを使用して JDBC URL を確認または更新します。 J2EE Config ツールはグラフィカル ツールであるため、**X サーバー**がインストールされている必要があります。
 
1. J2EE インスタンスのプライマリ アプリケーション サーバーにサインインして次を実行します。
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. 左側のフレームでセキュリティ ストアを選択します。
2. 右側のフレームで、キー jdbc/pool/<SAPSID>/url を選択します。
2. JDBC URL のホスト名を、該当する仮想ホスト名に変更します。 <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. [追加] を選択します。
5. 変更を保存するために、左上隅のディスク アイコンをクリックします。
5. 構成ツールを閉じます。
5. Java インスタンスを再起動します。

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>HADR 設定のログ アーカイブの構成
HADR 設定の Db2 ログ アーカイブを構成するには、すべてのログ アーカイブ保存先から自動的にログを取得できるようにプライマリとスタンバイの両方のデータベースを構成することをお勧めします。 プライマリ データベースとスタンバイ データベースの両方が、すべてのログ アーカイブ保存先 (データベース インスタンスの 1 つがログ ファイルをアーカイブする場所) からログ アーカイブ ファイルを取得できることが必要です。 

ログ アーカイブを実行するのは、プライマリ データベースだけです。 データベース サーバーの HADR ロールを変更した場合または障害が発生した場合は、新しいプライマリ データベースがログ アーカイブを担います。 異なるログ アーカイブ保存先を設定してある場合、ログが 2 回アーカイブされる可能性があります。そこで、ローカルまたはリモートのキャッチアップが生じた場合は、古いプライマリ サーバーのアーカイブ済みログを、新しいプライマリ サーバーのアクティブなログ保存先に手動でコピーしなければならないことがあります。

両方のノードからログが書き込まれる共通の NFS 共有を構成することをお勧めします。 NFS には高可用性が確保されている必要があります。 

transports や profile ディレクトリに使用されていた既存の高可用性 NFS を使用することができます。 Read:

- [SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性][nfs-ha] 
- [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) を使用して NFS 共有を作成する方法に関する「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」


## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

このセクションでは、Db2 の HADR 設定をテストする方法について説明します。 **すべてのテストでは、root ユーザーとしてログインしていること**、また IBM Db2 プライマリが **azibmdb01** 仮想マシンで稼働していることを前提としています。

すべてのテスト ケースの初期状態は次のとおりです (crm_mon -r または crm status)。

- **crm status**: 実行時におけるスナップショットの Pacemaker 状態 
- **crm_mon -r**: Pacemaker 状態の連続出力

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP システムにおける最初の状態は、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) --> [Configuration]\(構成\) --> [Overview]\(概要\) に次のように記録されています。

![DBACockpit - 移行前](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2 のテストの引き継ぎ


> [!IMPORTANT] 
> テストを開始する前に、Pacemaker には失敗した操作 (crm status) が存在しないこと、場所の制約 (移行テストの形跡) が存在しないこと、IBM Db2 HADR 同期が動作していることを確認してください。 ユーザー db2\<sid> で確認します。 <pre><code>db2pd -hadr -db \<DBSID></code></pre>


次のコマンドを実行して、プライマリ Db2 データベースが稼働しているノードを移行します。
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

移行が完了すると、crm status の出力は次のようになります。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP システムにおける最初の状態は、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) --> [Configuration]\(構成\) --> [Overview]\(概要\) に次のように記録されています。![DBACockpit - 移行後](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

"crm resource migrate" を使用したリソースの移行により、場所の制約が作成されます。 場所の制約は削除する必要があります。 場所の制約を削除しないと、リソースをフェールバックできない場合や不要な引き継ぎが行われない場合があります。 

リソースを再度 **azibmdb01** に移行し、場所の制約をクリアします。
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- crm resource migrate <res_name> <host> - 場所の制約を作成します。引き継ぎで問題が生じる場合があります。
- crm resource clear <res_name> - 場所の制約をクリアします。
- crm resource cleanup <res_name> - リソースのエラーをすべてクリアします。


### <a name="test-the-fencing-agent"></a>フェンス エージェントをテストする

この場合は、SUSE Linux での用途に推奨される SBD フェンスをテストします。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

クラスター ノード **azibmdb01** を再起動する必要があります。 IBM Db2 のプライマリ HADR ロールが **azibmdb02** に移動します。 **azibmdb01** がオンラインに戻ると、Db2 インスタンスがセカンダリ データベース インスタンスのロールに移動します。 

再起動された以前のプライマリで Pacemaker サービスが自動的に起動しない場合は、次のように手動で起動してください。

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>手動での引き継ぎをテストする

手動での引き継ぎをテストするには、**azibmdb01** ノードで Pacemaker サービスを停止します。
<pre><code>service pacemaker stop</code></pre>

**azibmdb02** の状態
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

フェールオーバー後、再び **azibmdb01** 上でサービスを開始できます。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>HADR プライマリ データベースが稼動しているノード上で Db2 プロセスを中止する

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 インスタンスで障害が発生し、Pacemaker から次の状態がレポートされます。

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms


</code></pre>

Pacemaker は、Db2 プライマリ データベース インスタンスを同じノードで再起動します。そうでなければ、セカンダリ データベース インスタンスが稼働しているノードにフェールオーバーして、エラーがレポートされます。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>セカンダリ データベース インスタンスが稼働しているノード上の Db2 プロセスを中止する

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

このノードは、失敗した状態になり、エラーがレポートされます。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 インスタンスが、以前割り当てられていたセカンダリ ロールで再起動されます。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>HADR プライマリ データベース インスタンスが稼動しているノード上の DB を db2stop force を使用して停止する

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

ユーザー db2\<sid> として、db2stop force コマンドを実行します。
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

障害が検出されました
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR セカンダリ データベース インスタンスがプライマリ ロールに昇格されました。
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>HADR プライマリ データベース インスタンスが稼動しているノード上の VM を再起動でクラッシュさせる

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker はセカンダリ インスタンスをプライマリ インスタンス ロールに昇格させます。 古いプライマリ インスタンスは、VM の再起動後にその VM とすべてのサービスが完全に復元された後、セカンダリ ロールに移行されます。

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>HADR プライマリ データベース インスタンスが稼動している VM を "停止" してクラッシュさせる

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

この場合、Pacemaker は、プライマリ データベース インスタンスが稼働しているノードが応答していないことを検出します。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

次に、**スプリット ブレイン**が発生しているかを確認します。 プライマリ データベース インスタンスを最後に実行したノードがダウンしていることを最後まで残ったノードが確認すると、リソースのフェールオーバーが実行されます。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


ノードを "停止" した場合、障害が発生したノードを Azure 管理ツール (ポータル、PowerShel、AzureCLI など) を使用して再起動する必要があります。障害が発生したノードは、オンラインに戻ると、Db2 インスタンスをセカンダリ ロールとして起動します。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>次の手順
以下のドキュメントを参照してください。

- [SAP NetWeaver のための高可用性のアーキテクチャとシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Azure の SUSE Linux Enter
- pri
- se Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

