---
title: RHEL 上の Azure 仮想マシン (VM) 上で IBM Db2 HADR を設定する | Microsoft Docs
description: Azure 仮想マシン (VM) RHEL 上で IBM Db2 LUW の高可用性を実現します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 979de230655b4804fa65a4bddb678e21b59c4ea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674446"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server 上の Azure VM での IBM Db2 LUW の高可用性

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
| [2002167] | Red Hat Enterprise Linux 7.x:Installation and Upgrade (Red Hat Enterprise Linux 7.x: インストールとアップグレード) |
| [2694118] | Azure の Red Hat Enterprise Linux HA アドオン |
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
| [Red Hat Enterprise Linux 7 の高可用性アドオンの概要][rhel-ha-addon] |
| [高可用性アドオンの管理][rhel-ha-admin] |
| [高可用性アドオンの参照][rhel-ha-ref] |
| [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines][rhel-azr-supp]
| [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する][rhel-azr-inst]
| [SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [RHEL 高可用性クラスターのサポートポリシー - クラスターでの IBM Db2 for Linux、Unix、および Windows の管理][rhel-db2-supp]



## <a name="overview"></a>概要
高可用性を実現するには、HADR を搭載した IBM Db2 LUW を 2 台以上の Azure 仮想マシンにインストールします。このとき、これらの仮想マシンは、[Azure 可用性セット](../../windows/tutorial-availability-sets.md)にデプロイするか、[Azure Availability Zones](./sap-ha-availability-zones.md) にデプロイします。 

以下の図には、データベース サーバーである 2 台の Azure VM の設定が示されています。 どちらのデータベース サーバー Azure VM も、それぞれ独自のストレージが接続され、稼動しています。 HADR では、いずれか 1 つの Azure VM にある 1 つのデータベース インスタンスにプライマリ インスタンスの役割が与えられます。 すべてのクライアントは、プライマリ インスタンスに接続されます。 データベース トランザクションにおけるすべての変更は、Db2 のトランザクション ログとしてローカルに永続化されます。 トランザクション ログ レコードは、ローカルに永続化されると、セカンダリ データベース サーバー (スタンバイ サーバーまたはスタンバイ インスタンス) 上のデータベース インスタンスに TCP/IP 経由で転送されます。 スタンバイ インスタンスは、転送されたトランザクション ログ レコードをロールフォワードすることによってローカル データベースを更新します。 このようにして、スタンバイ サーバーはプライマリ サーバーと同期された状態で維持されます。

HADR は、レプリケーション機能にすぎません。 障害の検出や自動引き継ぎ (フェールオーバー) の機能はありません。 スタンバイ サーバーへの引き継ぎ (切り替え) は、データベース管理者が手動で開始する必要があります。 自動引き継ぎと障害検出を実現するために、Linux Pacemaker クラスタリング機能を使用できます。 Pacemaker では 2 つのデータベース サーバー インスタンスを監視します。 プライマリ データベース サーバー インスタンスがクラッシュすると、Pacemaker で、スタンバイ サーバーによる HADR の *自動* 引き継ぎが開始されます。 また、Pacemaker では確実に仮想 IP アドレスが新しいプライマリ サーバーに割り当てられます。

![IBM Db2 高可用性の概要](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

SAP アプリケーション サーバーをプライマリ データベースに接続するには、仮想ホスト名と仮想 IP アドレスが必要です。 フェールオーバーが発生した場合は、SAP アプリケーション サーバーが新しいプライマリ データベース インスタンスに接続されます。 Azure の環境では、IBM Db2 の HADR に必要な方法で仮想 IP アドレスを使用するために [Azure ロード バランサー](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)が必要になります。 

HADR の IBM Db2 LUW と Pacemaker が高可用性 SAP システムの設定にいかに適しているかを十分に理解するのに役立つように、次の図では、IBM Db2 データベースに基づく SAP システムの高可用性設定の概要を示します。 この記事では、IBM Db2 のみを取り上げていますが、SAP システムの他のコンポーネントの設定方法に関する他の記事への参照を提供します。

![IBM DB2 高可用性の完全な環境の概要](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>必要な手順の概要
IBM Db2 構成をデプロイするには、これらの手順に従う必要があります。

  + 環境を計画する。
  + VM をデプロイします。
  + RHEL Linux を更新してファイル システムを構成する。
  + Pacemaker をインストールして構成する。
  + [glusterfs クラスター][glusterfs]または [Azure NetApp Files][anf-rhel] を設定する
  + [別のクラスターに ASCS/ERS][ascs-ha-rhel] をインストールする。
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
| Azure フェンス | スプリット ブレインの状況を回避するための方法が防止されます。 |
| Azure Load Balancer | Basic または Standard (推奨)、Db2 データベース向けのプローブ ポート **probe-port** (62500 を推奨) の使用。 |
| 名前解決| その環境における名前解決のしくみ。 DNS サービスを強くお勧めします。 ローカル ホスト ファイルを使用できます。 |
    
Azure の Linux Pacemaker の詳細については、「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする][rhel-pcs-azr]」を参照してください。

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux へのデプロイ

IBM Db2 LUW のリソース エージェントは、Red Hat Enterprise Linux Server HA アドオンに含まれています。 このドキュメントで説明されているセットアップでは、Red Hat Enterprise Linux for SAP を使用する必要があります。 Azure Marketplace には Red Hat Enterprise Linux 7.4 for SAP 以降のイメージが含まれており、これを新しい Azure 仮想マシンのデプロイに使用することができます。 Azure VM Marketplace で VM イメージを選択するときに Azure Marketplace を介して Red Hat によって提供される、さまざまなサポートまたはサービス モデルに注意してください。

### <a name="hosts-dns-updates"></a>ホスト:DNS の更新
仮想ホスト名を含む、すべてのホスト名のリストを作成し、適切な IP アドレスからホスト名への解決ができるよう、DNS サーバーを更新します。 DNS サーバーが存在しない場合や DNS エントリを更新したり作成したりできない場合は、このシナリオに参加している各 VM のローカル ホスト ファイルを使用する必要があります。 ホスト ファイルのエントリを使用する場合は、エントリが SAP システム環境内のすべての VM に適用されていることを確認します。 しかし、Azure に理想的な形で拡張される DNS を使用することをお勧めします


### <a name="manual-deployment"></a>手動デプロイ

選択した OS が IBM Db2 LUW 用の IBM/SAP によってサポートされていることを確認してください。 Azure VM と Db2 リリースでサポートされている OS バージョンのリストは、SAP ノート [1928533] で確認できます。 Db2 リリースごとの OS リリースのリストは、SAP 製品の可用性マトリックスにあります。 Red Hat Enterprise Linux 7.4 for SAP 以上の使用を強くお勧めします。このバージョン以降の Red Hat Enterprise Linux で Azure に関連したパフォーマンスが向上するためです。

1. リソース グループを作成または選択します。
1. 仮想ネットワークとサブネットを作成または選択します。
1. Azure 可用性セットを作成するか、可用性ゾーンをデプロイします。
    + 可用性セットの場合は、最大更新ドメインを 2 に設定します。
1. 仮想マシン 1 を作成します。
    + Azure Marketplace の SAP イメージには Red Hat Enterprise Linux を使用します。
    + 手順 3 で作成した Azure 可用性セットを選択するか、可用性ゾーンを選択します。
1.  仮想マシン 2 を作成します。
    + Azure Marketplace の SAP イメージには Red Hat Enterprise Linux を使用します。
    + 手順 3 で作成した Azure 可用性セットを選択するか、可用性ゾーン (手順 3 と同じゾーンではない) を選択します。
1. VM にデータ ディスクを追加した後、「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ][dbms-db2]」という記事のファイル システム設定に関する推奨事項を確認します。

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

#### <a name="red-hat-firewall-rules"></a>Red Hat のファイアウォール規則
Red Hat Enterprise Linux では、ファイアウォールが既定で有効になっています。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR 搭載の IBM Db2 LUW を設定するためのインストールのヒント

プライマリ IBM Db2 LUW データベース インスタンスを設定するには、次のようにします。

- 高可用性または分散オプションを使用する。
- SAP ASCS/ERS とデータベース インスタンスをインストールする。
- 新たにインストールされたデータベースのバックアップを作成する。

> [!IMPORTANT] 
> インストール中に設定される "データベース通信ポート" を書き留めておいてください。 これは、両方のデータベース インスタンスに対して同じポート番号である必要があります。
>![SAP SWPM ポート定義](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Azure 用の IBM Db2 HADR 設定

   Azure Pacemaker フェンス エージェントを使用する場合は、次のパラメーターを設定します。

   - HADR peer window duration (seconds) (HADR_PEER_WINDOW) = 240  
   - HADR timeout value (HADR_TIMEOUT) = 45

最初のフェールオーバー/引き継ぎテストに基づく上記のパラメーターをお勧めします。 これらのパラメーター設定でフェールオーバーと引き継ぎが適切に機能するかどうかを必ずテストしてください。 個々の構成は異なる場合があるため、パラメーターには調整が必要になる可能性があります。 

> [!NOTE]
> 通常起動を使用した HADR 構成の IBM Db2 に固有の情報:プライマリ データベース インスタンスを起動する前に、セカンダリまたはスタンバイ データベース インスタンスが稼動している必要があります。

   
> [!NOTE]
> Azure と Pacemaker に固有のインストールと構成の場合:SAP Software Provisioning Manager を使用したインストール手順の中で、IBM Db2 LUW の高可用性に関してはっきりした疑問が生じます。
>+ **[IBM Db2 pureScale]** は選択しない。
>+ **[Install IBM Tivoli System Automation for Multiplatforms]\(IBM Tivoli System Automation for Multiplatforms のインストール\)** は選択しない。
>+ **[Generate cluster configuration files]\(クラスター構成ファイルの生成\)** は選択しない。
>![SAP SWPM - DB2 HA オプション](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


SAP の同種システム コピー手順を使用してスタンバイ データベース サーバーを設定するには、これらの手順を行います。

1. **[System copy]\(システム コピー\)** オプション > **[Target systems]\(ターゲット システム\)**  >  **[分散]**  >  **[データベース インスタンス]** の順に選択します。
1. バックアップを使用してスタンバイ サーバー インスタンスにバックアップを復元できるように、コピー方法として **[Homogeneous System]\(同種システム\)** を選択します。
1. 同種システム コピーのデータベースを復元する最後の手順に到達したら、インストーラーを終了します。 プライマリ ホストのバックアップからデータベースを復元します。 後続のインストール フェーズはすべて、プライマリ データベース サーバー上で実行済みです。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR 用の Red Hat ファイアウォール規則
HADR が機能するように、DB2 へのトラフィックおよび DB2 間のトラフィックを許可するファイアウォール規則を追加します。
+ データベース通信ポート。 パーティションを使用する場合は、これらのポートも追加します。
+ HADR ポート (DB2 パラメーター HADR_LOCAL_SVC の値)
+ Azure プローブ ポート
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR の確認
デモンストレーションの目的上、このドキュメントで説明されている手順では、データベースの SID が **ID2** となっています。

HADR を構成し、プライマリおよびスタンバイ ノードの状態が PEER および CONNECTED になったら、以下の確認を行います。

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N


#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N
</code></pre>

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

**[A]** プローブ ポートのファイアウォール規則を追加します。

<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

## <a name="create-the-pacemaker-cluster"></a>Pacemaker クラスターを作成する
    
この IBM Db2 サーバー用に基本的な Pacemaker クラスターを作成する場合は、「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする][rhel-pcs-azr]」を参照してください。 

## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker の構成

ノードの障害発生時に自動フェールオーバーに Pacemaker を使用する場合は、Db2 インスタンスと Pacemaker を適宜構成する必要があります。 このセクションでは、この種の構成について説明します。

以下の項目には、次のいずれかのプレフィックスが付いています。

- **[A]** :すべてのノードに適用できます
- **[1]** :ノード 1 にのみ適用できます 
- **[2]** :ノード 2 にのみ適用できます

**[A]** Pacemaker の構成に関する前提条件:
1. ユーザー db2\<sid> で、db2stop を使用して両方のデータベース サーバーをシャットダウンします。
1. db2\<sid> ユーザーのシェル環境を */bin/ksh* に変更します。
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>  

### <a name="pacemaker-configuration"></a>Pacemaker の構成

**[1]** IBM Db2 HADR 固有の Pacemaker の構成:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM Db2 リソースを作成する:

**RHEL 7. x** でクラスターを構築する場合は、次のコマンドを使用します。

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**RHEL 8. x** でクラスターを構築する場合は、次のコマンドを使用します。

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' promotable meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-clone meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-clone

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-clone then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** IBM Db2 リソースを起動する:
* Pacemaker のメンテナンス モードを解除します。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。
<pre><code>sudo pcs status
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

> [!IMPORTANT]
> Pacemaker のツールを使用して、Pacemaker によってクラスター化された Db2 インスタンスを管理する必要があります。 db2stop などの db2 コマンドを使用する場合、Pacemaker ではアクションがリソースのエラーとして検出されます。 メンテナンスを行う場合は、ノードまたはリソースをメンテナンス モードにすることができます。 Pacemaker によってリソースの監視が中断されます。その後、通常の db2 管理コマンドを使用できます。

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
 
1. J2EE インスタンスのプライマリ アプリケーション サーバーにサインインし、以下を実行します。
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 左側のフレームで、**セキュリティ ストア** を選択します。
1. 右側のフレームで、キー `jdbc/pool/\<SAPSID>/url` を選択します。
1. JDBC URL のホスト名を仮想ホスト名に変更します。
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. **[追加]** を選択します。
1. 変更を保存するには、左上のディスク アイコンを選択します。
1. 構成ツールを閉じます。
1. Java インスタンスを再起動します。

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR 設定のログ アーカイブを構成する

HADR 設定の Db2 ログ アーカイブを構成するには、すべてのログ アーカイブ保存先から自動的にログを取得できるようにプライマリとスタンバイの両方のデータベースを構成することをお勧めします。 プライマリ データベースとスタンバイ データベースの両方で、すべてのログ アーカイブの場所 (データベース インスタンスの 1 つでログ ファイルがアーカイブされる可能性のある場所) からログ アーカイブ ファイルを取得できる必要があります。 

ログ アーカイブは、プライマリ データベースでのみ実行されます。 データベース サーバーの HADR ロールを変更した場合または障害が発生した場合は、新しいプライマリ データベースがログ アーカイブを担います。 複数のログ アーカイブの場所を設定した場合は、ログが 2 回アーカイブされる可能性があります。 ローカルまたはリモートのキャッチアップが発生した場合は、古いプライマリ サーバーから新しいプライマリ サーバーのアクティブなログの場所に、アーカイブされたログを手動でコピーする必要がある場合もあります。

両方のノードからログが書き込まれる共通の NFS 共有または GlusterFS を構成することをお勧めします。 NFS 共有または GlusterFS は高可用である必要があります。 

トランスポートまたはプロファイル ディレクトリで既存の高可用性 NFS 共有または GlusterFS を使用することができます。 詳細については、次を参照してください。

- [Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS][glusterfs] 
- [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (NFS 共有を作成する場合)

## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

このセクションでは、Db2 の HADR 設定をテストする方法について説明します。 すべてのテストでは、IBM Db2 プライマリが *az-idb01* 仮想マシン上で実行されていることを前提としています。 sudo 特権を持つユーザーまたは root (推奨されません) を使用する必要があります。

すべてのテスト ケースの初期状態は次のとおりです (crm_mon -r または pcs status)。

- **pcs status**: 実行時の Pacemaker 状態のスナップショット 
- **crm_mon -r**: Pacemaker 状態の連続出力

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP システムにおける最初の状態は、次のイメージに示されているように、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) > [構成] > [概要] に文書化されます。

![DBACockpit - 移行前](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)

### <a name="test-takeover-of-ibm-db2"></a>IBM Db2 の引き継ぎをテストする


> [!IMPORTANT] 
> テストを開始する前に、次のことを確認します。
> * Pacemaker に失敗したアクションがない (pcs status)。
> * 場所の制約 (移行テストの残り) がない
> * IBM Db2 HADR 同期が動作している。 ユーザー db2\<sid> で確認します <pre><code>db2pd -hadr -db \<DBSID></code></pre>

次のコマンドを実行して、プライマリ Db2 データベースを実行しているノードを移行します。
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
</code></pre>

移行が完了した後、crm status の出力は次のようになります。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP システムにおける最初の状態は、次のイメージに示されているように、[Transaction DBACOCKPIT]\(トランザクション DBACOCKPIT\) > [構成] > [概要] に文書化されます。

![DBACockpit - 移行後](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

"pcs resource move" を使用したリソースの移行により、場所の制約が作成されます。 この場合、場所の制約により、az-idb01 で IBM Db2 インスタンスの実行が妨げられています。 場所の制約が削除されていないと、リソースをフェールバックできません。

場所の制約を削除すると、スタンバイ ノードは az-idb01 で開始されます。
<pre><code># On RHEL 7.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

クラスターの状態は次のように変更されます。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - 削除された場所の制約](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)

リソースを再度 *az-idb01* に移行し、場所の制約をクリアします
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

- **RHEL 7.x の場合: pcs resource move \<res_name> <host>:** 場所の制約を作成します。引き継ぎで問題が生じる場合があります
- **RHEL 8.x の場合: pcs resource move \<res_name> --master:** 場所の制約を作成します。引き継ぎで問題が生じる場合があります
- **pcs resource clear \<res_name>** :場所の制約をクリアします
- **pcs resource cleanup \<res_name>** :リソースのすべてのエラーをクリアします

### <a name="test-a-manual-takeover"></a>手動での引き継ぎをテストする

手動での引き継ぎをテストするには、*az-idb01* ノードで Pacemaker サービスを停止します。
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02* の状態
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

フェールオーバー後、再び *az-idb01* 上でサービスを開始できます。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR プライマリ データベースを実行するノード上で Db2 プロセスを中止する

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 インスタンスで障害が発生し、Pacemaker によってマスター ノードが移動され、次の状態がレポートされます。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker によって、同じノードで Db2 プライマリ データベース インスタンスが再起動されます。または、セカンダリ データベース インスタンスを実行しているノードにフェールオーバーされ、エラーがレポートされます。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>セカンダリ データベース インスタンスを実行するノード上の Db2 プロセスを中止する

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

このノードは、失敗した状態になり、エラーがレポートされます。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 インスタンスが、以前割り当てられていたセカンダリ ロールで再起動されます。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR プライマリ データベース インスタンスを実行するノード上の DB を db2stop force を使用して停止する

ユーザー db2\<sid> として、db2stop force コマンドを実行します。
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

障害が検出されました。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR セカンダリ データベース インスタンスがプライマリ ロールに昇格されました。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>HADR プライマリ データベース インスタンスを実行する VM を "停止" してクラッシュさせる

<pre><code>#Linux kernel panic.
sudo echo b > /proc/sysrq-trigger</code></pre>

この場合、Pacemaker で、プライマリ データベース インスタンスを実行しているノードが応答していないことが検出されます。

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

次の手順は、*スプリット ブレイン* の状況を確認することです。 プライマリ データベース インスタンスを最後に実行したノードがダウンしていることが、最後まで残ったノードで確認された後、リソースのフェールオーバーが実行されます。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


カーネル パニックが発生した場合、障害が発生したノードは、フェンス エージェントによって再起動されます。 障害が発生したノードがオンラインに戻ったら、次によって Pacemaker クラスターを開始する必要があります。
<pre><code>sudo pcs cluster start</code></pre> これにより、Db2 インスタンスがセカンダリ ロールで起動されます。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>次のステップ
- [SAP NetWeaver のための高可用性のアーキテクチャとシナリオ](./sap-high-availability-architecture-scenarios.md)
- [Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
