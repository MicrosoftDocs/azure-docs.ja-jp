---
title: RHEL マルチ SID 上の SAP NW に対する Azure VM の高可用性に関するガイド | Microsoft Docs
description: Azure 仮想マシン (VM) RHEL マルチ SID 上の SAP NW の高可用性を確立します。
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
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676914"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

この記事では、Red Hat Enterprise Linux for SAP Applications を使用して、Azure VM の 2 ノード クラスターに複数の SAP NetWeaver 高可用性システム (つまりマルチ SID) をデプロイする方法について説明します。  

この例の構成では (インストール コマンドなど)、3 つの SAP NetWeaver 7.50 システムを、1 つの 2 ノード高可用性クラスターにデプロイします。 SAP システムの SID は次のとおりです。
* **NW1**: ASCS インスタンス番号 **00** および仮想ホスト名 **msnw1ascs**、ERS インスタンス番号 **02** および仮想ホスト名 **msnw1ers**。  
* **NW2**: ASCS インスタンス番号 **10** および仮想ホスト名 **msnw2ascs**、ERS インスタンス番号 **12** および仮想ホスト名 **msnw2ers**。  
* **NW3**: ASCS インスタンス番号 **20** および仮想ホスト名 **msnw3ascs**、ERS インスタンス番号 **22** および仮想ホスト名 **msnw3ers**。  

この記事では、データベース層および SAP NFS 共有のデプロイについては説明しません。 この記事の例では、NFS 共有に [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) のボリューム **sapMSID** を使用しますが、このボリュームは既にデプロイされているものとします。 また、Azure NetApp Files ボリュームは NFSv3 プロトコルを使用してデプロイされており、SAP システム NW1、NW2、NW3 の ASCS および ERS インスタンスのクラスター リソース用に次のファイル パスが存在するものとします。  

* ボリューム sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* ボリューム sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* ボリューム sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* ボリューム sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

始める前に、次の SAP のノートとホワイトペーパーを参照してください。

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* [Azure NetApp Files のドキュメント][anf-azure-doc]
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Pacemaker クラスターでの SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 7.5 内でスタンドアロン リソースを使用して SAP Netweaver のために ASCS/ERS を構成する](https://access.redhat.com/articles/3569681)
  * [RHEL 上の Pacemaker で SAP S/4HANA ASCS/ERS と Standalone Enqueue Server 2 (ENSA2) を構成する](https://access.redhat.com/articles/3974941)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]

## <a name="overview"></a>概要

クラスターに参加している仮想マシンは、フェールオーバーが発生したときのために、すべてのリソースを実行できるサイズになっている必要があります。 マルチ SID 高可用性クラスターでは、各 SAP SID は、相互に独立してフェールオーバーできます。  

高可用性を実現するには、SAP NetWeaver に高可用性の共有が必要です。 このドキュメントでは、[Azure NetApp Files の NFS ボリューム](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)にデプロイされる SAP 共有の例を紹介します。 また、複数の SAP システムで使用できる高可用性の [GlusterFS クラスター](./high-availability-guide-rhel-glusterfs.md)で共有をホストすることもできます。  

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Azure VM でゲスト オペレーティング システムとして Red Hat Linux を使用する SAP ASCS/ERS のマルチ SID クラスタリングのサポートでは、同じクラスター上の SAP SID は **5** 個に制限されています。 新しい SID が追加されるたびに、複雑さが増します。 SAP エンキュー レプリケーション サーバー 1 とエンキュー レプリケーション サーバー 2 を同じクラスター上に配置することは、**サポートされていません**。 マルチ SID クラスタリングとは、1 つの Pacemaker クラスター内での異なる SID を持つ複数の SAP ASCS/ERS インスタンスのインストールを指します。 現在、マルチ SID クラスタリングは ASCS/ERS に対してのみサポートされています。  

> [!TIP]
> SAP ASCS/ERS のマルチ SID クラスタリングは、さらに複雑なソリューションです。 実装するのがいっそう複雑になります。 また、メンテナンス作業 (OS の修正プログラムの適用など) を行うときの管理労力も増加します。 実際の実装を始める前に、デプロイと、VM、NFS マウント、VIP、ロード バランサーの構成などの関連するすべてのコンポーネントを、時間をかけて慎重に計画してください。  

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS では、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) の使用をお勧めします。  

次の一覧では、3 つの SAP システムから成るこのマルチ SID クラスターの例に対する (A)SCS と ERS ロード バランサーの構成を示します。 各 SID の ASCS および ERS インスタンスごとに、個別のフロントエンド IP、正常性プローブ、負荷分散規則が必要になります。 ASCS/ASCS クラスターの一部であるすべての VM を、単一 ILB の 1 つのバックエンド プールに割り当てます。  

### <a name="ascs"></a>(A)SCS

* フロントエンドの構成
  * NW1 の IP アドレス: 10.3.1.50
  * NW2 の IP アドレス: 10.3.1.52
  * NW3 の IP アドレス: 10.3.1.54

* プローブ ポート
  * ポート 620 <strong>&lt;nr&gt;</strong>。したがって、NW1、NW2、NW3 のプローブ ポートの場合は、620 **00**、620 **10**、620 **20**
* 負荷分散規則 - インスタンス (つまり、NW1/ASCS、NW2/ASCS、NW3/ASCS) ごとに 1 つ作成します。
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
  * Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* フロントエンドの構成
  * NW1 の IP アドレス: 10.3.1.51
  * NW2 の IP アドレス: 10.3.1.53
  * NW3 の IP アドレス: 10.3.1.55

* プローブ ポート
  * ポート 621 <strong>&lt;nr&gt;</strong>。したがって、NW1、NW2、N3 のプローブ ポートの場合は、621 **02**、621 **12**、621 **22**
* 負荷分散規則 - インスタンス (つまり、NW1/ERS、NW2/ERS、NW3/ERS) ごとに 1 つ作成します。
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
  * Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み

> [!IMPORTANT]
> フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

> [!Note]
> パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。  

> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。

## <a name="sap-shares"></a>SAP 共有

SAP NetWeaver では、転送、プロファイル ディレクトリなどに対する共有ストレージが必要です。 高可用性の SAP システムの場合、高可用性の共有を使用することが重要です。 SAP 共有のアーキテクチャを決定する必要があります。 1 つの方法は、[Azure NetApp Files NFS ボリューム](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)に共有をデプロイすることです。  Azure NetApp Files には、SAP NFS 共有の高可用性が組み込まれています。

もう 1 つの方法は、[Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS](./high-availability-guide-rhel-glusterfs.md) を構築して、複数の SAP システム間で共有できるようにすることです。 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>最初の SAP システムをクラスターにデプロイする

SAP 共有のアーキテクチャを決定したので、次に、対応するドキュメントに従って、クラスターに最初の SAP システムをデプロイします。

* Azure NetApp Files NFS ボリュームを使用する場合は、「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](./high-availability-guide-rhel-netapp-files.md)」に従ってください  
* GlusterFS クラスターを使用する場合は、「[Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS](./high-availability-guide-rhel-glusterfs.md)」に従ってください。  

上記のドキュメントには、必要なインフラストラクチャを準備する、クラスターを構築する、SAP アプリケーションを実行する OS を準備する手順が説明されています。  

> [!TIP]
> 最初のシステムをデプロイした後は、クラスターに別の SAP SID を追加する前に、クラスターのフェールオーバー機能を必ずテストします。 それにより、クラスターに他の SAP システムを追加して複雑さが増す前に、クラスターの機能が動作することがわかります。   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>クラスターに他の SAP システムをデプロイする

この例では、システム **NW1** がクラスターに既にデプロイされているものとします。 クラスターに SAP システム **NW2** と **NW3** をデプロイする方法を示します。 

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

### <a name="prerequisites"></a>前提条件 

> [!IMPORTANT]
> 手順に従ってクラスターに追加の SAP システムをデプロイする前に、手順に従ってクラスターに最初の SAP システムをデプロイします。最初のシステムをデプロイするときにのみ必要な手順があります。  

このドキュメントでは次のことを前提としています。
* Pacemaker クラスターが既に構成され、実行されています。  
* 少なくとも 1 つの SAP システム (ASCS/ERS インスタンス) が既にデプロイされており、クラスターで実行されています。  
* クラスターのフェールオーバー機能のテストが完了しています。  
* すべての SAP システムの NFS 共有がデプロイされています。  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver のインストールを準備する

1. 「[Azure portal 経由での手動による Azure Load Balancer のデプロイ](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal)」の手順に従って、新しくデプロイされたシステム (つまり、**NW2**、**NW3**) の構成を、既存の Azure Load Balancer に追加します。 構成の IP アドレス、正常性プローブ ポート、負荷分散規則を調整します。  

2. **[A]** 追加の SAP システムの名前解決を設定します。 DNS サーバーを使用するか、すべてのノードで `/etc/hosts` を変更することができます。 この例では、`/etc/hosts` ファイルを使用する方法を示します。  IP アドレスとホスト名を環境に合わせて調整します。 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** クラスターにデプロイする追加の SAP システム **NW2** および **NW3** 用の共有ディレクトリを作成します。 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** クラスターにデプロイする追加の SAP システム用に /sapmnt/SID および /usr/sap/SID/SYS ファイル システムのマウント エントリを追加します。 この例では、**NW2** と **NW3** です。  

   クラスターにデプロイする追加の SAP システム用のファイル システムで、ファイル `/etc/fstab` を更新します。  

   * Azure NetApp Files を使用する場合は、[こちら](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)の手順に従ってください  
   * GlusterFS クラスターを使用する場合は、[こちら](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)の手順に従ってください  

### <a name="install-ascs--ers"></a>ASCS/ERS をインストールする

1. クラスターにデプロイする追加の SAP システムの ASCS インスタンス用に、仮想 IP と正常性プローブのクラスター リソースを作成します。 以下の例は、Azure NetApp Files ボリューム上の NFS に NFSv3 プロトコルを使用している **NW2** および **NW3** ASCS を示しています。  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。  

2. **[1]** SAP NetWeaver ASCS をインストールします  

   root として SAP NetWeaver ASCS をインストールします。そのとき、ASCS に対するロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名を使用します。 たとえば、システム **NW2** の場合、仮想ホスト名は <b>msnw2ascs</b>、<b>10.3.1.52</b>、およびロード バランサーのプローブに使用したインスタンス番号 (たとえば <b>10</b>) になります。 システム **NW3** の場合、仮想ホスト名は <b>msnw3ascs</b>、<b>10.3.1.54</b>、およびロード バランサーのプローブに使用したインスタンス番号 (たとえば <b>20</b>) になります。 SAP SID ごとに ASCS をインストールしたクラスター ノードを書き留めておきます。  

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。 仮想ホスト名を使用して SAP をインストールするには、パラメーター SAPINST_USE_HOSTNAME を使用します。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   インストールで /usr/sap/**SID**/ASCS **Instance#** へのサブフォルダーの作成が失敗する場合は、所有者を **sid** adm に設定し、グループを ASCS **Instance#** の sapsys に設定して、もう一度試してください。

3. **[1]** クラスターにデプロイする追加の SAP システムの ERS インスタンス用に、仮想 IP と正常性プローブのクラスター リソースを作成します。 以下の例は、Azure NetApp Files ボリューム上の NFS に NFSv3 プロトコルを使用している **NW2** および **NW3** ERS を示しています。  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。  

   次に、新しく作成した ERS グループのリソースが、同じ SAP システムの ASCS インスタンスをインストールしたクラスター ノードとは逆のクラスター ノードで実行されていることを確認します。  たとえば、NW2 ASCS が `rhelmsscl1` にインストールされている場合は、NW2 ERS グループが `rhelmsscl2` で実行されていることを確認します。  グループ内のいずれかのクラスター リソースに対して次のコマンドを実行して、NW2 ERS グループを `rhelmsscl2` に移行できます。 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NetWeaver ERS をインストールします

   root として SAP NetWeaver ERS を他のノードにインストールします。そのとき、ERS に対するロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名を使用します。 たとえば、システム **NW2** の場合、仮想ホスト名は <b>msnw2ers</b>、<b>10.3.1.53</b>、およびロード バランサーのプローブに使用したインスタンス番号 (たとえば <b>12</b>) になります。 システム **NW3** の場合は、仮想ホスト名は <b>msnw3ers</b>、<b>10.3.1.55</b>、およびロード バランサーのプローブに使用したインスタンス番号 (たとえば <b>22</b>) になります。 

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。 仮想ホスト名を使用して SAP をインストールするには、パラメーター SAPINST_USE_HOSTNAME を使用します。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 以降を使用します。 これより下位のバージョンではアクセス許可が正しく設定されないため、インストールが失敗します。

   インストールで /usr/sap/**NW2**/ERS **Instance#** へのサブフォルダーの作成が失敗する場合は、所有者を **sid** adm に設定し、グループを ERS **Instance#** フォルダーの sapsys に設定して、もう一度試してください。

   新しくデプロイした SAP システムの ERS グループを別のクラスター ノードに移行する必要があった場合は、ERS グループに対する場所の制約を忘れずに削除してください。 制約を削除するには、次のコマンドを実行します (この例は、SAP システム **NW2** と **NW3** に対するものです)。 必ず、ERS クラスター グループを移動するコマンドで使用したのと同じリソースの一時的な制約を削除してください。

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 新しくインストールした SAP システムの ASCS/SCS および ERS インスタンスのプロファイルを調整します。 NW2 の例を次に示します。 クラスターに追加したすべての SAP インスタンスの ASCS/SCS および ERS のプロファイルを調整する必要があります。  
 
   * ASCS/SCS プロファイル

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     ENSA1 と ENSA2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。    

   * ERS プロファイル

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** /usr/sap/sapservices ファイルを更新します

   sapinit スタートアップ スクリプトによってインスタンスが開始されないようにするには、Pacemaker によって管理されているすべてのインスタンスを `/usr/sap/sapservices` ファイルからコメント アウトする必要があります。  次に示す例は、SAP システム **NW2** と **NW3** に対するものです。  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 新しくインストールした SAP システム用の SAP クラスター リソースを作成します。  

   エンキュー サーバー 1 アーキテクチャ (ENSA1) を使用する場合は、次のように、SAP systems **NW2** と **NW3** のリソースを定義します。

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP では、SAP NW 7.52 の時点で、レプリケーションを含むエンキュー サーバー 2 のサポートが導入されました。 ABAP Platform 1809 以降では、エンキュー サーバー 2 が既定でインストールされます。 エンキュー サーバー 2 のサポートについては、SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) を参照してください。
   エンキュー サーバー 2 アーキテクチャ ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) を使用する場合は、次のように、SAP systems **NW2** と **NW3** のリソースを定義します。

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   以前のバージョンからアップグレードし、エンキュー サーバー 2 に切り替えている場合は、SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019) を参照してください。 

   > [!NOTE]
   > 上記のタイムアウト構成はほんの一例であり、特定の SAP 設定では調整する必要がある場合があります。 

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。
   次の例では、SAP システム **NW2** と **NW3** をクラスターに追加した後の、クラスター リソースの状態を示します。 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します。  次の例は、SAP システム **NW2** と **NW3** の両方に対するファイアウォール規則を示しています。  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>SAP のインストールを続ける 

次のようにして、SAP のインストールを完了します。

* [SAP NetWeaver アプリケーションサーバーを準備します](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS インスタンスをインストールします](./high-availability-guide-rhel-netapp-files.md#install-database)
* [プライマリ SAP アプリケーション サーバーをインストールします](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* 1 つまたは複数の追加の SAP アプリケーション インスタンスをインストールします

## <a name="test-the-multi-sid-cluster-setup"></a>マルチ SID クラスターのセットアップをテストする

次のテストは、Red Hat のベスト プラクティス ガイドに記載されているテスト ケースのサブセットです。 作業を容易にするためにここに含めてあります。 クラスター テストの完全な一覧については、次のドキュメントを参照してください。

* Azure NetApp Files NFS ボリュームを使用する場合は、「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](./high-availability-guide-rhel-netapp-files.md)」に従ってください
* 高可用性の `GlusterFS` を使用する場合は、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](./high-availability-guide-rhel.md)」に従ってください。  

常に Red Hat のベスト プラクティス ガイドを読み、追加されている可能性があるすべての追加テストを実行してください。  
示されているテストは、3 つの SAP システムがインストールされた、2 ノードのマルチ SID クラスターでのものです。  

1. ASCS インスタンスを手動で移行します。 この例では、SAP システム NW3 の ASCS インスタンスの移行を示します。

   テスト開始前のリソースの状態:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   次のコマンドを root として実行して、NW3 ASCS インスタンスを移行します。

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   テスト後のリソースの状態:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. ノードのクラッシュをシミュレートする

   テスト開始前のリソースの状態:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   少なくとも 1 つの ASCS インスタンスが実行されているノードで、次のコマンドを root として実行します。 この例では、NW1、NW2、NW3 の ASCS インスタンスが実行されている `rhelmsscl1` でコマンドを実行しました。  

   ```
   echo c > /proc/sysrq-trigger
   ```

   テストの後、クラッシュしたノードが再び開始された後の状態は、次のようになります。

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   失敗したリソースに関するメッセージがある場合は、失敗したリソースの状態をクリーンアップします。 次に例を示します。

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。