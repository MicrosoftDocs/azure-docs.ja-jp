---
title: Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性 | Microsoft Docs
description: Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503788"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

この記事では、[Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) を使用して、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、および高可用性 SAP NetWeaver 7.50 システムのインストールを行う方法について説明します。
この例の構成やインストール コマンドなどでは、ASCS インスタンスの番号は 00、ERS インスタンスの番号は 01、プライマリ アプリケーション インスタンス (PAS) の番号は 02、アプリケーション インスタンス (AAS) の番号は 03 です。 SAP システム ID QAS が使用されます。 

データベース レイヤーについては、詳しくは説明していません。  

はじめに、次の SAP Note およびガイドを確認してください

* [Azure NetApp Files のドキュメント][anf-azure-doc] 
* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

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

SAP Netweaver セントラル サービスの高可用性 (HA) を実現するには、共有ストレージが必要です。
従来、Red Hat Linux 上でこれを実現するには、高可用性 GlusterFS クラスターを別途構築する必要がありました。 

現在は、Azure NetApp Files 上にデプロイした共有ストレージを使用して、SAP Netweaver HA を実現できるようになりました。 Azure NetApp Files を共有ストレージに使用すると、追加の [GlusterFS クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)は必要なくなります。 SAP Netweaver セントラル サービス (ASCS/SCS) の HA を実現するには、依然として Pacemaker が必要です。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS、SAP HANA データベースでは、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 (A)SCS と ERS に別々のフロント IP を使用したロード バランサーの構成を次に示します。

> [!IMPORTANT]
> Azure VM での Red Hat Linux をゲスト オペレーティング システムとした SAP ASCS/ERS のマルチ SID クラスタリングは**サポートされていません**。 マルチ SID クラスタリングとは、1 つの Pacemaker クラスター内での異なる SID を持つ複数の SAP ASCS/ERS インスタンスのインストールを指します。

### <a name="ascs"></a>(A)SCS

* フロントエンドの構成
  * IP アドレス 192.168.14.9
* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 620<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* フロントエンドの構成
  * IP アドレス 192.168.14.10
* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 621<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャの設定 

SAP NetWeaver では、転送とプロファイル ディレクトリ用の共有ストレージが必要です。  Azure NetApp Files インフラストラクチャの設定を続行する前に、「[Azure NetApp Files のドキュメント][anf-azure-doc]」の内容をよく理解しておいてください。 選択した Azure リージョンで、Azure NetApp Files が利用できるかどうかを確認します。 Azure リージョン別に Azure NetApp Files が利用可能かどうかを確認するには、[Azure リージョン別の Azure NetApp Files の利用可能性][anf-avail-matrix]に関するページを参照してください。

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する][anf-register]」の手順に従って、Azure NetApp Files へのオンボードを要求してください。 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ  

以下の手順では、[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) が既にインストールされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内にデプロイする必要があります。  

1. まだそのようになっていない場合は、[Azure NetApp Files へのオンボード](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)を要求してください。  
2. [NetApp アカウントの作成手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)に関するページに従って、選択した Azure リージョン内で NetApp アカウントを作成します。  
3. [Azure NetApp Files の容量プールの設定手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)に関するページに従って、Azure NetApp Files の容量プールを設定します。  
この記事で示されている SAP Netweaver アーキテクチャでは、1 つの Azure NetApp Files の容量プール、Premium SKU が使用されています。 Azure 上の SAP Netweaver アプリケーション ワークロード用には、Azure NetApp Files Premium SKU をお勧めします。  
4. [Azure NetApp Files へのサブネットの委任手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)に関するページの説明に従って、サブネットを Azure NetApp Files に委任します。  

5. [Azure NetApp Files 用のボリュームの作成手順](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)に関するページに従って、Azure NetApp Files のボリュームをデプロイします。 指定された Azure NetApp Files の[サブネット](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)内にボリュームをデプロイします。 Azure NetApp Files のリソースと Azure VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内に配置する必要があることに注意してください。 この例では、sap<b>QAS</b> と transSAP という、2 つの Azure NetApp Files ボリュームを使用します。 対応するマウント ポイントにマウントされるファイル パスは、/usrsap<b>qas</b>/sapmnt<b>QAS</b>、/usrsap<b>qas</b>/usrsap<b>QAS</b>sys のようになります。  

   1. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. ボリューム transSAP (nfs://192.168.24.4/transSAP)
   6. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
この例では、すべての SAP Netweaver ファイル システム用に Azure NetApp Files を使用して、Azure NetApp Files の使用方法を説明しました。 NFS 経由でマウントする必要がない SAP ファイル システムを [Azure ディスク ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)としてデプロイすることもできます。 この例の場合、<b>a-e</b> は、Azure NetApp Files 上に配置する必要があります。<b>f-g</b> (/usr/sap/<b>QAS</b>/D<b>02</b>、/usr/sap/<b>QAS</b>/D<b>03</b>) は、Azure ディスク ストレージとしてデプロイできます。 

### <a name="important-considerations"></a>重要な考慮事項

SUSE High Availability アーキテクチャ上で SAP Netweaver 用に Azure NetApp Files を検討するときは、以下の重要な考慮事項に注意してください。

- 最小容量プールは 4 TiB です。 容量プールのサイズは 4 TiB の倍数である必要があります。
- 最小ボリュームは 100 GiB です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)内に存在する必要があります。 同じリージョン内の VNET ピアリング経由での Azure NetApp Files のアクセスが、サポートされるようになっています。 グローバル ピアリング経由での Azure NetApp Files のアクセスは、まだサポートされていません。
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- 現在、Azure NetApp Files でサポートされるのは NFSv3 のみです。 
- Azure NetApp Files の[エクスポート ポリシー](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)では、ユーザーが制御できるのは、許可されたクライアント、アクセスの種類 (読み取りおよび書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、Azure NetApp Files 機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。 

## <a name="setting-up-ascs"></a>(A)SCS のセットアップ

この例では、リソースは [Azure portal](https://portal.azure.com/#home) を使用して手動でデプロイされています。

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal を使用した手動による Linux のデプロイ

最初に Azure NetApp Files ボリュームを作成する必要があります。 VM をデプロイします。 その後、ロード バランサーを作成し、バックエンド プール内の仮想マシンを使用します。

1. ロード バランサー (内部) を作成します  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 192.168.14.9
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **frontend.QAS.ASCS**)
         1. [割り当て] を [静的] に設定し、IP アドレスを入力します (例: **192.168.14.9**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 192.168.14.10
         * 上記の "a" 以下の手順を繰り返して、ERS の IP アドレスを作成します (例: **192.168.14.10** および **frontend.QAS.ERS**)
   1. バックエンド プールを作成します
      1. ASCS のバックエンド プールの作成
         1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
         1. 新規のバックエンド プールの名前を入力します (例: **backend.QAS**)
         1. [仮想マシンの追加] をクリックします。
         1. 前に作成した ASCS 用の可用性セットを選択します 
         1. (A)SCS クラスターの仮想マシンを選択します
         1. [OK] をクリックします
   1. 正常性プローブを作成します
      1. ASCS のポート 620**00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **health.QAS.ASCS**)
         1. プロトコルに TCP、ポートに 620**00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621**01**
            * 上記の "c" 以下の手順を繰り返して、ERS の正常性プローブを作成します (例: 621**01** および **health.QAS.ERS**)
   1. 負荷分散規則
      1. ASCS の 32**00** TCP
         1. ロード バランサーを開き、負荷分散規則を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **lb.QAS.ASCS.3200**)
         1. 前に作成した ASCS 用のフロントエンド IP アドレス、バックエンド プール、および正常性プローブを選択します (例: **frontend.QAS.ASCS**)
         1. プロトコルは **TCP** のままにし、ポートに「**3200**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
      1. ASCS の追加のポート
         * ASCS のポート 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 と TCP に対して上記の手順を繰り返します
      1. ASCS ERS の追加のポート
         * ASCS ERS のポート 32**01**、33**01**、5**01**13、5**01**14、5**01**16 と TCP に対して上記の "d" 以下の手順を繰り返します


> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください。

### <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md)」 (Azure で Red Hat Enterprise Linux に Pacemaker を設定する) に記載の手順に従って、この (A)SCS サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver のインストールの準備

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます

    ```
    sudo vi /etc/hosts
    ```

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Azure NetApp Files ボリュームに SAP ディレクトリを作成します。  
   いずれかの VM で Azure NetApp Files ボリュームを一時的にマウントし、SAP ディレクトリ (ファイル パス) を作成します。  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS sudo chattr +i /usr/sap/trans sudo chattr +i /usr/sap/QAS/SYS sudo chattr +i /usr/sap/QAS/ASCS00 sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Loaded plugins: langpacks, product-id, search-disabled-repos
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # <a name="installed-packages"></a>Installed Packages
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Version     :3.9.5
   # <a name="release------124el7"></a>Release     :124.el7
   # <a name="size---------100-k"></a>Size        :100 k
   # <a name="repo---------installed"></a>Repo        : installed
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>From repo   : rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Summary     :SAP cluster resource agents and connector script
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL         : https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>License     :GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Description :The SAP resource agents and connector script interface with
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>:Pacemaker to allow SAP instances to be managed in a cluster
   #          <a name="-environment"></a>: environment.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3  192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3  192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Set the property ResourceDisk.EnableSwap to y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # <a name="size-of-the-swapfile"></a>Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl2-standby"></a>Node anftstsapcl2: standby
   # <a name="online--anftstsapcl1-"></a>Online: [ anftstsapcl1 ]
   #
   # <a name="full-list-of-resources"></a>Full list of resources:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo pcs node unstandby anftstsapcl2 sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl1-standby"></a>Node anftstsapcl1: standby
   # <a name="online--anftstsapcl2-"></a>Online: [ anftstsapcl2 ]
   #
   # <a name="full-list-of-resources"></a>Full list of resources:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2<
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Change the restart command to a start command
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Change the restart command to a start command
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>remove Autostart from ERS profile
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>On the node where you installed the ASCS, comment out the following line
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>On the node where you installed the ERS, comment out the following line
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000  sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1  sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000  sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pcs status
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # <a name="full-list-of-resources"></a>Full list of resources:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>Resource Group: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>Resource Group: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent sudo firewall-cmd --zone=public --add-port=62000/tcp sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent sudo firewall-cmd --zone=public --add-port=3200/tcp sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent sudo firewall-cmd --zone=public --add-port=3600/tcp sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent sudo firewall-cmd --zone=public --add-port=3900/tcp sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent sudo firewall-cmd --zone=public --add-port=8100/tcp sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent sudo firewall-cmd --zone=public --add-port=50013/tcp sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent sudo firewall-cmd --zone=public --add-port=50014/tcp sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent sudo firewall-cmd --zone=public --add-port=50016/tcp
   # <a name="probe-port-of-ers"></a>Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent sudo firewall-cmd --zone=public --add-port=62101/tcp sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent sudo firewall-cmd --zone=public --add-port=3301/tcp sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent sudo firewall-cmd --zone=public --add-port=50113/tcp sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent sudo firewall-cmd --zone=public --add-port=50114/tcp sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>マウントする
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>マウントする
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Set the property ResourceDisk.EnableSwap to y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # <a name="size-of-the-swapfile"></a>Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent restart
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore List
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT   ENV :192.168.14.4:30313   USER:SAPABAP1   DATABASE:QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - qasadm hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl2  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    Started anftstsapcl1  Resource Group: g-QAS_ASCS      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):  Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):    Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): Started anftstsapcl1  Resource Group: g-QAS_AERS      fs_QAS_AERS        (ocf::heartbeat:Filesystem):  Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):    Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):     Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance): Started anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
