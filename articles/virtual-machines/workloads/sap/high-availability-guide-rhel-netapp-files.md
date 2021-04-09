---
title: Azure NetApp Files での RHEL 上の SAP NW 用の Azure VM の高可用性| Microsoft Docs
description: Azure NetApp Files で Azure 仮想マシン (VM) RHEL 上の SAP NW の高可用性を確立します。
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
ms.openlocfilehash: e652d1374db12d797dc4505f07350e6e110d6408
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674435"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性

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

この記事では、[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) を使用して、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、および高可用性 SAP NetWeaver 7.50 システムのインストールを行う方法について説明します。
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

現在は、Azure NetApp Files 上にデプロイした共有ストレージを使用して、SAP Netweaver HA を実現できるようになりました。 Azure NetApp Files を共有ストレージに使用すると、追加の [GlusterFS クラスター](./high-availability-guide-rhel-glusterfs.md)は必要なくなります。 SAP Netweaver セントラル サービス (ASCS/SCS) の HA を実現するには、依然として Pacemaker が必要です。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS、SAP HANA データベースでは、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) の使用をお勧めします。 (A)SCS と ERS に別々のフロントエンド IP を使用したロード バランサーの構成を次に示します。

### <a name="ascs"></a>(A)SCS

* フロントエンドの構成
  * IP アドレス 192.168.14.9
* プローブ ポート
  * ポート 620<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
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
* プローブ ポート
  * ポート 621<strong>&lt;nr&gt;</strong>
* 負荷分散規則
  * Standard Load Balancer を使用する場合は、 **[HA ポート]** を選択します
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

* バックエンドの構成
  * (A)SCS/ERS クラスターに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャの設定 

SAP NetWeaver では、転送とプロファイル ディレクトリ用の共有ストレージが必要です。  Azure NetApp Files インフラストラクチャの設定を続行する前に、「[Azure NetApp Files のドキュメント][anf-azure-doc]」の内容をよく理解しておいてください。 選択した Azure リージョンで、Azure NetApp Files が利用できるかどうかを確認します。 Azure リージョン別に Azure NetApp Files が利用可能かどうかを確認するには、[Azure リージョン別の Azure NetApp Files の利用可能性][anf-avail-matrix]に関するページを参照してください。

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する][anf-register]」の手順に従って、Azure NetApp Files へのオンボードを要求してください。 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ  

以下の手順では、[Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) が既にインストールされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内にデプロイする必要があります。  

1. まだそのようになっていない場合は、[Azure NetApp Files へのオンボード](../../../azure-netapp-files/azure-netapp-files-register.md)を要求してください。  
2. [NetApp アカウントの作成手順](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)に関するページに従って、選択した Azure リージョン内で NetApp アカウントを作成します。  
3. [Azure NetApp Files の容量プールの設定手順](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)に関するページに従って、Azure NetApp Files の容量プールを設定します。  
この記事で示されている SAP Netweaver アーキテクチャでは、1 つの Azure NetApp Files の容量プール、Premium SKU が使用されています。 Azure 上の SAP Netweaver アプリケーション ワークロード用には、Azure NetApp Files Premium SKU をお勧めします。  
4. [Azure NetApp Files へのサブネットの委任手順](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)に関するページの説明に従って、サブネットを Azure NetApp Files に委任します。  

5. [Azure NetApp Files 用のボリュームの作成手順](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)に関するページに従って、Azure NetApp Files のボリュームをデプロイします。 指定された Azure NetApp Files の[サブネット](/rest/api/virtualnetwork/subnets)内にボリュームをデプロイします。 Azure NetApp ボリュームの IP アドレスは、自動的に割り当てられます。 Azure NetApp Files のリソースと Azure VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内に配置する必要があることに注意してください。 この例では、sap<b>QAS</b> と transSAP という、2 つの Azure NetApp Files ボリュームを使用します。 対応するマウント ポイントにマウントされるファイル パスは、/usrsap<b>qas</b>/sapmnt<b>QAS</b>、/usrsap<b>qas</b>/usrsap<b>QAS</b>sys のようになります。  

   1. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. ボリューム transSAP (nfs://192.168.24.4/transSAP)
   6. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. ボリューム sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
この例では、すべての SAP Netweaver ファイル システム用に Azure NetApp Files を使用して、Azure NetApp Files の使用方法を説明しました。 NFS 経由でマウントする必要がない SAP ファイル システムを [Azure ディスク ストレージ](../../disks-types.md#premium-ssd)としてデプロイすることもできます。 この例の場合、<b>a-e</b> は、Azure NetApp Files 上に配置する必要があります。<b>f-g</b> (/usr/sap/<b>QAS</b>/D<b>02</b>、/usr/sap/<b>QAS</b>/D<b>03</b>) は、Azure ディスク ストレージとしてデプロイできます。 

### <a name="important-considerations"></a>重要な考慮事項

SUSE High Availability アーキテクチャ上で SAP Netweaver 用に Azure NetApp Files を検討するときは、以下の重要な考慮事項に注意してください。

- 最小容量プールは 4 TiB です。 容量プールのサイズは、1 TiB 単位で増やすことができます。
- 最小ボリュームは 100 GiB です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)内に存在する必要があります。 同じリージョン内の VNET ピアリング経由での Azure NetApp Files のアクセスが、サポートされるようになっています。 グローバル ピアリング経由での Azure NetApp Files のアクセスは、まだサポートされていません。
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp Files の[エクスポート ポリシー](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)では、ユーザーが制御できるのは、許可されたクライアント、アクセスの種類 (読み取りおよび書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、Azure NetApp Files 機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。 
- Azure NetApp Files ボリュームは、NFSv3 または NFSv4.1 ボリュームとしてデプロイできます。 SAP アプリケーション層 (ASCS/ERS、SAP アプリケーション サーバー) では、両方のプロトコルがサポートされています。 

## <a name="setting-up-ascs"></a>(A)SCS のセットアップ

この例では、リソースは [Azure portal](https://portal.azure.com/#home) を使用して手動でデプロイされています。

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal を使用した手動による Linux のデプロイ

最初に Azure NetApp Files ボリュームを作成する必要があります。 VM をデプロイします。 その後、ロード バランサーを作成し、バックエンド プール内の仮想マシンを使用します。

1. ロード バランサー (内部、標準) を作成します。  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 192.168.14.9
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **frontend.QAS.ASCS**)
         1. [割り当て] を [静的] に設定し、IP アドレスを入力します (例: **192.168.14.9**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 192.168.14.10
         * 上記の "a" 以下の手順を繰り返して、ERS の IP アドレスを作成します (例: **192.168.14.10** および **frontend.QAS.ERS**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新規のバックエンド プールの名前を入力します (例: **backend.QAS**)
      1. [仮想マシンの追加] をクリックします。
      1. 仮想マシンを選択します。 
      1. (A)SCS クラスターの仮想マシンとその IP アドレスを選択します。
      1. [追加] をクリックします。
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **health.QAS.ASCS**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **01**
            * 上記の "c" 以下の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **01** および **health.QAS.ERS**)
   1. 負荷分散規則
      1. ASCS の負荷分散規則
         1. ロード バランサーを開き、負荷分散規則を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **lb.QAS.ASCS**)
         1. 前に作成した ASCS 用のフロントエンド IP アドレス、バックエンド プール、および正常性プローブを選択します (例: **frontend.QAS.ASCS**、**backend.QAS**、**health.QAS.ASCS**)
         1. **[HA ポート]** を選択します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
         * 上記の手順を繰り返して、ERS の負荷分散規則を作成します (例: **lb.QAS.ERS**)
1. または、シナリオに基本的なロード バランサー (内部) が必要な場合は、次の手順に従ってください。  
   1. フロントエンド IP アドレスを作成します
      1. ASCS の IP アドレス 192.168.14.9
         1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
         1. 新規のフロントエンド IP プールの名前を入力します (例: **frontend.QAS.ASCS**)
         1. [割り当て] を [静的] に設定し、IP アドレスを入力します (例: **192.168.14.9**)
         1. [OK] をクリックします
      1. ASCS ERS の IP アドレス 192.168.14.10
         * 上記の "a" 以下の手順を繰り返して、ERS の IP アドレスを作成します (例: **192.168.14.10** および **frontend.QAS.ERS**)
   1. バックエンド プールの作成
      1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
      1. 新規のバックエンド プールの名前を入力します (例: **backend.QAS**)
      1. [仮想マシンの追加] をクリックします。
      1. 前に作成した ASCS 用の可用性セットを選択します 
      1. (A)SCS クラスターの仮想マシンを選択します
      1. [OK] をクリックします
   1. 正常性プローブを作成します
      1. ASCS のポート 620 **00**
         1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
         1. 新しい正常性プローブの名前を入力します (例: **health.QAS.ASCS**)
         1. プロトコルに TCP、ポートに 620 **00** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
         1. [OK] をクリックします
      1. ASCS ERS のポート 621 **01**
            * 上記の "c" 以下の手順を繰り返して、ERS の正常性プローブを作成します (例: 621 **01** および **health.QAS.ERS**)
   1. 負荷分散規則
      1. ASCS の 32 **00** TCP
         1. ロード バランサーを開き、負荷分散規則を選択して [追加] をクリックします
         1. 新しいロード バランサー規則の名前を入力します (例: **lb.QAS.ASCS.3200**)
         1. 前に作成した ASCS 用のフロントエンド IP アドレス、バックエンド プール、および正常性プローブを選択します (例: **frontend.QAS.ASCS**)
         1. プロトコルは **TCP** のままにし、ポートに「**3200**」を入力します
         1. アイドル タイムアウトを 30 分に増やします
         1. **Floating IP を有効にします**
         1. [OK] をクリックします
      1. ASCS の追加のポート
         * ASCS のポート 36 **00**、39 **00**、81 **00**、5 **00** 13、5 **00** 14、5 **00** 16 と TCP に対して上記の手順を繰り返します
      1. ASCS ERS の追加のポート
         * ASCS ERS のポート 32 **01**、33 **01**、5 **01** 13、5 **01** 14、5 **01** 16 と TCP に対して上記の "d" 以下の手順を繰り返します

      > [!IMPORTANT]
      > フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

      > [!Note]
      > パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。  

      > [!IMPORTANT]
      > Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID マッピングを無効にする (NFSv 4.1 を使用する場合)

このセクションの手順は、NFSv4.1 プロトコルで Azure NetApp Files ボリュームを使用している場合にのみ適用されます。 Azure NetApp Files NFSv4.1 ボリュームがマウントされるすべての VM で構成を実行します。  

1. NFS ドメイン設定を確認します。 ドメインが既定の Azure NetApp Files ドメイン (つまり、 **`defaultv4iddomain.com`** ) として構成され、マッピングが **nobody** に設定されていることを確認します。  

    > [!IMPORTANT]
    > Azure NetApp Files の既定のドメイン構成 ( **`defaultv4iddomain.com`** ) と一致するように、VM 上の `/etc/idmapd.conf` に NFS ドメインを設定していることを確認します。 NFS クライアント (つまり、VM) と NFS サーバー (つまり、Azure NetApp 構成) のドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は `nobody` と表示されます。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping` を確認します。 これは、**Y** に設定されている必要があります。`nfs4_disable_idmapping` が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   `nfs4_disable_idmapping` パラメーターを変更する方法については、「 https://access.redhat.com/solutions/1749883 」を参照してください。

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
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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
    ``` 

1. **[A]** 共有ディレクトリを作成します

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS クライアントとその他の要件をインストールします

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** resource-agents-sap のバージョンを確認します

   インストールされている resource-agents-sap パッケージのバージョンが、3.9.5-124.el7 以上であることを確認します
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** マウント エントリを追加します

   NFSv3 を使用する場合:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv 4.1 を使用する場合:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > ボリュームをマウントするときは、Azure NetApp Files ボリュームの NFS プロトコルバージョンと一致していることを確認してください。 Azure NetApp Files ボリュームが NFSv3 ボリュームとして作成されている場合は、対応する NFSv3 構成を使用します。 Azure NetApp Files ボリュームが NFSv4.1 ボリュームとして作成されている場合は、手順に従って ID マッピングを無効にし、対応する NFSv4.1 構成を使用してください。 この例では、Azure NetApp Files ボリュームが NFSv3 ボリュームとして作成されています。  

   新しい共有をマウントします

   ```
   sudo mount -a  
   ```

1. **[A]** スワップ ファイルを構成します

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   エージェントを再起動して変更をアクティブにします

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL 構成

   SAP Note [2002167] 内の説明に従って RHEL を構成します

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS のインストール

1. **[1]** ASCS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** SAP NetWeaver ASCS をインストールします  

   root として SAP NetWeaver ASCS を最初のノードにインストールします。その際、ASCS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>anftstsapvh</b>、<b>192.168.14.9</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>00</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   インストールで /usr/sap/**QAS**/ASCS **00** へのサブフォルダーの作成に失敗する場合は、ASCS **00** フォルダーの所有者とグループを設定し、もう一度試してください。

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** ERS インスタンス用の仮想 IP リソースと正常性プローブを作成します

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NetWeaver ERS をインストールします  

   root として SAP NetWeaver ERS を 2 番目のノードにインストールします。その際、ERS のロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名 (たとえば、<b>anftstsapers</b>、<b>192.168.14.10</b>) と、ロード バランサーのプローブに使用したインスタンス番号 (たとえば、<b>01</b>) を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   インストールで /usr/sap/**QAS**/ERS **01** へのサブフォルダーの作成に失敗する場合は、ERS **01** フォルダーの所有者とグループを設定し、もう一度試してください。

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** ASCS/SCS および ERS インスタンス プロファイルを適用します

   * ASCS/SCS プロファイル

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   ENSA1 と ENSA2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。  

   * ERS プロファイル

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** キープ アライブを構成します

   SAP NetWeaver アプリケーション サーバーと ASCS/SCS の間の通信は、ソフトウェア ロード バランサーを介してルーティングされます。 ロード バランサーは、構成可能なタイムアウト後に非アクティブな接続を切断します。 これを防止するには、SAP NetWeaver ASCS/SCS プロファイル内にパラメーターを設定し (ENSA1 を使用している場合)、ENSA1/ENSA2 の両方について、すべての SAP サーバーで Linux システムの `keepalive` 設定を変更する必要があります。 詳細については、[SAP Note 1410736][1410736] を参照してください。

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** /usr/sap/sapservices ファイルを更新します

   Sapinit スタートアップ スクリプトによってインスタンスが開始されるのを防ぐために、Pacemaker によって管理されているすべてのインスタンスを /usr/sap/sapservices ファイルからコメント アウトする必要があります。 SAP HANA インスタンスが HANA SR と一緒に使用されている場合は、SAP HANA インスタンスをコメント アウトしないでください。

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** SAP クラスター リソースを作成します

   エンキュー サーバー 1 のアーキテクチャ (ENSA1) を使用する場合は、次のようにリソースを定義します。

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP では、SAP NW 7.52 の時点で、レプリケーションを含むエンキュー サーバー 2 のサポートが導入されました。 ABAP Platform 1809 以降では、エンキュー サーバー 2 が既定でインストールされます。 エンキュー サーバー 2 のサポートについては、SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) を参照してください。
   エンキュー サーバー 2 アーキテクチャ ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) を使用する場合は、resource-agents-sap-4.1.1-12.el7.x86_64 以降のリソース エージェントをインストールし、リソースを次のように定義します。

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   以前のバージョンからアップグレードし、エンキュー サーバー 2 に切り替えている場合は、SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322) を参照してください。 

   > [!NOTE]
   > 上記のタイムアウト構成はほんの一例であり、特定の SAP 設定では調整する必要がある場合があります。 

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します ASCS および ERS に対するファイアウォール規則を両方のノード上に追加します。
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver アプリケーション サーバーの準備

   一部のデータベースでは、データベース インスタンスのインストールがアプリケーション サーバーで実行される必要があります。 このような場合に使用できるようにアプリケーション サーバー仮想マシンを準備します。  

   次の手順では、ASCS/SCS および HANA サーバーとは別のサーバーにアプリケーション サーバーをインストールすることを前提としています。 それ以外の場合、以下の手順の一部 (ホスト名解決の構成など) は必要ありません。  

   次の各手順の先頭には、 **[A]** - PAS と AAS の両方が該当、 **[P]** - PAS のみ該当、 **[S]** - ノード AAS のみ該当、のいずれかが付いています。  

1. **[A]** ホスト名解決を設定します DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。  

   ```
   sudo vi /etc/hosts
   ```

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します。

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** sapmnt ディレクトリを作成します sapmnt ディレクトリを作成します。
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS クライアントとその他の要件をインストールします  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** マウント エントリを追加します  
   NFSv3 を使用する場合:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv 4.1 を使用する場合:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   新しい共有をマウントします

   ```
   sudo mount -a
   ```

1. **[P]** PAS ディレクトリを作成してマウントします  
   NFSv3 を使用する場合:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv 4.1 を使用する場合:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** AAS ディレクトリを作成してマウントします  
   NFSv3 を使用する場合:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv 4.1 を使用する場合:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** スワップ ファイルを構成します
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   エージェントを再起動して変更をアクティブにします

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>データベースのインストール

この例では、SAP HANA に SAP NetWeaver がインストールされます。 このインストールではサポートされているすべてのデータベースを使用できます。 Azure への SAP HANA のインストール方法の詳細については、「[High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]」 (Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性) をご覧ください。

1. SAP データベース インスタンスのインストールを実行します

   root として SAP NetWeaver データベース インスタンスをインストールします。その際、データベースのロード バランサー フロントエンド構成の IP アドレスに対応する仮想ホスト名を使用します。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver アプリケーション サーバーのインストール

次の手順に従って、SAP アプリケーション サーバーをインストールします。

1. アプリケーション サーバーを準備します

   前述の「[SAP NetWeaver アプリケーション サーバーの準備](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)」の章の手順に従って、アプリケーション サーバーを準備します。

1. SAP NetWeaver アプリケーション サーバーをインストールします

   プライマリまたは追加の SAP NetWeaver アプリケーション サーバーをインストールします。

   sapinst パラメーターの SAPINST_REMOTE_ACCESS_USER を使用すると、root 以外のユーザーが sapinst に接続することを許可できます。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA Secure Store を更新します

   SAP HANA システム レプリケーション セットアップの仮想名を指すように SAP HANA Secure Store を更新します。

   次のコマンドを実行して、エントリを \<sapsid>adm として一覧表示します

   ```
   hdbuserstore List
   ```

   これにより、次のようにすべてのエントリが一覧表示されます
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   出力には、既定のエントリの IP アドレスがロード バランサーの IP アドレスではなく仮想マシンを指していることが示されます。 このエントリは、ロード バランサーの仮想ホスト名を指すように変更する必要があります。 同じポート (上の出力内の **30313**) とデータベース名 (上の出力内の **QAS**) を必ず使用してください。

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

1. ASCS インスタンスを手動で移行する

   テスト開始前のリソースの状態:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   次のコマンドを root として実行して、ASCS インスタンスを移行します。

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. ノードのクラッシュをシミュレートする

   テスト開始前のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   ASCS インスタンスが実行されているノードで、次のコマンドを root として実行します。

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   ノードの起動後の状態は、再び次のようになります。

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   次のコマンドを実行して、失敗したリソースを取り除きます。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. メッセージ サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   次のコマンドを root として実行して、メッセージ サーバーのプロセスを特定し、強制終了します。

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   メッセージ サーバーは、1 回だけ強制終了しても、`sapstart` によって再起動されます。 強制終了を複数回実行すると、Pacemaker は最終的に ASCS インスタンスを他のノードに移動します。 テスト後に、次のコマンドを root として実行して、ASCS と ERS インスタンスのリソースの状態をクリーンアップします。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. エンキュー サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   ASCS インスタンスが実行されているノードで、次のコマンドを root として実行して、エンキュー サーバーを強制終了します。

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   ASCS インスタンスがすぐに他のノードにフェールオーバーします。 ASCS インスタンスの開始後に、ERS インスタンスもフェールオーバーします。 テスト後に、次のコマンドを root として実行して、ASCS と ERS インスタンスのリソースの状態をクリーンアップします。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. エンキュー レプリケーション サーバー プロセスを強制終了する

   テスト開始前のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ERS インスタンスが実行されているノードで、次のコマンドを root として実行して、エンキュー レプリケーション サーバー プロセスを強制終了します。

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   このコマンドを 1 回だけ実行しても、プロセスは `sapstart` によって再起動されます。 複数回実行すれば、`sapstart` によるプロセスの再起動はなくなり、リソースは停止状態になります。 テスト後に、次のコマンドを root として実行して、ERS インスタンスのリソースの状態をクリーンアップします。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. エンキュー sapstartsrv プロセスを強制終了する

   テスト開始前のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ASCS が実行されているノードで、次のコマンドを root として実行します。

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   sapstartsrv プロセスは、監視の一環として Pacemaker リソース エージェントによって常に再起動される必要があります。 テスト後のリソースの状態:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>次のステップ

* [RHEL for SAP アプリケーション マルチ SID 上の Azure VM での SAP NW の HA ガイド](./high-availability-guide-rhel-multi-sid.md)
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。