---
title: SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性 | Microsoft Docs
description: SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 93644b9a3487906a27db70bfe82cceccdc7ab45c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707220"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

この記事では、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、可用性の高い SAP システムの共有データを格納するために使用できる高可用性 NFS サーバーのインストールの方法について説明します。
このガイドでは、NW1 と NW2 の 2 つの SAP システムで使用される高可用性 NFS サーバーを設定する方法について説明します。 この例のリソース (仮想マシン、仮想ネットワークなど) の名前は、リソース プレフィックスが **prod** の [SAP ファイル サーバー テンプレート][template-file-server]を使用していることを想定しています。

はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2205917]: SUSE Linux Enterprise Server for SAP Applications 向けの推奨の OS 設定が記載されています。
* SAP Note [1944799]: SUSE Linux Enterprise Server for SAP Applications の SAP HANA ガイドラインが記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SUSE Linux Enterprise High Availability Extension 12 SP3 のベスト プラクティス ガイド][sles-hae-guides]
  * Highly Available NFS Storage with DRBD and Pacemaker (DRBD と Pacemaker を使用した高可用性 NFS ストレージ)
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 のベスト プラクティス ガイド][sles-for-sap-bp]
* [SUSE High Availability Extension 12 SP3 リリース ノート][suse-ha-12sp3-relnotes]

## <a name="overview"></a>概要

高可用性を実現するため、SAP NetWeaver には NFS サーバーが必要です。 NFS サーバーは別のクラスターで構成されており、複数の SAP システムが使用できます。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-nfs/ha-suse-nfs.png)

NFS サーバーは、この NFS サーバーを使用するすべての SAP システムに専用の仮想ホスト名と仮想 IP アドレスを使用します。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 ロード バランサーの構成を次に示します。        

* フロントエンドの構成
  * IP アドレス 10.0.0.4 (NW1)
  * IP アドレス 10.0.0.5 (NW2)
* バックエンドの構成
  * NFS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 61000 (NW1)
  * ポート 61001 (NW2)
* 負荷分散規則
  * 2049 TCP (NW1)
  * 2049 UDP (NW1)
  * 2049 TCP (NW2)
  * 2049 UDP (NW2)

## <a name="set-up-a-highly-available-nfs-server"></a>高可用性 NFS サーバーの設定

GitHub にある Azure テンプレートを使用して、仮想マシン、可用性セット、ロード バランサーなどの必要なすべての Azure リソースをデプロイできます。また、各リソースを手動でデプロイすることもできます。

### <a name="deploy-linux-via-azure-template"></a>Azure テンプレートを使用した Linux のデプロイ

Azure Marketplace には、SUSE Linux Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい仮想マシンのデプロイに使用できます。
GitHub にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイできます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。テンプレートをデプロイするには、次の手順に従います。

1. Azure portal で [SAP ファイル サーバー テンプレート][template-file-server]を開きます   
1. 次のパラメーターを入力します
   1. Resource Prefix (リソース プレフィックス)  
      使用するプレフィックスを入力します。 この値は、デプロイされるリソースのプレフィックスとして使用されます。
   2. SAP System Count (SAP システム数)  
      このファイル サーバーを使用する SAP システムの数を入力します。 これにより、必要な量のフロントエンド構成、負荷分散規則、プローブ ポート、ディスクなどがデプロイされます。
   3. OS の種類  
      いずれかの Linux ディストリビューションを選択します。 この例では、SLES 12 を選択します。
   4. [管理ユーザー名] と[管理パスワード]  
      コンピューターへのログオンで使用できる新しいユーザーが作成されます。
   5. サブネット ID  
      VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、この ID は、/subscriptions/ **&lt;サブスクリプション ID&gt;** /resourceGroups/ **&lt;リソース グループ名&gt;** /providers/Microsoft.Network/virtualNetworks/ **&lt;仮想ネットワーク名&gt;** /subnets/ **&lt;サブネット名&gt;** のようになります。

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal を使用した手動による Linux のデプロイ

まず、この NFS クラスターの仮想マシンを作成する必要があります。 その後、ロード バランサーを作成し、バックエンド プール内の仮想マシンを使用します。

1. リソース グループを作成します
1. 仮想ネットワークを作成します
1. 可用性セットを作成します  
   更新ドメインの最大数を設定します
1. 仮想マシン 1 を作成します。SLES4SAP 12 SP3 以上を使用してください。この例では、SLES4SAP 12 SP3 BYOS イメージ SLES For SAP Applications 12 SP3 (BYOS) が使用されています  
   前に作成された可用性セットを選択します  
1. 仮想マシン 2 を作成します。SLES4SAP 12 SP3 以上を使用してください。この例では、SLES4SAP 12 SP3 BYOS イメージを使用します  
   SLES For SAP Applications 12 SP3 (BYOS) が使用されています  
   前に作成された可用性セットを選択します  
1. SAP システムごとに 1 つのデータ ディスクを両方の仮想マシンに追加します。
1. ロード バランサー (内部) を作成します  
   1. フロントエンド IP アドレスを作成します
      1. IP アドレス 10.0.0.4 (NW1)
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **nw1-frontend**)
         1. 割り当てを "静的" に設定し、IP アドレスを入力します (例: **10.0.0.4**)
         1. [OK] をクリックします
      1. IP アドレス 10.0.0.5 (NW2)
         * NW2 に対して上記の手順を繰り返します
   1. バックエンド プールを作成します
      1. NFS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み (NW1)
         1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
         1. 新規のバックエンド プールの名前を入力します (例: **nw1-backend**)
         1. [仮想マシンの追加] をクリックします
         1. 前の手順で作成した可用性セットを選択します
         1. NFS クラスターの仮想マシンを選択します
         1. [OK] をクリックします
      1. NFS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み (NW2)
         * 上記の手順を繰り返して、NW2 に対してバックエンド プールを作成します
   1. 正常性プローブを作成します
      1. ポート 61000 (NW1)
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **nw1-hp**)
         1. プロトコルに TCP、ポートに 610**00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ポート 61001 (NW2)
         * 上記の手順を繰り返して、NW2 に対して正常性プローブを作成します
   1. 負荷分散規則
      1. 2049 TCP (NW1)
         1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **nw1-lb-2049**)
         1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **nw1-frontend**)
         1. プロトコルは **TCP** のままにし、ポートに「**2049**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
      1. 2049 UDP (NW1)
         * 上記の手順を繰り返して、NW1 に対してポート 2049 と UDP を設定します
      1. 2049 TCP (NW2)
         * 上記の手順を繰り返して、NW2 に対してポート 2049 と TCP を設定します
      1. 2049 UDP (NW2)
         * 上記の手順を繰り返して、NW2 に対してポート 2049 と UDP を設定します

> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください。

### <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](high-availability-guide-suse-pacemaker.md)」の手順に従って、この NFS サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="configure-nfs-server"></a>NFS サーバーの構成

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS サーバーを有効にします

   ルートの NFS エクスポート エントリを作成します

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** drbd コンポーネントをインストールします

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** drbd デバイス用のパーティションを作成します

   使用可能なすべてのデータ ディスクを表示します

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   出力例
   
   ```
   lun0  lun1
   ```

   すべてのデータ ディスクのパーティションを作成します

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM 構成を作成します

   使用可能なすべてのパーティションを表示します

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   出力例
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   すべてのパーティションに LVM ボリュームを作成します

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** drbd を構成します

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   drbd.conf ファイルに次の 2 行が含まれていることを確認します

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   グローバルな drbd 構成を変更します

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   ハンドラーと net セクションに次のエントリを追加します。

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** NFS drbd デバイスを作成します

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   新しい drbd デバイスの構成を挿入し、終了します

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   新しい drbd デバイスの構成を挿入し、終了します

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   drbd デバイスを作成し、起動します

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** 初期同期をスキップします

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** プライマリ ノードを設定します

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** 新しい drbd デバイスが同期されるまで待ちます

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** drbd デバイス上にファイル システムを作成します

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** drbd スプリット ブレイン検出を設定します

   drbd を使用してあるホストから別のホストにデータを同期するときに、スプリット ブレインと呼ばれる状況が発生することがあります。 スプリット ブレインは、両方のクラスター ノードの drbd デバイスがプライマリに昇格され、非同期になるシナリオです。これはまれな状況かもしれませんが、スプリット ブレインをできるだけ早く処理して解決する必要があります。 したがって、スプリット ブレインが発生したときに通知を受け取ることが重要です。

   スプリット ブレインの通知を設定する方法については、[drbd の公式ドキュメント](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification)を参照してください。

   さらに、スプリット ブレイン シナリオから自動的に復旧することも可能です。 詳細については、「[Automatic split brain recovery policies (自動スプリット ブレイン復旧ポリシー)](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)」を参照してください
   
### <a name="configure-cluster-framework"></a>クラスター フレームワークの構成

1. **[1]** SAP システム NW1 の NFS drbd デバイスをクラスター構成に追加します

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** SAP システム NW2 の NFS drbd デバイスをクラスター構成に追加します

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** メンテナンス モードを無効にします
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>次の手順

* [SAP ASCS とデータベースのインストール](high-availability-guide-suse.md)
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]に関するページを参照してください。
