---
title: Azure 仮想マシン (VM) 上で IBM Db2 HADR を設定する | Microsoft Docs
description: Azure 仮想マシン (VM) 上で IBM Db2 LUW の高可用性を実現します。
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: faafce32c3452a5c4ff08783ec2edd28f7f961e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671885"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性

[高可用性とディザスター リカバリー (HADR) 構成](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)の IBM Db2 for Linux, UNIX, and Windows (LUW) は、プライマリ データベース インスタンスを実行する 1 つのノードと、セカンダリ データベース インスタンスを実行する 1 つ以上のノードで構成されています。 プライマリ データベース インスタンスに対する変更は、実際の構成に応じて、同期的または非同期的にセカンダリ データベース インスタンスにレプリケートされます。 

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*マスター*" と "*スレーブ*" という用語への言及があります。 ソフトウェアからこれらの用語が削除された時点で、この記事から削除します。
   
この記事では、Azure 仮想マシン (VM) をデプロイして構成し、クラスター フレームワークをインストールし、HADR 構成で IBM Db2 LUW をインストールする方法について説明します。 

この記事では、HADR の IBM Db2 LUW をインストールして構成する方法や SAP ソフトウェアのインストールについては説明しません。 これらのタスクの実行に役立つように、SAP と IBM のインストール マニュアルへの参照を提供します。 この記事では、Azure 環境に固有の部分に重点が置かれています。 

SAP ノート [1928533] に記載されているように、サポートされる IBM Db2 バージョンは 10.5 以上です。

インストールを開始する前に、以下の SAP ノートとドキュメントを参照してください。

| SAP ノート | 説明 |
| --- | --- |
| [1928533] | SAP applications on Azure: Supported products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類) |
| [2015553] | SAP on Azure:Support prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [2178632] | SAP on Azure 用の主要な監視メトリック |
| [2191498] | SAP on Linux with Azure:Enhanced monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能) |
| [2243692] | Linux on Azure (IaaS) VM:SAP license issues (Microsoft Azure (IaaS) VM 上の Linux: SAP ライセンスの問題) |
| [1984787] | SUSE LINUX Enterprise Server 12:インストールに関する注記 |
| [1999351] | Troubleshooting enhanced Azure monitoring for SAP (強化された Azure Monitoring for SAP のトラブルシューティング) |
| [2233094] | DB6:IBM Db2 for Linux, UNIX, and Windows を使用する Azure 上の SAP アプリケーション - 追加情報 |
| [1612105] | DB6:HADR の Db2 に関する FAQ |


| ドキュメント | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes):Linux に必要な SAP ノートがすべて掲載されています |
| [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関するガイド |
| [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide] (この記事) |
| [Linux 上の SAP のための Azure Virtual Machines データベース管理システム (DBMS) のデプロイ][dbms-guide]に関するガイド |
| [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server for SAP Applications 12 SP4 のベスト プラクティス ガイド][sles-for-sap-bp] |
| [SUSE Linux Enterprise High Availability Extension 12 SP4][sles-ha-guide] |
| [SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>概要
高可用性を実現するには、HADR を搭載した IBM Db2 LUW を 2 台以上の Azure 仮想マシンにインストールします。このとき、これらの仮想マシンは、[Azure 可用性セット](../../windows/tutorial-availability-sets.md)にデプロイするか、[Azure Availability Zones](./sap-ha-availability-zones.md) にデプロイします。 

以下の図には、データベース サーバーである 2 台の Azure VM の設定が示されています。 どちらのデータベース サーバー Azure VM も、それぞれ独自のストレージが接続され、稼動しています。 HADR では、いずれか 1 つの Azure VM にある 1 つのデータベース インスタンスにプライマリ インスタンスの役割が与えられます。 すべてのクライアントは、このプライマリ インスタンスに接続されています。 データベース トランザクションにおけるすべての変更は、Db2 のトランザクション ログとしてローカルに永続化されます。 トランザクション ログ レコードは、ローカルに永続化されると、セカンダリ データベース サーバー (スタンバイ サーバーまたはスタンバイ インスタンス) 上のデータベース インスタンスに TCP/IP 経由で転送されます。 スタンバイ インスタンスは、転送されたトランザクション ログ レコードをロールフォワードすることによってローカル データベースを更新します。 このようにして、スタンバイ サーバーはプライマリ サーバーと同期された状態で維持されます。

HADR は、レプリケーション機能にすぎません。 障害の検出や自動引き継ぎ (フェールオーバー) の機能はありません。 スタンバイ サーバーへの引き継ぎ (切り替え) は、データベース管理者が手動で開始する必要があります。 自動引き継ぎと障害検出を実現するために、Linux Pacemaker クラスタリング機能を使用できます。 Pacemaker では 2 つのデータベース サーバー インスタンスを監視します。 プライマリ データベース サーバー インスタンスがクラッシュすると、Pacemaker で、スタンバイ サーバーによる HADR の *自動* 引き継ぎが開始されます。 また、Pacemaker では確実に仮想 IP アドレスが新しいプライマリ サーバーに割り当てられます。

![IBM Db2 高可用性の概要](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP アプリケーション サーバーをプライマリ データベースに接続するには、仮想ホスト名と仮想 IP アドレスが必要です。 フェールオーバーが発生した場合は、SAP アプリケーション サーバーが新しいプライマリ データベース インスタンスに接続されます。 Azure の環境では、IBM Db2 の HADR に必要な方法で仮想 IP アドレスを使用するために [Azure ロード バランサー](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)が必要になります。 

HADR の IBM Db2 LUW と Pacemaker が高可用性 SAP システムの設定にいかに適しているかを十分に理解するのに役立つように、次の図では、IBM Db2 データベースに基づく SAP システムの高可用性設定の概要を示します。 この記事では、IBM Db2 のみを取り上げていますが、SAP システムの他のコンポーネントの設定方法に関する他の記事への参照を提供します。

![IBM DB2 高可用性の完全な環境の概要](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>必要な手順の概要
IBM Db2 構成をデプロイするには、これらの手順に従う必要があります。

  + 環境を計画する。
  + VM をデプロイします。
  + SUSE Linux を更新してファイル システムを構成する。
  + Pacemaker をインストールして構成する。
  + [高可用性 NFS][nfs-ha] をインストールする。
  + [別のクラスターに ASCS/ERS][ascs-ha] をインストールする。
  + 分散/高可用性オプション (SWPM) で IBM Db2 データベースをインストールする。
  + セカンダリ データベース ノードとインスタンスをインストールおよび作成し、HADR を構成する。
  + HADR が動作していることを確認する。
  + Pacemaker の構成を適用して IBM Db2 を制御する。
  + Azure Load Balancer を構成する。
  + プライマリおよびダイアログ アプリケーション サーバーをインストールする。
  + SAP アプリケーション サーバーの構成を確認して調整する。
  + フェールオーバーおよび引き継ぎテストを実行する。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR の IBM Db2 LUW をホストするための Azure インフラストラクチャを計画する

デプロイする前に、計画プロセスを完了します。 計画では、Azure で HADR の Db2 の構成をデプロイするための基盤を築きます。 IMB Db2 LUW (SAP 環境のデータベース部分) の計画に含める必要がある重要な要素は、以下の表にリストされています。

| トピック | 簡単な説明 |
| --- | --- |
| Azure リソース グループを定義する | VM、VNet、Azure Load Balancer、およびその他のリソースをデプロイするリソース グループ。 既存のものでも、新しいものでもかまいません。 |
| 仮想ネットワークまたはサブネットの定義 | IBM Db2 の VM と Azure Load Balancer のデプロイ先。 既存のものでも、新たに作成したものでもかまいません。 |
| IBM Db2 LUW をホストしている仮想マシン | VM サイズ、ストレージ、ネットワーク、IP アドレス。 |
| IBM Db2 データベースの仮想ホスト名と仮想 IP| SAP アプリケーション サーバーの接続に使用される仮想 IP またはホスト名。 **db-virt-hostname**、**db-virt-ip**。 |
| Azure フェンス | Azure フェンスまたは SBD フェンス (強く推奨)。 スプリット ブレインの状況を回避するための方法。 |
| SBD VM | SBD 仮想マシンのサイズ、ストレージ、ネットワーク。 |
| Azure Load Balancer | Basic または Standard (推奨)、Db2 データベース向けのプローブ ポート **probe-port** (62500 を推奨) の使用。 |
| 名前解決| その環境における名前解決のしくみ。 DNS サービスを強くお勧めします。 ローカル ホスト ファイルを使用できます。 |
    
Azure の Linux Pacemaker の詳細については、「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](./high-availability-guide-suse-pacemaker.md)」を参照してください。

## <a name="deployment-on-suse-linux"></a>SUSE Linux へのデプロイ

IBM Db2 LUW のリソース エージェントは、SUSE Linux Enterprise Server for SAP Applications に同梱されています。 このドキュメントで説明されている設定では、SUSE Linux Server for SAP Applications を使用する必要があります。 Azure Marketplace には、SUSE Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい Azure 仮想マシンのデプロイに使用できます。 Azure VM Marketplace で VM イメージを選択するときに Azure Marketplace を介して SUSE によって提供される、さまざまなサポートまたはサービス モデルに注意してください。 

### <a name="hosts-dns-updates"></a>ホスト:DNS の更新
仮想ホスト名を含む、すべてのホスト名のリストを作成し、適切な IP アドレスからホスト名への解決ができるよう、DNS サーバーを更新します。 DNS サーバーが存在しない場合や DNS エントリを更新したり作成したりできない場合は、このシナリオに参加している各 VM のローカル ホスト ファイルを使用する必要があります。 ホスト ファイルのエントリを使用する場合は、エントリが SAP システム環境内のすべての VM に適用されていることを確認します。 しかし、Azure に理想的な形で拡張される DNS を使用することをお勧めします


### <a name="manual-deployment"></a>手動デプロイ

選択した OS が IBM Db2 LUW 用の IBM/SAP によってサポートされていることを確認してください。 Azure VM と Db2 リリースでサポートされている OS バージョンのリストは、SAP ノート [1928533] で確認できます。 Db2 リリースごとの OS リリースのリストは、SAP 製品の可用性マトリックスにあります。 SLES 12 SP4 以上の使用を強くお勧めします。このバージョン以降の SUSE Linux で Azure に関連したパフォーマンスが向上するためです。

1. リソース グループを作成または選択します。
1. 仮想ネットワークとサブネットを作成または選択します。
1. Azure 可用性セットを作成するか、可用性ゾーンをデプロイします。
    + 可用性セットの場合は、最大更新ドメインを 2 に設定します。
1. 仮想マシン 1 を作成します。
    + Azure Marketplace の SLES for SAP イメージを使用します。
    + 手順 3 で作成した Azure 可用性セットを選択するか、可用性ゾーンを選択します。
1.  仮想マシン 2 を作成します。
    + Azure Marketplace の SLES for SAP イメージを使用します。
    + 手順 3 で作成した Azure 可用性セットを選択するか、可用性ゾーン (手順 3 と同じゾーンではない) を選択します。
1. VM にデータ ディスクを追加した後、「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ][dbms-db2]」という記事のファイル システム設定に関する推奨事項を確認します。

## <a name="create-the-pacemaker-cluster"></a>Pacemaker クラスターを作成する
    
この IBM Db2 サーバー用に基本的な Pacemaker クラスターを作成する場合は、「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする][sles-pacemaker]」を参照してください。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>IBM Db2 LUW と SAP 環境をインストールする

IBM Db2 LUW に基づく SAP 環境のインストールを開始する前に、以下のドキュメントを確認してください。

+ Azure のドキュメント
+ SAP のドキュメント
+ IBM のドキュメント

このドキュメントへのリンクは、この記事の概要セクションで提供されています。

IBM Db2 LUW での NetWeaver ベース アプリケーションのインストールについては、SAP のインストール マニュアルを確認してください。

SAP ヘルプ ポータルに関するガイドは、[SAP Installation Guide Finder][sap-instfind] を使用して検索できます。

以下のフィルターを設定することで、ポータルに表示されるガイドの数を減らすことができます。

- [I want to]\(検索対象\): "Install a new system"
- [My Database]\(使用データベース\): "IBM Db2 for Linux, Unix, and Windows"
- SAP NetWeaver のバージョン、スタック構成、オペレーティング システムに関するその他のフィルター

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR 搭載の IBM Db2 LUW を設定するためのインストールのヒント

プライマリ IBM Db2 LUW データベース インスタンスを設定するには、次のようにします。

- 高可用性または分散オプションを使用する。
- SAP ASCS/ERS とデータベース インスタンスをインストールする。
- 新たにインストールされたデータベースのバックアップを作成する。


> [!IMPORTANT] 
> インストール中に設定される "データベース通信ポート" を書き留めておいてください。 これは、両方のデータベース インスタンスに対して同じポート番号である必要があります。

SAP の同種システム コピー手順を使用してスタンバイ データベース サーバーを設定するには、これらの手順を行います。

1. **[System copy]\(システム コピー\)** オプション > **[Target systems]\(ターゲット システム\)**  >  **[分散]**  >  **[データベース インスタンス]** の順に選択します。
1. バックアップを使用してスタンバイ サーバー インスタンスにバックアップを復元できるように、コピー方法として **[Homogeneous System]\(同種システム\)** を選択します。
1. 同種システム コピーのデータベースを復元する最後の手順に到達したら、インストーラーを終了します。 プライマリ ホストのバックアップからデータベースを復元します。 後続のインストール フェーズはすべて、プライマリ データベース サーバー上で実行済みです。
1. IBM Db2 の HADR を設定します。

   > [!NOTE]
   > Azure と Pacemaker に固有のインストールと構成の場合:SAP Software Provisioning Manager を使用したインストール手順の中で、IBM Db2 LUW の高可用性に関してはっきりした疑問が生じます。
   >+ **[IBM Db2 pureScale]** は選択しない。
   >+ **[Install IBM Tivoli System Automation for Multiplatforms]\(IBM Tivoli System Automation for Multiplatforms のインストール\)** は選択しない。
   >+ **[Generate cluster configuration files]\(クラスター構成ファイルの生成\)** は選択しない。

   Linux Pacemaker に SBD デバイスを使用する場合は、以下の Db2 HADR パラメーターを設定します。
   + HADR peer window duration (seconds) (HADR_PEER_WINDOW) = 300  
   + HADR timeout value (HADR_TIMEOUT) = 60

   Azure Pacemaker フェンス エージェントを使用する場合は、次のパラメーターを設定します。
   + HADR peer window duration (seconds) (HADR_PEER_WINDOW) = 900  
   + HADR timeout value (HADR_TIMEOUT) = 60

最初のフェールオーバー/引き継ぎテストに基づく上記のパラメーターをお勧めします。 これらのパラメーター設定でフェールオーバーと引き継ぎが適切に機能するかどうかを必ずテストしてください。 個々の構成は異なる場合があるため、パラメーターには調整が必要になる可能性があります。 

> [!IMPORTANT]
> 通常起動を使用した HADR 構成の IBM Db2 に固有の情報:プライマリ データベース インスタンスを起動する前に、セカンダリまたはスタンバイ データベース インスタンスが稼動している必要があります。

デモンストレーションの目的上、このドキュメントで説明されている手順では、データベースの SID が **PTR** となっています。

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR の確認
HADR を構成し、プライマリおよびスタンバイ ノードの状態が PEER および CONNECTED になったら、以下の確認を行います。

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

ノードの障害発生時に自動フェールオーバーに Pacemaker を使用する場合は、Db2 インスタンスと Pacemaker を適宜構成する必要があります。 このセクションでは、この種の構成について説明します。

以下の項目には、次のいずれかのプレフィックスが付いています。

- **[A]** :すべてのノードに適用できます
- **[1]** :ノード 1 にのみ適用できます 
- **[2]** :ノード 2 にのみ適用できます

**[A]** Pacemaker の構成に関する前提条件:
1. ユーザー db2\<sid> で、db2stop を使用して両方のデータベース サーバーをシャットダウンします。
1. db2\<sid> ユーザーのシェル環境を */bin/ksh* に変更します。 Yast ツールを使用することをお勧めします。 


### <a name="pacemaker-configuration"></a>Pacemaker の構成

> [!IMPORTANT]
> 最近のテストで、バックログと 1 つの接続のみを処理するという制限があるため、netcat によって要求への応答が停止される状況があることが明らかになりました。 netcat リソースでは、Azure ロード バランサー要求のリッスンを停止し、フローティング IP は使用できなくなります。  
> 既存の Pacemaker クラスターについては、以前、netcat を socat に置き換えることをお勧めしました。 現時点では、resource-agents パッケージの一部である azure-lb リソース エージェントを使用することをお勧めしています。パッケージのバージョン要件は次のとおりです。
> - SLES 12 SP4/SP5 の場合、バージョンは resource-agents-4.3.018.a7fb5035-3.30.1 以上である必要があります。  
> - SLES 15/15 SP1 の場合、バージョンは resource-agents-4.3.0184.6ee15eb2-4.13.1 以上である必要があります。  
>
> 変更には短時間のダウンタイムが必要であることに注意してください。  
> 既存の Pacemaker クラスターについては、「[Azure Load-Balancer の検出のセキュリティ強化](https://www.suse.com/support/kb/doc/?id=7024128)」で説明されているように、socat を使用するよう構成が既に変更されていた場合は、すぐに azure-lb リソース エージェントに切り替える必要はありません。

**[1]** IBM Db2 HADR 固有の Pacemaker の構成:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM Db2 リソースを作成する:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** IBM Db2 リソースを起動する:
* Pacemaker のメンテナンス モードを解除します。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodes configured
# <a name="5-resources-configured"></a>5 resources configured

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Full list of resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd):Started azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):     Started azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):    Started azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaves: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Pacemaker のツールを使用して、Pacemaker によってクラスター化された Db2 インスタンスを管理する必要があります。 db2stop などの db2 コマンドを使用する場合、Pacemaker ではアクションがリソースのエラーとして検出されます。 メンテナンスを行う場合は、ノードまたはリソースをメンテナンス モードにすることができます。 Pacemaker によってリソースの監視が中断されます。その後、通常の db2 管理コマンドを使用できます。


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer を構成する
Azure Load Balancer を構成する場合は、[Azure Standard Load Balancer SKU](../../../load-balancer/load-balancer-overview.md) を使用して、以下の操作を行うことをお勧めします。

> [!NOTE]
> Standard Load Balancer SKU では、Load Balancer の管理下にあるノードからパブリック IP アドレスへのアクセスが制限されます。 「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」という記事では、それらのノードからパブリック IP アドレスにアクセスできるようにする方法が説明されています

> [!IMPORTANT]
> フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

1. フロントエンド IP プールを作成する:

   a. Azure portal で Azure Load Balancer を開き、 **[フロントエンド IP プール]** を選んでから、 **[追加]** を選択します。

   b. 新規のフロントエンド IP プールの名前を入力します (例: **Db2-connection**)。

   c. **[割り当て]** を **[静的]** に設定し、最初に定義した IP アドレス **Virtual-IP** を入力します。

   d. **[OK]** を選択します。

   e. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

1. バックエンド プールを作成する:

   a. Azure portal で Azure Load Balancer を開き、 **[バックエンド プール]** を選んでから、 **[追加]** を選択します。

   b. 新しいバックエンド プールの名前を入力します (例: **Db2-backend**)。

   c. **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。

   d. 前の手順で作成された IBM Db2 データベースをホストする仮想マシンまたは可用性セットを選択します。

   e. IBM Db2 クラスターの仮想マシンを選択します。

   f. **[OK]** を選択します。

1. 正常性プローブを作成する:

   a. Azure portal で Azure Load Balancer を開き、 **[正常性プローブ]** を選び、 **[追加]** を選択します。

   b. 新しい正常性プローブの名前を入力します (例: **Db2-hp**)。

   c. プロトコルとして **[TCP]** を選択し、ポート **62500** を選択します。 **[間隔]** の値を **[5]** に設定したままにし、 **[異常のしきい値]** の値を **[2]** に設定したままにします。

   d. **[OK]** を選択します。

1. 負荷分散規則を作成します。

   a. Azure portal で Azure Load Balancer を開き、 **[負荷分散規則]** を選んでから、 **[追加]** を選択します。

   b. 新しいロード バランサー規則の名前を入力します (例: **Db2-SID**)。

   c. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **Db2-frontend**)。

   d. **[プロトコル]** の設定は **[TCP]** のままにして、ポート <*データベース通信ポート*> を入力します。

   e. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします

   f. **Floating IP を有効にします**。

   g. **[OK]** を選択します。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>接続に仮想 IP を使用するよう SAP プロファイルに変更を加える
HADR 構成のプライマリ インスタンスに接続するには、SAP アプリケーション レイヤーで、Azure Load Balancer 用に定義して構成した仮想 IP アドレスを使用する必要があります。 次の変更が必要です。

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>プライマリおよびダイアログ アプリケーション サーバーをインストールする

Db2 HADR 構成に対してプライマリおよびダイアログ アプリケーション サーバーをインストールする際は、その構成用に選択した仮想ホスト名を使用します。 

Db2 HADR 構成を作成する前にインストールを行った場合は、前のセクションの説明に従って変更を加え、SAP Java スタックに対しては次のように変更を行う必要があります。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java または Java スタック システムの JDBC URL の確認

J2EE Config ツールを使用して JDBC URL を確認または更新します。 J2EE Config ツールはグラフィカル ツールであるため、X サーバーがインストールされている必要があります。
 
1. J2EE インスタンスのプライマリ アプリケーション サーバーにサインインし、`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`を実行します。
1. 左側のフレームで、**セキュリティ ストア** を選択します。
1. 右側のフレームで、キー jdbc/pool/\<SAPSID>/url を選択します。
1. JDBC URL のホスト名を仮想ホスト名に変更します。
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. **[追加]** を選択します。
1. 変更を保存するには、左上のディスク アイコンを選択します。
1. 構成ツールを閉じます。
1. Java インスタンスを再起動します。

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR 設定のログ アーカイブを構成する
HADR 設定の Db2 ログ アーカイブを構成するには、すべてのログ アーカイブ保存先から自動的にログを取得できるようにプライマリとスタンバイの両方のデータベースを構成することをお勧めします。 プライマリ データベースとスタンバイ データベースの両方で、すべてのログ アーカイブの場所 (データベース インスタンスの 1 つでログ ファイルがアーカイブされる可能性のある場所) からログ アーカイブ ファイルを取得できる必要があります。 

ログ アーカイブは、プライマリ データベースでのみ実行されます。 データベース サーバーの HADR ロールを変更した場合または障害が発生した場合は、新しいプライマリ データベースがログ アーカイブを担います。 複数のログ アーカイブの場所を設定した場合は、ログが 2 回アーカイブされる可能性があります。 ローカルまたはリモートのキャッチアップが発生した場合は、古いプライマリ サーバーから新しいプライマリ サーバーのアクティブなログの場所に、アーカイブされたログを手動でコピーする必要がある場合もあります。

両方のノードからログが書き込まれる共通の NFS 共有を構成することをお勧めします。 NFS 共有は高可用である必要があります。 

トランスポートまたはプロファイル ディレクトリで既存の高可用性 NFS 共有を使用することができます。 詳細については、次を参照してください。

- [SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性][nfs-ha] 
- [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (NFS 共有を作成する場合)


## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

このセクションでは、Db2 の HADR 設定をテストする方法について説明します。 *すべてのテストで前提となるのは、root ユーザーとしてログインしていること* と、IBM Db2 プライマリが *azibmdb01* 仮想マシンで実行されていることです。

すべてのテスト ケースの初期状態は次のとおりです (crm_mon -r または crm status)。

- **crm status**: 実行時の Pacemaker 状態のスナップショット 
- **crm_mon -r**: Pacemaker 状態の連続出力

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP システムにおける最初の状態は、次のイメージに示されているように、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) > [構成] > [概要] に文書化されます。

![DBACockpit - 移行前](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2 の引き継ぎをテストする


> [!IMPORTANT] 
> テストを開始する前に、次のことを確認します。
> * Pacemaker に失敗したアクションがない (crm status)。
> * 場所の制約 (移行テストの残り) がない
> * IBM Db2 HADR 同期が動作している。 ユーザー db2\<sid> で確認します <pre><code>db2pd -hadr -db \<DBSID></code></pre>


次のコマンドを実行して、プライマリ Db2 データベースを実行しているノードを移行します。
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

移行が完了した後、crm status の出力は次のようになります。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP システムにおける最初の状態は、次のイメージに示されているように、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) > [構成] > [概要] に文書化されます。

![DBACockpit - 移行後](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

"crm resource migrate" を使用したリソースの移行により、場所の制約が作成されます。 場所の制約は削除する必要があります。 場所の制約が削除されていないと、リソースをフェールバックできない場合や、不要な引き継ぎが行われる場合があります。 

リソースを再度 *azibmdb01* に移行し、場所の制約をクリアします
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm resource migrate \<res_name> \<host>:** 場所の制約を作成します。引き継ぎで問題が生じる場合があります
- **crm resource clear \<res_name>** :場所の制約をクリアします
- **crm resource cleanup \<res_name>** :リソースのすべてのエラーをクリアします

### <a name="test-the-fencing-agent"></a>フェンス エージェントをテストする

この場合は、SBD フェンスをテストします。これは、SUSE Linux を使用するときに実行することをお勧めします。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

クラスター ノード *azibmdb01* を再起動する必要があります。 IBM Db2 のプライマリ HADR ロールは *azibmdb02* に移動されます。 *azibmdb01* がオンラインに戻ると、Db2 インスタンスがセカンダリ データベース インスタンスのロールに移動します。 

再起動された以前のプライマリで Pacemaker サービスが自動的に起動しない場合は、次のように手動で起動してください。

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>手動での引き継ぎをテストする

手動での引き継ぎをテストするには、*azibmdb01* ノードで Pacemaker サービスを停止します。
<pre><code>service pacemaker stop</code></pre>

*azibmdb02* の状態
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

フェールオーバー後、再び *azibmdb01* 上でサービスを開始できます。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR プライマリ データベースを実行するノード上で Db2 プロセスを中止する

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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker によって、同じノードで Db2 プライマリ データベース インスタンスが再起動されます。または、セカンダリ データベース インスタンスを実行しているノードにフェールオーバーされ、エラーがレポートされます。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>セカンダリ データベース インスタンスを実行するノード上の Db2 プロセスを中止する

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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR プライマリ データベース インスタンスを実行するノード上の DB を db2stop force を使用して停止する

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR プライマリ データベース インスタンスを実行するノード上の VM を再起動でクラッシュさせる

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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>HADR プライマリ データベース インスタンスを実行する VM を "停止" してクラッシュさせる

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

この場合、Pacemaker で、プライマリ データベース インスタンスを実行しているノードが応答していないことが検出されます。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

次の手順は、*スプリット ブレイン* の状況を確認することです。 プライマリ データベース インスタンスを最後に実行したノードがダウンしていることが、最後まで残ったノードで確認された後、リソースのフェールオーバーが実行されます。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


ノードが "停止" した場合、障害が発生したノードを (Azure portal、PowerShell、または Azure CLI で) Azure 管理ツールを使用して再起動する必要があります。 障害が発生したノードがオンラインに戻った後、Db2 インスタンスがセカンダリ ロールとして起動されます。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>次のステップ
- [SAP NetWeaver のための高可用性のアーキテクチャとシナリオ](./sap-high-availability-architecture-scenarios.md)
- [Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](./high-availability-guide-suse-pacemaker.md)

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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
