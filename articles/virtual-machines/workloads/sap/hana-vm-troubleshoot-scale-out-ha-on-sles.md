---
title: Azure 仮想マシン上での SLES 12 SP3 による SAP HANA 2.0 スケールアウトの HSR と Pacemaker のセットアップに対するトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンで実行されている SLES 12 SP3 での SAP HANA システム レプリケーション (HSR) と Pacemaker に基づく複雑な SAP HANA スケールアウトの高可用性構成の確認とトラブルシューティングのガイド
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: b794b045efa4be20a63e9996425d69f0212ae0d7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707235"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>SLES 12 SP3 での SAP HANA スケールアウトの高可用性セットアップの確認とトラブルシューティング 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


この記事は、Azure の仮想マシン (VM) で実行されている SAP HANA スケールアウトの Pacemaker クラスター構成を確認するのに役立ちます。 クラスターは、SAP HANA システム レプリケーション (HSR) および SUSE RPM パッケージ SAPHanaSR-ScaleOut と組み合わせてセットアップされました。 すべてのテストは SUSE SLES 12 SP3 でのみ行われました。 この記事は、さまざまな領域を扱うセクションで構成されており、コマンド例および構成ファイルからの抜粋が含まれます。 これらの例を使用して、クラスターのセットアップ全体を確認することをお勧めします。



## <a name="important-notes"></a>重要

SAP HANA システム レプリケーションおよび Pacemaker と組み合わされた SAP HANA スケールアウトに対するすべてのテストは、SAP HANA 2.0 でのみ行われました。 オペレーティング システムのバージョンは SUSE Linux Enterprise Server 12 SP3 for SAP アプリケーションでした。 Pacemaker クラスターのセットアップには SUSE の最新の RPM パッケージ SAPHanaSR-ScaleOut が使用されました。
SUSE では、[このパフォーマンス最適化セットアップの詳細な説明][sles-hana-scale-out-ha-paper]が公開されています。

SAP HANA スケールアウトでサポートされている仮想マシンの種類については、[SAP HANA 認定 IaaS ディレクトリ][sap-hana-iaas-list]に関するページを参照してください。

複数のサブネットおよび vNIC と組み合わされた SAP HANA スケールアウト、および HSR のセットアップでは、技術的な問題が発生していました。 この問題が修正されている最新の SAP HANA 2.0 修正プログラムを使用する必要があります。 サポートされている SAP HANA のバージョンは次のとおりです。 

* rev2.00.024.04 以降 
* rev2.00.032 以降

SUSE からのサポートが必要な場合は、この[ガイド][suse-pacemaker-support-log-files]に従ってください。 記事の説明に従って、SAP HANA 高可用性 (HA) クラスターに関するすべての情報を収集します。 SUSE サポートでは、さらに詳しい分析を行うためにこの情報が必要です。

内部テスト中、Azure portal を介した通常の正常な VM シャットダウンにより、クラスターのセットアップが混乱しました。 そのため、他の方法でクラスターのフェールオーバーをテストすることをお勧めします。 カーネル パニックの強制やネットワークのシャットダウンなどの方法を使用するか、または **msl** リソースを移行します。 次のセクションで詳細を確認してください。 標準のシャットダウンが意図的に発生することが前提となります。 意図的なシャットダウンの最適な例は、メンテナンスです。 詳細は、「[Azure の計画メンテナンス](#planned-maintenance)」を参照してください。

また、内部テスト中、クラスターがメンテナンス モードの間に手動で SAP HANA の引き継ぎが行われた後、クラスターのセットアップが混乱しました。 クラスターのメンテナンス モードを終了する前に、もう一度手動で切り替えて戻すことをお勧めします。 もう 1 つのオプションは、クラスターをメンテナンス モードにする前に、フェールオーバーをトリガーすることです。 詳細については、「[Azure の計画メンテナンス](#planned-maintenance)」を参照してください。 SUSE のドキュメントでは、**crm** コマンドを使用して、この方法でクラスターをリセットする方法について説明されています。 ただし、前述のアプローチは内部テスト中は堅牢で、予期しない副作用は何も発生しませんでした。

**crm migrate** コマンドを使用する場合は、必ずクラスター構成をクリーンアップしてください。 このコマンドによって場所の制約が追加されますが、これは認識できない可能性があります。 これらの制約は、クラスターの動作に影響します。 詳細は、「[Azure の計画メンテナンス](#planned-maintenance)」を参照してください。



## <a name="test-system-description"></a>テスト システムの説明

 SAP HANA スケール アウトの HA 検証と証明書には、セットアップが使用されていました。 これは、それぞれ 3 つの SAP HANA ノード (1 つのマスターと 2 つのワーカー) を持つ 2 つのシステムで構成されていました。 次の表には、VM 名と内部 IP アドレスが一覧表示されています。 以降の確認の例は、すべてこれらの VM で実行されました。 コマンド例の中でこれらの VM 名と IP アドレスを使用すると、コマンドとその出力をより深く理解できます。


| ノードの種類 | VM 名 | IP アドレス |
| --- | --- | --- |
| サイト 1 のマスター ノード | hso-hana-vm-s1-0 | 10.0.0.30 |
| サイト 1 のワーカー ノード 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| サイト 1 のワーカー ノード 2 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| サイト 2 のマスター ノード | hso-hana-vm-s2-0 | 10.0.0.40 |
| サイト 2 のワーカー ノード 1 | hso-hana-vm-s2-1 | 10.0.0.41 |
| サイト 2 のワーカー ノード 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| マジョリティ メーカー ノード | hso-hana-dm | 10.0.0.13 |
| SBD デバイス サーバー | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS サーバー 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS サーバー 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>複数のサブネットおよび vNIC

SAP HANA ネットワークの推奨事項に従って、1 つの Azure 仮想ネットワーク内に 3 つのサブネットが作成されました。 Azure 上の SAP HANA スケールアウトは、非共有モードでインストールする必要があります。 つまり、すべてのノードで **/hana/data** と **/hana/log** に対してローカル ディスク ボリュームが使用されます。 ノードではローカル ディスク ボリュームのみが使用されるため、ストレージ用に別のサブネットを定義する必要はありません。

- 10.0.2.0/24: SAP HANA ノード間通信
- 10.0.1.0/24: SAP HANA システム レプリケーション (HSR)
- 10.0.0.0/24: その他すべて

複数のネットワークの使用に関連する SAP HANA 構成については、「[SAP HANA global.ini](#sap-hana-globalini)」を参照してください。

クラスター内のすべての VM には、サブネットの数に対応する 3 つの vNIC があります。 「[複数のネットワーク インターフェイス カードを使用して Linux 仮想マシンを Azure に作成する方法][azure-linux-multiple-nics]」describes a potential routing issue on Azure when deploying a Linux VM. This specific routing article applies only for use of multiple vNICs. The problem is solved by SUSE per default in SLES 12 SP3. For more information, see [Multi-NIC with cloud-netconfig in EC2 and Azure][suse-cloud-netconfig]。


複数のネットワークを使用するように SAP HANA が正しく構成されていることを確認するには、次のコマンドを実行します。 最初に、3 つのすべてのサブネットに対する 3 つのすべての内部 IP アドレスがアクティブであることを、OS レベルで確認します。 別の IP アドレス範囲でサブネットを定義した場合は、コマンドを調整する必要があります。

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

以下は、サイト 2 の 2 番目のワーカー ノードからの出力例です。 eth0、eth1、eth2 からの 3 つの異なる内部 IP アドレスを確認できます。

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


次に、ネーム サーバーと HSR の SAP HANA ポートを確認します。 SAP HANA は、対応するサブネットでリッスンする必要があります。 SAP HANA インスタンス番号によっては、コマンドを調整する必要があります。 テスト システムでは、インスタンス番号は **00** でした。 使用されているポートを確認するには、さまざまな方法があります。 

次の SQL ステートメントでは、インスタンス ID、インスタンス番号、およびその他の情報が返されます。

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

正しいポート番号を確認するには、たとえば HANA Studio の **[Configuration]\(構成\)** で、または SQL ステートメントを使用して参照できます。

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

SAP HANA などの SAP ソフトウェア スタックで使用されているすべてのポートを確認するには、[すべての SAP 製品の TCP/IP ポート][sap-list-port-numbers]を検索します。

SAP HANA 2.0 テスト システムのインスタンス数が **00** なので、ネーム サーバーのポート番号は **30001** です。 HSR メタデータ通信のポート番号は **40002** です。 1 つのオプションとしては、ワーカー ノードにサインインし、マスター ノードのサービスを確認します。 この記事では、サイト 2 のマスター ノードに接続しようとしているサイト 2 のワーカー ノード 2 で確認しました。

ネーム サーバー ポートを確認します。

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

ノード間通信でサブネット **10.0.2.0/24** が使用されていることを証明するには、結果が次の出力例のようになるはずです。
サブネット **10.0.2.0/24** 経由の接続のみが成功します。

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

ここで、HSR ポート **40002** を確認します。

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

HSR 通信でサブネット **10.0.1.0/24** が使用されていることを証明するには、結果が次の出力例のようになるはずです。
サブネット **10.0.1.0/24** 経由の接続のみが成功します。

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


**corosync** 構成ファイルは、マジョリティ メーカー ノードを含むクラスター内のすべてのノードで正しい必要があります。 ノードのクラスター結合が想定どおりに動作しない場合は、すべてのノードに **/etc/corosync/corosync.conf** を手動で作成またはコピーし、サービスを再起動します。 

テスト システムからの **corosync.conf** の内容は、一例です。

[クラスターのインストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation)に関するセクションの手順 11 で説明されているように、最初のセクションは **totem** です。 **mcastaddr** の値は無視できます。 既存のエントリを保持します。 **token** と **consensus** のエントリは、[Microsoft Azure SAP HANA のドキュメント][sles-pacemaker-ha-guide]に従って設定する必要があります。

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

2 番目のセクション **logging** は、特定の既定値から変更されていません。

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

3 番目のセクションは **nodelist** を示しています。 クラスターのすべてのノードは、その **nodeid** と共に表示される必要があります。

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

最後のセクション **quorum** では、**expected_votes** の値を正しく設定することが重要です。 この値は、マジョリティ メーカー ノードを含むノードの数である必要があります。 また、**two_node** の値は **0** である必要があります。 エントリを完全に削除しないでください。 値を **0** に設定するだけです。

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


**systemctl** を使用してサービスを再起動します。

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD デバイス

Azure VM で SBD デバイスをセットアップする方法については、[SBD フェンス](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)のセクションで説明されています。

最初に、クラスター内のすべてのノードに ACL エントリがあるかどうか、SBD サーバー VM を確認します。 SBD サーバー VM で次のコマンドを実行します。


<pre><code>
targetcli ls
</code></pre>


テスト システムでは、コマンドの出力は次の例のようになります。 **iqn.2006-04.hso-db-0.local:hso-db-0** などの ACL 名を、VM 上の対応するイニシエーター名として入力する必要があります。 VM ごとに異なる名前が必要です。

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

次に、すべての VM 上のイニシエーター名が異なり、上記のエントリに対応していることを確認します。 サイト 1 のワーカー ノード 1 の例を次に示します。

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

出力は次の例のようになります。

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

次に、**discovery** が正しく動作するかどうかを確認します。 SBD サーバー VM の IP アドレスを使用して、すべてのクラスター ノードで次のコマンドを実行します。

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

出力は次の例のようになります。

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

次のプルーフ ポイントでは、ノードで SDB デバイスが認識されていることを確認します。 この確認は、マジョリティ メーカー ノードを含むすべてのノードで行います。

<pre><code>
lsscsi | grep dbhso
</code></pre>

出力は次の例のようになります。 ただし、名前が異なる場合があります。 VM の再起動後にデバイス名も変化することがあります。

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

システムの状態によっては、iSCSI サービスを再起動すると問題の解決に役立つ場合があります。 次のコマンドを実行します。

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


任意のノードから、すべてのノードが **clear** であるかどうかを確認できます。 特定のノードで正しいデバイス名を使用していることを確認します。

<pre><code>
sbd -d /dev/sdm list
</code></pre>

クラスター内のすべてのノードについて、出力に **clear** と表示されるはずです。

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


SBD の別のチェックでは、**sbd** コマンドの **dump** オプションを使用します。 このコマンド例と、マジョリティ メーカー ノードからの出力では、デバイス名は **sdm** ではなく **sdd** です。

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

デバイス名以外の出力は、すべてのノードで同じになるはずです。

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

SBD のもう 1 つのチェックとして、別のノードにメッセージを送信できるかどうかを確認します。 サイト 2 のワーカー ノード 2 にメッセージを送信するには、サイト 2 のワーカー ノード 1 で次のコマンドを実行します。

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

ターゲット VM 側 (この例では **hso-hana-vm-s2-2**) の **/var/log/messages** に、次のエントリがあります。

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

**/etc/sysconfig/sbd** のエントリが、「[Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)」(Azure で SUSE Linux Enterprise Server に Pacemaker を設定する) の説明に対応しているかどうかを確認します。 **/etc/iscsi/iscsid.conf** のスタートアップ設定が自動に設定されていることを確認します。

**/etc/sysconfig/sbd** では、次のエントリが重要です。 必要に応じて、**id** 値を調整します。

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


**/etc/iscsi/iscsid.conf** でスタートアップ設定を確認します。 ドキュメントで説明されているように、次の **iscsiadm** コマンドによって、必要な設定が行われます。 その設定が異なっている場合は、**vi** を使用して手動で設定を確認して調整します。

このコマンドは、スタートアップ動作を設定します。

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

**/etc/iscsi/iscsid.conf** にこのエントリを作成します。

<pre><code>
node.startup = automatic
</code></pre>

テストおよび検証中に、VM の再起動後に SBD デバイスが表示されなくなる場合がありました。 スタートアップ設定と、YaST2 によって表示される内容との間に相違が見られました。 この設定を確認するには、次の手順を行います。

1. YaST2 を起動します。
2. 左側にある **[Network Services]\(ネットワーク サービス\)** を選択します。
3. 右側で **[iSCSI Initiator]\(iSCSI イニシエーター\)** までスクロールして選択します。
4. 次の画面の **[Service]\(サービス\)** タブに、ノードの一意のイニシエーター名が表示されます。
5. イニシエーター名の上にある **[Service Start]\(サービスの開始\)** 値が **[When Booting]\(起動時\)** に設定されていることを確認します。
6. そうでない場合は、 **[Manually]\(手動\)** の代わりに **[When Booting]\(起動時\)** に設定します。
7. 次に、上部のタブを **[Connected Targets]\(接続済みのターゲット\)** に切り替えます。
8. **[Connected Targets]\(接続済みのターゲット)** 画面に、次の例のような SBD デバイスのエントリが表示されます:**10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**。
9. **Start-Up** 値が **on boot** に設定されているかどうかを確認します。
10. そうでない場合は、 **[Edit]\(編集\)** を選択して変更します。
11. 変更内容を保存し、YaST2 を終了します。



## <a name="pacemaker"></a>Pacemaker

すべてのセットアップが正しく行われたら、すべてのノードで次のコマンドを実行して、Pacemaker サービスの状態を確認できます。

<pre><code>
systemctl status pacemaker
</code></pre>

出力の先頭は次の例のようになります。 **Active** の後の状態は **loaded** および **active (running)** と表示される必要があります。 **Loaded** の後の状態は **enabled** と表示される必要があります。

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

設定が **disabled** のままの場合は、次のコマンドを実行します。

<pre><code>
systemctl enable pacemaker
</code></pre>

Pacemaker 内の構成済みのすべてのリソースを表示するには、次のコマンドを実行します。

<pre><code>
crm status
</code></pre>

出力は次の例のようになります。 マジョリティ メーカー VM (**hso-hana-dm**) で **cln** と **msl** のリソースが stopped と表示されていても問題ありません。 マジョリティ メーカー ノードには SAP HANA インストールがありません。 そのため、**cln** と **msl** のリソースは stopped と表示されています。 VM の正確な総数 (**7**) が示される必要があります。 クラスターの一部であるすべての VM が、**Online** の状態で一覧表示される必要があります。 現在のプライマリ マスター ノードが正しく認識される必要があります。 この例では、**hso-hana-vm-s1-0** です。

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Pacemaker の重要な機能は、メンテナンス モードです。 このモードでは、即時にクラスター アクションを誘発することなく変更を加えることができます。 VM の再起動は一例です。 一般的なユース ケースは、OS または Azure インフラストラクチャの計画済みメンテナンスです。 「[Azure の計画メンテナンス](#planned-maintenance)」を参照してください。 Pacemaker をメンテナンス モードにするには、次のコマンドを使用します。

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

**crm status** を確認すると、出力内ですべてのリソースが **unmanaged** とマークされていることがわかります。 この状態では、クラスターは SAP HANA の開始や停止などのいずれの変更にも反応しません。
クラスターがメンテナンス モード中の **crm status** コマンドの出力例を次に示します。

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


このコマンド例では、クラスターのメンテナンス モードを終了する方法を示しています。

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


もう 1 つの **crm** コマンドは、エディターにクラスター構成全体を取り込んで編集できるようにします。 変更を保存したら、クラスターで適切なアクションが開始されます。

<pre><code>
crm configure edit
</code></pre>

クラスター構成全体を確認するには、**crm show** オプションを使用します。

<pre><code>
crm configure show
</code></pre>



クラスター リソースのエラーが発生すると、**crm status** コマンドによって **Failed Actions** の一覧が表示されます。 この出力例を以下に示します。


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

エラーの発生後は、クラスターのクリーンアップを実行する必要があります。 **crm** コマンドを再度使用し、コマンド オプション **cleanup** を使用して、エラーが発生したこれらのアクション エントリを除去します。 次のように、対応するクラスター リソースに名前を付けます。

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

コマンドで返される出力は次の例のようになります。

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>フェールオーバーまたは引き継ぎ

「[重要](#important-notes)」のセクションで説明されているように、通常の正常なシャットダウンを使用してクラスターのフェールオーバーまたは SAP HANA HSR の引き継ぎをテストすることはできません。 代わりに、カーネル パニックなどをトリガーするか、リソースの移行を強制するか、または VM の OS レベルですべてのネットワークをシャットダウンすることをお勧めします。 もう 1 つの方法は、**crm \<node\> standby** コマンドを使用することです。 [SUSE のドキュメント][sles-12-ha-paper]を参照してください。 

次の 3 つのサンプル コマンドは、クラスターのフェールオーバーを強制できます。

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

「[Azure の計画メンテナンス](#planned-maintenance)」セクションで説明されているように、クラスター アクティビティを監視するには、**watch** コマンドと共に **SAPHanaSR-showAttr** を実行することをお勧めします。

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

これは、SAP Python スクリプトからの SAP HANA ランドスケープの状態を確認するのにも役立ちます。 クラスターのセットアップでは、この状態値が求められます。 このことは、ワーカー ノードのエラーについて考えるとよくわかります。 ワーカー ノードがダウンした場合、SAP HANA では、スケールアウト システム全体の正常性のエラーはすぐに返されません。 

不要なフェールオーバーを回避するために、数回の再試行が行われます。 クラスターは、状態が**Ok** (戻り値 **4**) から**error** (戻り値 **1**) に変化した場合にのみ反応します。 したがって、**SAPHanaSR showAttr** からの出力に **offline** 状態の VM が示されている場合は、正常な動作です。 ただし、プライマリとセカンダリを切り替えるアクティビティがまだ発生していません。 SAP HANA によってエラーが返されない限り、クラスター アクティビティはトリガーされません。

次のように、SAP Python スクリプトを呼び出すことで、ユーザー **\<HANA SID\>adm** として SAP HANA ランドスケープの正常性状態を監視することができます。 場合によっては、パスを調整する必要があります。

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

このコマンドの出力は、次の例のようになります。 **Host Status** 列と **overall host status** はどちらも重要です。 実際の出力は、さらに列が表示されて幅広になっています。
このドキュメントでは、出力テーブルを見やすくするために、右側にあるほとんどの列を削除しています。

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


現在のクラスター アクティビティを確認するもう 1 つのコマンドがあります。 次のコマンドと、プライマリ サイトのマスター ノードが強制終了された後の出力の末尾をご覧ください。 前のセカンダリ マスター ノード (**hso-hana-vm-s2-0**) を新しいプライマリ マスターとして**昇格**させるなど、遷移アクションの一覧が表示されています。 何も問題がなく、すべてのアクティビティが終了したら、**Transition Summary** の一覧は空になります。

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Azure の計画メンテナンス 

計画メンテナンスについては、さまざまなユース ケースがあります。 問題は、それが OS レベルでの変更やディスク構成などのインフラストラクチャのメンテナンスだけなのか、または HANA アップグレードなのかということです。
追加情報については、[「Towards Zero Downtime」(ゼロ ダウンタイムに向けて)][sles-zero-downtime-paper] or [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]など、SUSE のドキュメントを参照してください。 これらのドキュメントには、プライマリを手動で移行する方法を示す例も記載されています。

インフラストラクチャのメンテナンスのユース ケースを確認するために、集中的な内部テストが行われました。 プライマリの移行に関連するあらゆる問題を回避するために、クラスターをメンテナンス モードにする前に、必ずプライマリを移行することを決定しました。 このようにすると、クラスターに前の状況 (どちらの側がプライマリで、どちら側がセカンダリであったか) を忘れさせる必要がなくなります。

この点については、次の 2 つの異なる状況があります。

- **現在のセカンダリでの計画メンテナンス**。 この場合は、クラスターをメンテナンス モードにし、クラスターに影響を与えることなくセカンダリで作業を行います。

- **現在のプライマリでの計画メンテナンス**。 メンテナンス中にユーザーが作業を続けられるように、強制フェールオーバーを実行する必要があります。 このアプローチでは、SAP HANA HSR レベルだけでなく、Pacemaker によってクラスターのフェールオーバーをトリガーする必要があります。 Pacemaker のセットアップにより、SAP HANA の引き継ぎが自動的にトリガーされます。 また、クラスターをメンテナンス モードにする前にフェールオーバーを実行する必要があります。

現在のセカンダリ サイトでのメンテナンス手順は、次のようになります。

1. クラスターをメンテナンス モードにします。
2. セカンダリ サイトで作業を行います。 
3. クラスターのメンテナンス モードを終了します。

現在のプライマリ サイトでのメンテナンス手順は、より複雑です。

1. Pacemaker リソースの移行を使用して、手動でフェールオーバーまたは SAP HANA の引き継ぎをトリガーします。 以下の詳細を参照してください。
2. クラスターのセットアップにより、前のプライマリ サイト上の SAP HANA がシャットダウンされます。
3. クラスターをメンテナンス モードにします。
4. メンテナンス作業が完了したら、前のプライマリを新しいセカンダリ サイトとして登録します。
5. クラスター構成をクリーンアップします。 以下の詳細を参照してください。
6. クラスターのメンテナンス モードを終了します。


リソースを移行すると、クラスター構成にエントリが追加されます。 フェールオーバーの強制はその一例です。 メンテナンス モードを終了する前に、これらのエントリをクリーンアップする必要があります。 次の例を参照してください。

最初に、**msl** リソースを現在のセカンダリ マスター ノードに移行することで、クラスターのフェールオーバーを強制します。 このコマンドにより、**移動制約**が作成されたという警告が示されます。

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


コマンド **SAPHanaSR-showAttr** を使用して、フェールオーバー プロセスを確認します。 クラスターの状態を監視するには、専用シェル ウィンドウを開き、**watch** を使用してコマンドを開始します。

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

出力には手動フェールオーバーが示されます。 前のセカンダリ マスター ノードが**昇格**しました (この例では **hso-hana-vm-s2-0**)。 前のプライマリ サイトが停止されました (前のプライマリ マスター ノード **hso-hana-vm-s1-0** の **lss** 値 **1**)。 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

「[Pacemaker](#pacemaker)」で説明されているように、クラスターのフェールオーバーと SAP HANA の引き継ぎが終了したら、クラスターをメンテナンス モードにします。

コマンド **SAPHanaSR-showAttr** および **crm status** は、リソースの移行によって作成された制約については何も示しません。 これらの制約を表示する 1 つのオプションとしては、次のコマンドを使用して、完全なクラスター リソース構成を表示します。

<pre><code>
crm configure show
</code></pre>

クラスター構成内で、以前の手動によるリソースの移行に起因する新しい場所の制約を見つけます。 このエントリ例は、**location cli-** で始まります。

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

残念ながらこのような制約は、クラスターの全体的な動作に影響を与える可能性があります。 そのため、システム全体をバックアップする前に、これらの制約をもう一度削除する必要があります。 **unmigrate** コマンドを使用すると、前に作成された場所の制約をクリーンアップできます。 このコマンドの名前は少し誤解されやすいかもしれません。 これは、リソースをその移行前の元の VM に移行し直そうとはしません。 コマンドを実行すると、場所の制約が削除され、対応する情報が返されるだけです。


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

「[Pacemaker](#pacemaker)」で説明されているように、メンテナンス作業の最後に、クラスターのメンテナンス モードを停止します。



## <a name="hbreport-to-collect-log-files"></a>ログ ファイルを収集する hb_report

Pacemaker クラスターの問題を分析するには、**hb_report** ユーティリティの実行が有用であり、また SUSE サポートで要求されています。 何が起こったかを分析する必要があるすべての重要なログ ファイルを収集します。 このサンプルの呼び出しでは、特定のインシデントが発生した開始時間と終了時間を使用します。 「[重要](#important-notes)」も参照してください。

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

このコマンドにより、圧縮されたログ ファイルが格納される場所が示されます。

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

その後、標準の **tar** コマンドを使用して、個々のファイルを抽出できます。

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

抽出したファイルを確認すると、すべてのログ ファイルが見つかります。 これらのファイルのほとんどは、クラスター内のノードごとに別々のディレクトリに格納されています。

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


指定された時間範囲内に、現在のマスター ノード **hso-hana-vm-s1-0** が強制終了されました。 **journal.log** 内で、このイベントに関連するエントリを見つけることができます。

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

もう 1 つの例として、セカンダリ マスター上の Pacemaker ログ ファイルが、新しいプライマリ マスターになりました。 次の引用部分は、強制終了されたプライマリ マスター ノードの状態が **offline** に設定されたことを示しています。

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


以下は、クラスター サイト 2 上の SAP HANA **global.ini** ファイルからの抜粋です。 この例は、SAP HANA ノード間通信と HSR 用に異なるネットワークを使用するためのホスト名解決エントリを示しています。

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

クラスター ソリューションでは、シェル レベルですべてのコマンドを持つよりもメニューやグラフィックスを好むユーザー向けに、GUI を提供するブラウザー インターフェイスが用意されています。
ブラウザー インターフェイスを使用するには、次の URL の **\<node\>** を実際の SAP HANA ノードに置き換えます。 その後、クラスターの資格情報を入力します (ユーザー **cluster**)。

<pre><code>
https://&ltnode&gt:7630
</code></pre>

次のスクリーンショットは、クラスター ダッシュボードを示しています。


![Hawk クラスター ダッシュボード](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


この例では、「[Azure の計画メンテナンス](#planned-maintenance)」で説明されているように、クラスター リソースの移行に起因する場所の制約を示しています。


![Hawk で制約を一覧表示する](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


次のように、 **[History]\(履歴\)** の下の Hawk で **hb_report** 出力をアップロードすることもできます。 「ログ ファイルを収集する hb_report」を参照してください。 

![Hawk で hb_report 出力をアップロードする](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

その後、**履歴エクスプローラー**を使用して、**hb_report** 出力に含まれるクラスターのすべての遷移を確認できます。

![hb_report 出力内の Hawk 遷移](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

この最後のスクリーンショットは、1 つの遷移の **[詳細]** セクションを示しています。 クラスターがプライマリ マスター ノードのクラッシュに反応しました (ノード **hso-hana-vm-s1-0**)。 セカンダリ ノードが新しいマスターとして昇格しています (**hso-hana-vm-s2-0**)。

![Hawk の 1 つの遷移](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>次の手順

このトラブルシューティング ガイドでは、スケールアウト構成での SAP HANA の高可用性について説明しました。 データベースに加え、SAP ランドスケープ内のもう 1 つの重要なコンポーネントは、SAP NetWeaver スタックです。 [SUSE Enterprise Linux Server を使用した Azure の仮想マシン上の SAP NetWeaver の高可用性][sap-nw-ha-guide-sles]について学習します。

