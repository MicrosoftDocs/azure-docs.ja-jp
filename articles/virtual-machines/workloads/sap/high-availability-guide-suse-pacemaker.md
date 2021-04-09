---
title: Azure での SLES に対する Pacemaker の設定 | Microsoft Docs
description: Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: ddee5edcf1d19af0fb088976c590b62866a1484e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674421"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Azure で Pacemaker クラスターをセットアップする場合、2 つの選択肢があります。 1 つはフェンス エージェントを使う方法です。フェンス エージェントは、障害が発生したノードを Azure API で再起動させる役割を担います。もう 1 つは、SBD デバイスを使う方法です。

SBD デバイスを使う場合、iSCSI ターゲット サーバーとして機能しつつ SBD デバイスを提供する仮想マシンが、少なくとも 1 つ、追加で必要となります。 ただし、これらの iSCSI ターゲット サーバーは他の Pacemaker クラスターと共有することができます。 SBD デバイスを使う利点は、SBD デバイスを既にオンプレミスで使用している場合、Pacemaker クラスターの運用方法に変更を加える必要は一切ないことです。 特定の SBD デバイスが利用できなくなる状況 (たとえば、iSCSI ターゲット サーバーの OS 修正プログラムの適用時など) に備えるにあたっては、1 つの Pacemaker クラスターに対し、最大 3 つの SBD デバイスを使用できます。 Pacemaker あたり 2 つ以上の SBD デバイスを使用する場合は、複数の iSCSI ターゲット サーバーをデプロイし、各 iSCSI ターゲット サーバーから 1 つの SBD を接続するようにしてください。 SBD デバイスは、1 つまたは 3 つ使用することをお勧めします。 SBD デバイスを 2 つだけ構成し、それらのいずれかが利用できなくなった場合、Pacemaker では、クラスター ノードを自動的にフェンシングすることができません。 1 つの iSCSI ターゲット サーバーがダウンした場合にフェンシングを行えるようにするには、3 つの SBD デバイスと、3 つの iSCSI ターゲット サーバーを使用する必要があります。これは、SBD を使用しているときに最も回復性がある構成です。

Azure Fence エージェントでは、追加の仮想マシンをデプロイする必要はありません。   

![SLES における Pacemaker の概要](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Linux Pacemaker クラスター ノードおよび SBD デバイスを計画およびデプロイする場合、関係する VM と SBD デバイスをホストする VM が [NVA](https://azure.microsoft.com/solutions/network-appliances/) などのその他のデバイスを通過しないようにすることが、完全なクラスター構成の全体的な信頼性に必要です。 このようにしないと、NVA の問題とメンテナンス イベントがクラスター構成全体の安定性と信頼性に負の影響を与える可能性があります。 このような障害を回避するには、Linux Pacemaker クラスター ノードおよび SBD デバイスを計画およびデプロイする場合に、クラスター化されたノードと NVA や類似のデバイスを介した SBD デバイスなどの間のトラフィックをルーティングする、NVA のルーティング規則または[ユーザー定義のルーティング規則](../../../virtual-network/virtual-networks-udr-overview.md)を定義しないようにする必要があります。 
>

## <a name="sbd-fencing"></a>SBD フェンス

SBD デバイスをフェンスに使用する場合は、次の手順に従ってください。

### <a name="set-up-iscsi-target-servers"></a>iSCSI ターゲット サーバーのセットアップ

まず、iSCSI ターゲット仮想マシンを作成する必要があります。 iSCSI ターゲット サーバーは、複数の Pacemaker クラスターと共有することができます。

1. 新しい SLES 12 SP1 以降の仮想マシンをデプロイし、ssh 経由でそれらに接続します。 サイズの大きいマシンは必要ありません。 Standard_E2s_v3 または Standard_D2s_v3 などの仮想マシン サイズで十分です。 OS ディスクには Premium ストレージを使用してください。

すべての **iSCSI ターゲット仮想マシン** に対し、次のコマンドを実行します。

1. SLES を更新します

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > OS のアップグレード後または更新後に、OS の再起動が必要になる場合があります。 

1. パッケージの削除

   targetcli および SLES 12 SP3 に関する既知の問題を回避するには、次のパッケージをアンインストールします。 見つからないパッケージに関するエラーは無視できます

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. iSCSI ターゲット パッケージをインストールします

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. iSCSI ターゲット サービスを有効にします

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>iSCSI ターゲット サーバーに iSCSI デバイスを作成する

すべての **iSCSI ターゲット仮想マシン** に対して次のコマンドを実行し、SAP システムによって使用されるクラスター用の iSCSI ディスクを作成します。 次の例では、複数のクラスターに対して SBD デバイスが作成されています。 この例では、複数のクラスターに対して 1 つの iSCSI ターゲット サーバーを使用する方法を示しています。 SBD デバイスは OS ディスク上に配置されます。 十分な容量があることを確認してください。

**`nfs`** は、NFS クラスターを識別するために使用されます。**ascsnw1** は **NW1** の ASCS クラスターを識別するために使用され、**dbnw1** は **NW1** のデータベース クラスターを識別するために使用されます。**nfs-0** と **nfs-1** は、NFS クラスター ノードのホスト名で、**nw1-xscs-0** と **nw1-xscs-1** は、**NW1** の ASCS クラスター ノードのホスト名です。**nw1-db-0** と **nw1-db-1** は、データベース クラスター ノードのホスト名です。 これらは、実際のクラスター ノードのホスト名と、実際の SAP システムの SID に置き換えてください。

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

すべてが正しく設定されたかどうかは、次のコマンドで確認できます

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD デバイスをセットアップする

最後の手順で作成した iSCSI デバイスにクラスターから接続します。
以下のコマンドは、作成する新しいクラスターの各ノード上で実行します。
次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** iSCSI デバイスに接続します

   まず、iSCSI サービスと SBD サービスを有効にします。

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 1 つ目のノードでイニシエーターの名前を変更します

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI ターゲット サーバーに iSCSI デバイスを作成したときに使った ACL に合わせて、ファイルの内容を変更します (たとえば、NFS サーバー用に)。

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** 2 つ目のノードでイニシエーターの名前を変更します

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI ターゲット サーバーに iSCSI デバイスを作成したときに使った ACL に合わせてファイルの内容を変更します

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** iSCSI サービスを再起動します

   ここで iSCSI サービスを再起動すると、変更が適用されます

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   iSCSI デバイスを接続します。 下の例の 10.0.0.17 は iSCSI ターゲット サーバーの IP アドレス、3260 は既定のポートです。 <b>iqn.2006-04.nfs.local:nfs</b> は、下記の 1 つ目のコマンド (iscsiadm -m discovery) を実行したときに表示されるターゲット名の 1 つです。

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   iSCSI デバイスが利用可能な状態であることを確認し、デバイス名を書き留めておいてください (次の例では /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   ここで、iSCSI デバイスの ID を取得します。

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   このコマンドを実行すると、SBD デバイスごとに 3 つのデバイス ID が一覧表示されます。 上の例の scsi-3 で始まる ID (下記) の使用をお勧めします

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** SBD デバイスを作成します

   iSCSI デバイスのデバイス ID を使用して、1 つ目のクラスター ノードに新しい SBD デバイスを作成します。

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** SBD の構成を調整します

   SBD の構成ファイルを開きます

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   SBD デバイスのプロパティを変更し、Pacemaker の統合を有効にして、SBD の起動モードを変更します。

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   `softdog` 構成ファイルを作成します

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   モジュールを読み込みます

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>クラスターのインストール

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** SLES を更新します

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** クラスター リソースに必要なコンポーネントをインストールします

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** クラスター リソースに必要な azure-lb コンポーネントをインストールします

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > パッケージ resource-agents のバージョンを確認し、最小バージョン要件が満たされていることを確認します。  
   > - SLES 12 SP4/SP5 の場合、バージョンは resource-agents-4.3.018.a7fb5035-3.30.1 以上である必要があります。  
   > - SLES 15/15 SP1 の場合、バージョンは resource-agents-4.3.0184.6ee15eb2-4.13.1 以上である必要があります。  

1. **[A]** オペレーティング システムを構成します

   場合によっては、Pacemaker によって多数のプロセスが作成され、その結果、プロセスの許容数に達することがあります。 その場合、クラスター ノード間のハートビートが失敗し、リソースのフェールオーバーが発生する可能性があります。 次のパラメーターを設定して、許可されるプロセスの最大数を増やすことをお勧めします。

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   ダーティ キャッシュのサイズを小さくします。 詳しくは、「[Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287)」(大容量 RAM を備えた SLES 11/12 サーバーでの書き込みのパフォーマンスの低さ) をご覧ください。

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** HA クラスター用に cloud-netconfig-azure を構成します

   >[!NOTE]
   > **zypper info cloud-netconfig-azure** を実行して、パッケージ **cloud-netconfig-azure** のインストールされているバージョンを確認します。 環境内のバージョンが 1.3 以降である場合、クラウド ネットワーク プラグインによるネットワーク インターフェイスの管理を抑制する必要はなくなりました。 バージョンが 1.3 より前の場合は、パッケージ **cloud-netconfig-azure** を利用可能な最新バージョンに更新することをお勧めします。  

   クラウド ネットワーク プラグインによって仮想 IP アドレスが削除されるのを防ぐために、次に示すようにネットワーク インターフェイスの構成ファイルを変更します (Pacemaker で VIP の割り当てを制御する必要があります)。 詳細については、[SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633) を参照してください。 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH アクセスを有効にします

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH アクセスを有効にします

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH アクセスを有効にします

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** STONITH デバイスを使用している場合は、Azure Fence エージェントに基づいて、Fence エージェント パッケージをインストールします。  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > クラスター ノードがフェンスされる必要がある場合に、Azure Fence エージェントの短縮されたフェールオーバー時間の恩恵を受けるには、インストールされている **fence-agents** パッケージのバージョンが **4.4.0** 以上である必要があります。 古いバージョンを実行している場合は、パッケージを更新することをお勧めします。  


1. **[A]** Azure Python SDK のインストール 
   - SLES 12 SP4 または SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - SLES 15 以上 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >バージョンとイメージの種類によっては、Azure Python SDK をインストールする前に、使用している OS リリースのパブリック クラウド拡張機能をアクティブにする必要がある場合があります。
   >SUSEConnect ---list-extensions を実行して、拡張機能を確認できます。  
   >Azure Fence エージェントでフェールオーバー時間を短縮するには、次のようにします。
   > - SLES 12 SP4 または SLES 12 SP5 の場合、python-azure-mgmt-compute パッケージのバージョン **4.6.2** 以上をインストールする  
   > - SLES 15 の場合、python **3**-azure-mgmt-compute パッケージのバージョン **4.6.2** 以上をインストールする 

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。

   >[!IMPORTANT]
   > クラスター構成でホスト名を使用する場合は、信頼できるホスト名解決が不可欠です。 名前が使用できず、クラスターのフェールオーバーの遅延につながる可能性がある場合、クラスター通信は失敗します。
   > /etc/hosts を使用する利点は、単一障害点になる可能性もある DNS からクラスターを独立させられることです。  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** クラスターをインストールします
- フェンスに SBD デバイスを使用している場合
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- フェンスに SBD デバイスを "*使用していない*" 場合
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** クラスターにノードを追加します

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster のパスワードを同じパスワードに変更します

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** corosync の設定を調整します。  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   値が無いか、異なる場合は、次の太字の内容をファイルに追加します。 トークンを 30000 に変更してメモリ保持メンテナンスを可能にします。 詳細については、[Linux の場合はこちらの記事][virtual-machines-linux-maintenance]、[Windows の場合はこちらの記事][virtual-machines-windows-maintenance]を参照してください。

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   その後、corosync サービスを再起動します

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD 用の既定の Pacemaker 構成

このセクションの構成は、SBD STONITH を使用している場合にのみ適用されます。  

1. **[1]** STONITH デバイスの使用を有効にし、フェンスの遅延を設定します

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure Fence エージェントの STONITH デバイスの作成

ドキュメントのこのセクションは、Azure Fence エージェントに基づいて STONITH を使用している場合にのみ適用されます。
STONITH デバイスは、サービス プリンシパルを使用して Microsoft Azure を承認します。 サービス プリンシパルを作成するには、次に手順に従います。

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します
1. [Azure Active Directory] ブレードを開きます  
   [プロパティ] に移動し、ディレクトリ ID をメモします。 これは、**テナント ID** です。
1. [アプリの登録] を選択します
1. [New Registration]\(新規登録\) をクリックします
1. 名前を入力し、[Accounts in this organization directory only]\(この組織ディレクトリ内のアカウントのみ\) を選択します 
2. [アプリケーションの種類] は "Web" を選択し、サインオン URL (たとえば http:\//localhost) を入力して、[追加] をクリックします  
   サインオン URL は使用されず、任意の有効な URL を指定することができます
1. [Certificates and Secrets]\(証明書とシークレット\) を選択し、[New client secret]\(新しいクライアント シークレット\) をクリックします
1. 新しいキーの説明を入力し、[Never expires]\(有効期限なし\) を選択して [追加] をクリックします
1. 値をメモします。 この値は、サービス プリンシパルの **パスワード** として使用します
1. [概要] を選択します。 アプリケーション ID をメモします。 これは、サービス プリンシパルのユーザー名 (下記の手順の **ログイン ID**) として使用します

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** フェンス エージェントのカスタム ロールを作成する

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 クラスターのすべての仮想マシンを開始および停止 (割り当て解除) する権限を、サービス プリンシパルに付与する必要があります。 まだカスタム ロールを作成していない場合は、[PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) または [Azure CLI](../../../role-based-access-control/custom-roles-cli.md) を使って作成してください

入力ファイルには次の内容を使用します。 実際のサブスクリプションに合わせて内容を調整する必要があります。つまり、c276fc76-9cd4-44c9-99a7-4fd71546436e and e91d47c4-76f3-4271-a796-21b4ecfe3624 は、ご利用のサブスクリプションの ID に置き換えます。 ご利用のサブスクリプションが 1 つしかない場合は、AssignableScopes の 2 つ目のエントリは削除してください。

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** サービス プリンシパルにカスタム ロールを割り当てる

最後の章で作成したカスタム ロール "Linux Fence Agent Role" をサービス プリンシパルに割り当てます。 所有者ロールは今後使わないでください。

1. [https://portal.azure.com](https://portal.azure.com) に移動します
1. [All resources] \(すべてのリソース) ブレードを開きます
1. 1 つ目のクラスター ノードの仮想マシンを選択します
1. [アクセス制御 (IAM)] を選択します
1. [ロールの割り当ての追加] をクリックします
1. "Linux Fence Agent Role" ロールを選択します
1. 上記で作成したアプリケーションの名前を入力します
1. [保存] をクリックします。

2 つ目のクラスター ノードについても上記の手順を繰り返します。

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH デバイスを作成します

仮想マシンのアクセス許可を編集したあとで、クラスターの STONITH デバイスを構成できます。

> [!NOTE]
> "pcmk_host_map" オプションは、ホスト名と Azure VM 名が同一でない場合のみ、このコマンドで必要です。 **hostname:vm-name** 形式でマッピングを指定します。
> このコマンドの太字のセクションを参照してください。

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> 監視およびフェンス操作は逆シリアル化されます。 その結果、長時間実行されている監視操作と同時フェンス イベントがある場合、既に実行されている監視操作により、クラスターのフェールオーバーに遅延は発生しません。

> [!TIP]
>Azure Fence Agent では、[標準 ILB を使用した VM 用のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)に関する記事で説明されているように、使用可能なソリューションと共に、パブリック エンドポイントへの送信接続が必要です。  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Azure のスケジュール化されたイベントの Pacemaker 構成

Azure では、[スケジュール化されたイベント](../../linux/scheduled-events.md)が提供されています。 スケジュール化されたイベントは、メタデータ サービスを介して提供され、VM のシャットダウンや VM の再デプロイなどのイベントに対して準備する時間をアプリケーションに与えます。リソース エージェント **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** では、スケジュール化された Azure イベントが監視されます。 イベントが検出された場合、エージェントは影響を受ける VM 上のすべてのリソースを停止してクラスター内の別のノードに移動しようとします。 これを実現するには、追加の Pacemaker リソースを構成する必要があります。 

1. **[A]** **azure-events** エージェントのパッケージが既にインストールされ、最新であることを確認します。 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Pacemaker 内でリソースを構成します。 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > azure-events エージェントの Pacemaker リソースを構成した後、クラスターのメンテナンス モードを設定または設定解除すると、次のような警告メッセージが表示されることがあります。  
     警告: cib-bootstrap-options: 属性 'hostName_  <strong>hostname</strong>' が不明です  
     警告: cib-bootstrap-options: 属性 'azure-events_globalPullState' が不明です  
     警告: cib-bootstrap-options: 属性 'hostName_ <strong>hostname</strong>' が不明です  
   > これらの警告メッセージは無視できます。

## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性][sles-nfs-guide]
* [SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性][sles-guide]
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
