---
title: RHEL での HSR と Pacemaker を使用した SAP HANA スケールアウト | Microsoft Docs
description: RHEL での HSR と Pacemaker を使用した SAP HANA スケールアウト
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: dd000ca36bb0ad586b973b2bcdc638d8992b1a73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668641"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux での SAP HANA スケールアウト システムの高可用性 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


この記事では、Azure Red Hat Enterprise Linux 仮想マシン (VM) で HANA システム レプリケーション (HSR) と Pacemaker を使用して、高可用性 SAP HANA システムをスケールアウト構成でデプロイする方法について説明します。 提示されているアーキテクチャの共有ファイル システムは [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) によって提供され、NFS 経由でマウントされます。  

構成例やインストール コマンドなどでは、HANA インスタンスは **03**、HANA システム ID は **HN1** です。 例は HANA 2.0 SP4 と Red Hat Enterprise Linux for SAP 7.6 に基づいています。 

始める前に、次の SAP のノートとホワイトペーパーを参照してください。

* SAP ノート [1928533] には次のものが含まれます。  
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* SAP ノート [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています
* SAP ノート [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP ノート [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が含まれます
* SAP ノート [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが含まれます
* SAP ノート [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が含まれます
* SAP ノート [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が含まれます
* SAP ノート [1900823]: SAP HANA ストレージ要件に関する情報が含まれます
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes):Linux に関するすべての必要な SAP ノートが含まれます
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP HANA のネットワーク要件](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux ネットワーク ガイド](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [NFS 共有上の HANA ファイル システムを使用して、Pacemaker クラスターで SAP HANA スケールアウト システム レプリケーションを構成する方法](https://access.redhat.com/solutions/5423971)
* Azure 固有の RHEL ドキュメント:
  * [Microsoft Azure で使用するために Red Hat Enterprise Linux に SAP HANA をインストールする](https://access.redhat.com/public-cloud/microsoft-azure)
  * [SAP HANA のスケールアウトとシステム レプリケーションのための Red Hat Enterprise Linux ソリューション](https://access.redhat.com/solutions/4386601)
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]
* [Azure NetApp Files のドキュメント][anf-azure-doc] 
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)

## <a name="overview"></a>概要

HANA スケールアウトのインストールで HANA の高可用性を実現する方法の 1 つは、HANA システム レプリケーションを構成し、Pacemaker クラスターでの自動フェールオーバーの許可によりソリューションを保護することです。 アクティブ ノードで障害が発生すると、クラスターは HANA リソースを他のサイトにフェールオーバーします。  
提示されている構成では、各サイトに 3 つの HANA ノードが表示されているのに加え、スプリット ブレイン シナリオを防ぐためのマジョリティ メーカー ノードが表示されています。 より多くの VM を HANA DB ノードとして含めるように、手順を調整できます。  

提示されているアーキテクチャの HANA 共有ファイル システム `/hana/shared` は、[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) によって提供されます。 これは、同じ HANA システム レプリケーション サイト内の各 HANA ノードに NFSv4.1 経由でマウントされます。 ファイル システム `/hana/data` と `/hana/log` はローカル ファイル システムであり、HANA DB ノード間で共有されません。 SAP HANA は非共有モードでインストールされます。 

> [!TIP]
> 推奨される SAP HANA のストレージ構成については、「[SAP HANA Azure VM のストレージ構成](./hana-vm-operations-storage.md)」を参照してください。   

[![HSR と Pacemaker クラスターを使用した SAP HANA スケールアウト](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

上の図では、1 つの Azure 仮想ネットワーク内に次の 3 つのサブネットが表示されており、これは次に示す SAP HANA ネットワークの推奨事項に従っています。 
* クライアント通信用 - `client` 10.23.0.0/24  
* HANA 内部のノード間通信用 - `inter` 10.23.1.128/26  
* SAP HANA システム レプリケーション用 - `hsr` 10.23.1.192/26  

`/hana/data` と `/hana/log` はローカル ディスク上に展開されるため、ストレージとの通信用に別のサブネットと仮想ネットワーク カードを展開する必要はありません。  

Azure NetApp ボリュームは別のサブネットに展開されており、[Azure NetApp Files に委任されています](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>インフラストラクチャをセットアップする

これ以降の手順は、リソース グループおよび 3 つの Azure ネットワーク サブネット (`client`、`inter`、`hsr`) を持つ Azure 仮想ネットワークが既に作成されていることを前提としています。

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure portal を使用して Linux 仮想マシンをデプロイする
1. Azure VM を展開します。  
このドキュメントに示されている構成の場合は、次の 7 台の仮想マシンを展開します。 
   - HANA レプリケーション サイト 1 の HANA DB ノードとして機能する 3 台の仮想マシン: **hana-s1-db1**、**hana-s1-db2**、**hana-s1-db3**  
   - HANA レプリケーション サイト 2 の HANA DB ノードとして機能する 3 台の仮想マシン: **hana-s2-db1**、**hana-s2-db2**、**hana-s2-db3**  
   - *マジョリティ メーカー* として機能する小規模な仮想マシン: **hana-s-mm**

   SAP DB HANA ノードとして展開される VM は、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)で公開されているように、SAP for HANA によって認定されている必要があります。 HANA DB ノードを展開するときは、[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)が選択されていることを確認してください。  
  
   マジョリティ メーカー ノードに展開する VM では SAP HANA リソースが実行されないため、小規模の VM を展開できます。 マジョリティ メーカー VM は、スプリット ブレイン シナリオで奇数個のクラスター ノードを実現するためのクラスター構成として使用されます。 今回の例では、マジョリティ メーカー VM には `client` サブネットに 1 つの仮想ネットワーク インターフェイスが必要となるだけです。        

   `/hana/data` および `/hana/log` 用のローカル マネージド ディスクを展開します。 `/hana/data` と `/hana/log` に推奨される最小ストレージ構成については、「[SAP HANA Azure VM のストレージ構成](./hana-vm-operations-storage.md)」を参照してください。

   各 VM のプライマリ ネットワーク インターフェイスを `client` 仮想ネットワーク サブネット内に展開します。  
   Azure portal 経由で VM が展開されると、ネットワーク インターフェイス名が自動的に生成されます。 ここではわかりやすくするために、自動的に生成された `client` Azure 仮想ネットワーク サブネットに接続されたプライマリ ネットワーク インターフェイスを、**hana-s1-db1-client**、**hana-s1-db2-client**、**hana-s1-db3-client** などのように呼びます。  


   > [!IMPORTANT]
   > 選択した OS が、使用している特定の VM の種類の SAP HANA に対して認定されていることを確認してください。 SAP HANA 認定 VM の種類と、その種類に対応する OS リリースの一覧については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページをご覧ください。 一覧表示されている VM の種類の詳細をクリックすると、その種類に対して SAP HANA でサポートされている OS のリリースの完全な一覧が表示されます。  
  

2. HANA DB 仮想マシンごとに 1 つずつ、6 つのネットワーク インターフェイスを `inter` 仮想ネットワーク サブネットに作成します (この例では、**hana-s1-db1-inter**、**hana-s1-db2-inter**、**hana-s1-db3-inter**、**hana-s2-db1-inter**、**hana-s2-db2-inter**、**hana-s2-db3-inter** とします)。  

3. HANA DB 仮想マシンごとに 1 つずつ、6 つのネットワーク インターフェイスを `hsr` 仮想ネットワーク サブネットに作成します (この例では、**hana-s1-db1-hsr**、**hana-s1-db2-hsr**、**hana-s1-db3-hsr**、**hana-s2-db1-hsr**、**hana-s2-db2-hsr**、**hana-s2-db3-hsr** とします)。  

4. 新しく作成した仮想ネットワーク インターフェイスを、対応する仮想マシンに接続します。  

    a. [Azure portal](https://portal.azure.com/#home) で仮想マシンに移動します。  

    b. 左側のペインで、 **[Virtual Machines]** を選択します。 仮想マシン名でフィルター処理し (たとえば **hana-s1-db1**)、その仮想マシンを選択します。  

    c. **[概要]** ペインで、 **[停止]** を選択して仮想マシンの割り当てを解除します。  

    d. **[ネットワーク]** を選択してから、ネットワーク インターフェイスを接続します。 **[ネットワーク インターフェイスの接続]** ドロップダウン リストで、`inter` および `hsr` サブネットに対して既に作成したネットワーク インターフェイスを選択します。  
    
    e. **[保存]** を選択します。 
 
    f. 残りの仮想マシン (この例では **hana-s1-db2**、**hana-s1-db3**、**hana-s2-db1**、**hana-s2-db2**、**hana-s2-db3**) に対して、手順 b から e を繰り返します。
 
    g. 今のところ、仮想マシンは停止状態のままにしておきます。 次に、新しく接続されたすべてのネットワーク インターフェイスに対して[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)を有効にします。  

5. 次の手順を行って、`inter` および `hsr` サブネット用の追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    a. [Azure portal](https://portal.azure.com/#home) で [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。  

    b. 次のコマンドを実行して、`inter` および `hsr` サブネットに接続された、追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. HANA DB 仮想マシンを起動します

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer をデプロイする

1. 標準のロード バランサーの使用をお勧めします。 標準のロード バランサーを展開するには、次の構成手順に従います。
   1. まず、フロントエンド IP プールを作成します。

      1. ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
      1. 新規のフロントエンド IP プールの名前を入力します (例: **hana-frontend**)。
      1. **[割り当て]** を **[静的]** に設定し、IP アドレスを入力します (例: **10.23.0.18**)。
      1. **[OK]** を選択します。
      1. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

   1. 次に、バックエンド プールを作成し、すべてのクラスター VM をバックエンド プールに追加します。

      1. ロードバランサーを開き、 **[backend pools]\(バックエンド プール\)** を選択し、 **[Add]\(追加\)** を選択します。
      1. 新しいバックエンド プールの名前を入力します (例: **hana-backend**)。
      1. **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。
      1. **[仮想マシン]** を選択します。
      1. SAP HANA クラスターの仮想マシンとその `client` サブネットの IP アドレスを選択します。
      1. **[追加]** を選択します。

   1. 次に、正常性プローブを作成します。

      1. ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
      1. 新しい正常性プローブの名前を入力します (例: **hana-hp**)。
      1. プロトコルとして **[TCP]** を選択し、ポート 625 **03** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
      1. **[OK]** を選択します。

   1. 次に、負荷分散規則を作成します。
   
      1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
      1. 新しいロード バランサー規則の名前を入力します (例: **hana-lb**)。
      1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**、**hana-backend**、**hana-hp**)。
      1. **[HA ポート]** を選択します。
      1. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
      1. **Floating IP を有効にします**。
      1. **[OK]** を選択します。

   > [!IMPORTANT]
   > フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。    
   
   > [!Note]
   > パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。  


   > [!IMPORTANT]
   > Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](../../../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。
   > SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) も参照してください。  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャを展開する 

`/hana/shared` ファイル システムの ANF ボリュームを展開します。 HANA システム レプリケーション サイトごとに、個別の `/hana/shared` ボリュームが必要になります。 詳細については、「[Azure NetApp Files インフラストラクチャを設定する](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)」を参照してください。

この例では、次の Azure NetApp Files ボリュームが使用されています。 

* ボリューム **HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)
* ボリューム **HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)

## <a name="operating-system-configuration-and-preparation"></a>オペレーティング システムの構成と準備

次のセクションの手順には、次の省略形のいずれかを表すプレフィックスが付いています。
* **[A]** :    すべてのノードに適用できます
* **[AH]** :   すべての HANA DB ノードに適用できます
* **[M]** :    マジョリティ メーカーノードに適用できます
* **[AH1]** :  SITE 1 のすべての HANA DB ノードに適用できます
* **[AH2]** :  SITE 2 のすべての HANA DB ノードに適用できます
* **[1]** :    HANA DB ノード 1、SITE 1 のみに適用できます
* **[2]** :    HANA DB ノード 1、SITE 2 のみに適用できます


次の手順を行って、OS の構成と準備を行います。

1. **[A]** 仮想マシン上にホスト ファイルを維持します。 すべてのサブネットのエントリを含めます。 この例では、次のエントリが `/etc/hosts` に追加されています。  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** NFS クライアント パッケージをインストールします。  

    ```yum install nfs-utils ```


3. **[AH]** HANA 用の Red Hat の構成。  

    <https://access.redhat.com/solutions/2447641> および次の SAP note で説明されているように RHEL を構成します。  
   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB:RHEL 8 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux:GCC 6.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux:GCC 7.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux:GCC 9.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>ファイル システムを準備する
### <a name="mount-the-shared-file-systems"></a>共有ファイル システムをマウントする

この例では、共有 HANA ファイル システムが Azure NetApp Files に展開され、NFSv4 経由でマウントされます。  

1. **[AH]** HANA データベース ボリュームのマウント ポイントを作成します。  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** NFS ドメイン設定を確認します。 ドメインが既定の Azure NetApp Files ドメイン (すなわち **`defaultv4iddomain.com`** ) として構成され、マッピングが **nobody** に設定されていることを確認します。  
   この手順は、Azure NetAppFiles NFSv4.1 を使用する場合にのみ必要です。  

    > [!IMPORTANT]
    > Azure NetApp Files の既定のドメイン構成 ( **`defaultv4iddomain.com`** ) と一致するように、VM 上の `/etc/idmapd.conf` に NFS ドメインを設定していることを確認します。 NFS クライアント (つまり、VM) と NFS サーバー (つまり、Azure NetApp 構成) のドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は `nobody` と表示されます。  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** `nfs4_disable_idmapping` を確認します。 これは、**Y** に設定されている必要があります。`nfs4_disable_idmapping` が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。  
   この手順は、Azure NetAppFiles NFSv4.1 を使用する場合にのみ必要です。  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   `nfs4_disable_idmapping` パラメーターを変更する方法に関する詳細については、「 https://access.redhat.com/solutions/1749883 」を参照してください。

4. **[AH1]** SITE1 HANA DB VM に共有 Azure NetApp Files ボリュームをマウントします。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** SITE2 HANA DB VM に共有 Azure NetApp Files ボリュームをマウントします。  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[AH]** 対応する `/hana/shared/` ファイル システムが、NFS プロトコル バージョン **NFSv4** を使用しているすべての HANA DB VM にマウントされていることを確認します。  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>データおよびログのローカル ファイル システムを準備する
提示されている構成では、ファイル システム `/hana/data` と `/hana/log` はマネージド ディスクに展開され、各 HANA DB VM にローカルでアタッチされます。 各 HANA DB 仮想マシンで、ローカルのデータとログのボリュームを作成する手順を実行する必要があります。 

**論理ボリューム マネージャー (LVM)** を使用してディスク レイアウトを設定します。 次の例は、各 HANA 仮想マシンに 3 つのデータ ディスクがアタッチされていて、これを使用して 2 つのボリュームを作成することを前提としています。

1. **[AH]** すべての使用できるディスクの一覧を出力します。
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   出力例:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** 使用するすべてのディスクの物理ボリュームを作成します。
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** データ ファイル用のボリューム グループを作成します。 ログ ファイル用に 1 つ、SAP HANA の共有ディレクトリ用に 1 つのボリューム グループを作成します。
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** 論理ボリュームを作成します。 
   `-i` スイッチを指定せずに `lvcreate` を使用すると、線形のボリュームが作成されます。 I/O パフォーマンスを向上させるためにストライプ ボリュームを作成し、[SAP HANA VM のストーレジ構成](./hana-vm-operations-storage.md)に関する記事に記載されている値にストライプ サイズを合わせることをお勧めします。 `-i` 引数は、基になる物理ボリュームの数、`-I` 引数はストライプ サイズにする必要があります。 このドキュメントでは、2 つの物理ボリュームが使用されるため、`-i` スイッチ引数は **2** に設定されます。 データ ボリュームのストライプ サイズは **256 KiB** です。 ログ ボリューム用に物理ボリュームが 1 つ使用されるため、ログ ボリューム コマンドに対して `-i` および `-I` スイッチは明示的には使用されません。  

   > [!IMPORTANT]
   > データまたはログ ボリュームごとに複数の物理ボリュームを使用する場合は、`-i` スイッチを使用して基になる物理ボリュームの数を設定します。 ストライプ ボリュームを作成するときにストライプ サイズを指定するには、`-I` スイッチを使用します。  
   > ストライプ サイズやディスク数など、推奨されるストレージ構成については、[SAP HANA VM ストレージ構成](./hana-vm-operations-storage.md)に関する記事を参照してください。  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** マウント ディレクトリを作成し、すべての論理ボリュームの UUID をコピーします。
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** 論理ボリュームの `fstab` エントリを作成してマウントします。
    ```bash
    sudo vi /etc/fstab
    ```

   `/etc/fstab` ファイルに次の行を挿入します。

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   新しいボリュームをマウントします。

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>インストール  

この例では、Azure VM で HSR を使用してスケールアウト構成で SAP HANA を展開するために、HANA 2.0 SP4 を使用しました。  

### <a name="prepare-for-hana-installation"></a>HANA のインストールの準備

1. **[AH]** HANA をインストールする前に、ルート パスワードを設定します。 インストールが完了した後で、ルート パスワードを無効にすることができます。 `root` として `passwd` コマンドを実行します。  

2. **[1,2]** `/hana/shared` のアクセス許可を変更します 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** パスワードの入力を求められることなく、このサイトの HANA DB VM である **hana-s1-db2** および **hana-s1-db3** に SSH 経由でログインできることを確認します。  
   そうでない場合は、[キーベースの認証の使用](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)に関するページに記載されているように、ssh キーを交換します。  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** パスワードの入力を求められることなく、このサイトの HANA DB VM である **hana-s2-db2** および **hana-s2-db3** に SSH 経由でログインできることを確認します。  
   そうでない場合は、[キーベースの認証の使用](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)に関するページに記載されているように、ssh キーを交換します。  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** HANA 2.0 SP4 に必要な追加のパッケージをインストールします。 詳細については、RHEL 7 向けの SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) を参照してください。 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** HANA のインストールに干渉しないように、ファイアウォールを一時的に無効にします。 HANA のインストールが完了したら、再度有効にすることができます。 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>各サイトの最初のノードへの HANA のインストール

1. **[1]** 「[SAP HANA 2.0 のインストールと更新ガイド](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)」の指示に従って、SAP HANA をインストールします。 次の手順では、SITE 1 の最初のノードへの SAP HANA のインストールを示します。   

   a. HANA のインストール ソフトウェア ディレクトリから、**hdblcm** プログラムを `root` で起動します。 `internal_network` パラメーターを使用して、内部 HANA のノード間通信に使用されるサブネットのアドレス空間を渡します。  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. プロンプトで次の値を入力します。

     * **[Choose an action]\(アクションを選択する\)** : 「**1**」と入力します (インストールの場合)。
     * **[Additional components for installation]\(追加でインストールするコンポーネント\)** : 「**2, 3**」と入力します。
     * [installation path]\(インストール パス\): Enter キーを押します (既定値は /hana/shared)
     * **[Local Host Name]\(ローカル ホスト名\)** : Enter キーを押して既定値をそのまま使用します
     * **[Do you want to add hosts to the system?]\(システムにホストを追加しますか?\)** : 「**n**」と入力します
     * **[SAP HANA System ID]\(SAP HANA システム ID\)** : 「**HN1**」と入力します
     * **[Instance number]\(インスタンス番号\)** [00]: 「**03**」と入力します
     * **[Local Host Worker Group]\(ローカル ホスト ワーカー グループ\)** [既定値]: Enter キーを押して既定値をそのまま使用します
     * **[Select System Usage / Enter index [4]\(システム用途の選択/インデックスを入力 [4]\)** \: 「**4**」と入力します (カスタム)
     * **[Location of Data Volumes]\(データ ボリュームの場所\)** [/hana/data/HN1]: Enter キーを押して既定値をそのまま使用します
     * **[Location of Log Volumes]\(ログ ボリュームの場所\)** [/hana/log/HN1]: Enter キーを押して既定値をそのまま使用します
     * **[Restrict maximum memory allocation?]\(メモリの最大割り当てを制限しますか?\)** [n]: 「**n**」と入力します
     * **[Certificate Host Name For Host hana-s1-db1]\(ホスト hana-s1-db1 の証明書ホスト名\)** [hana-s1-db1]: Enter キーを押して既定値をそのまま使用します
     * **[SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワード\)** : パスワードを入力します
     * **[Confirm SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードの確認\)** : パスワードを入力します
     * **[System Administrator (hn1adm) Password]\(システム管理者 (hn1adm) のパスワード\)** : パスワードを入力します
     * **[System Administrator Home Directory]\(システム管理者のホーム ディレクトリ\)** [/usr/sap/HN1/home]: Enter キーを押して既定値をそのまま使用します
     * **[System Administrator Login Shell]\(システム管理者のログイン シェル\)** [/bin/sh]: Enter キーを押して既定値をそのまま使用します
     * **[System Administrator User ID]\(システム管理者のユーザー ID\)** [1001]: Enter キーを押して既定値をそのまま使用します
     * **[Enter ID of User Group (sapsys)]\(ユーザー グループ (sapsys) の ID を入力\)** [79]: Enter キーを押して既定値をそのまま使用します
     * **[System Database User (system) Password]\(システム データベース ユーザー (system) のパスワード\)** : システムのパスワードを入力します
     * **[Confirm System Database User (system) Password]\(システム データベース ユーザー (system) のパスワードの確認\)** : システムのパスワードを入力します
     * **[Restart system after machine reboot?]\(コンピューターの再起動後にシステムを再起動しますか?\)** [n]: 「**n**」と入力します 
     * **[Do you want to continue (y/n)]\(続行しますか? (y/n)\)** : 概要を検証し、すべて問題がなさそうな場合は「**y**」と入力します

2. **[2]** 上記の手順を繰り返して、SITE 2 の最初のノードに SAP HANA をインストールします。   

3. **[1,2]** global.ini を確認します  

   global.ini を表示し、内部 SAP HANA のノード間通信が正しく構成されていることを確認します。 **communication** セクションを確認します。 `inter` サブネットに対するアドレス空間があり、`listeninterface` が `.internal` に設定されている必要があります。 **internal_hostname_resolution** セクションを確認します。 `inter` サブネットに属する HANA 仮想マシンの IP アドレスが含まれている必要があります。  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1,2]** SAP note [2080991](https://launchpad.support.sap.com/#/notes/0002080991) の説明に従って、共有されていない環境でのインストールのための `global.ini` を準備します。  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** SAP HANA を再起動して、変更をアクティブにします。  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** クライアント インターフェイスが `client` サブネットの IP アドレスを使用して通信するようになっていることを確認します。  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   構成を確認する方法については、SAP ノート 「[2183363 - SAP HANA 内部ネットワークの構成](https://launchpad.support.sap.com/#/notes/2183363)」を参照してください。  

7. **[AH]** HANA のインストール エラーを回避するために、データ ディレクトリとログ ディレクトリのアクセス許可を変更します。  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** セカンダリ HANA ノードをインストールします。 このステップでは、例として SITE 1 での手順を示します。  
   a. 常駐の **hdblcm** プログラムを `root` で開始します。    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. プロンプトで次の値を入力します。

     * **[Choose an action]\(アクションを選択する\)** : 「**2**」と入力します (ホストの追加)
     * **[Enter comma separated host names to add]\(追加するホストの名前をコンマ区切りで入力\)** : hana-s1-db2, hana-s1-db3
     * **[Additional components for installation]\(追加でインストールするコンポーネント\)** : 「**2, 3**」と入力します。
     * **[Enter Root User Name]\(ルート ユーザー名を入力\) [root]** : Enter キーを押して既定値をそのまま使用します
     * **[Select roles for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のロールを選択\) [1]** :1 (worker)
     * **[Enter Host Failover Group for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のホスト フェールオーバー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します
     * **[Enter Storage Partition Number for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のストレージ パーティション番号を入力\) [<<assign automatically>>]** : Enter キーを押して既定値をそのまま使用します
     * **[Enter Worker Group for host 'hana-s1-db2']\(ホスト 'hana-s1-db2' のワーカー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します
     * **[Select roles for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のロールを選択\) [1]** :1 (worker)
     * **[Enter Host Failover Group for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のホスト フェールオーバー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します
     * **[Enter Storage Partition Number for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のストレージ パーティション番号を入力\) [<<assign automatically>>]** : Enter キーを押して既定値をそのまま使用します
     * **[Enter Worker Group for host 'hana-s1-db3']\(ホスト 'hana-s1-db3' のワーカー グループを入力\) [既定値]** : Enter キーを押して既定値をそのまま使用します
     * **[System Administrator (hn1adm) Password]\(システム管理者 (hn1adm) のパスワード\)** : パスワードを入力します
     * **[Enter SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードを入力\)** : パスワードを入力します
     * **[Confirm SAP Host Agent User (sapadm) Password]\(SAP ホスト エージェント ユーザー (sapadm) のパスワードの確認\)** : パスワードを入力します
     * **[Certificate Host Name For Host hana-s1-db2]\(ホスト hana-s1-db2 の証明書ホスト名\)** [hana-s1-db2]: Enter キーを押して既定値をそのまま使用します
     * **[Certificate Host Name For Host hana-s1-db3]\(ホスト hana-s1-db3 の証明書ホスト名\)** [hana-s1-db3]: Enter キーを押して既定値をそのまま使用します
     * **[Do you want to continue (y/n)]\(続行しますか? (y/n)\)** : 概要を検証し、すべて問題がなさそうな場合は「**y**」と入力します

9. **[2]** 上記の手順を繰り返して、SITE 2 にセカンダリ SAP HANA ノードをインストールします。   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 システム レプリケーションの構成

1. **[1]** SITE 1 でシステム レプリケーションを構成します。

   **hn1** adm としてデータベースをバックアップします。

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   システム PKI ファイルをセカンダリ サイトにコピーします。

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   プライマリ サイトを作成します。

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** SITE 2 でシステム レプリケーションを構成します。
    
   2 番目のサイトを登録して、システム レプリケーションを開始します。 <hanasid\>adm として次のコマンドを実行します。

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** レプリケーションの状態をチェックする

   レプリケーションの状態をチェックし、すべてのデータベースが同期されるまで待機します。

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** HANA システム レプリケーション仮想ネットワーク インターフェイスを通じて、HANA システム レプリケーションの通信が行われるように、HANA の構成を変更します。   
   - 両方のサイトで HANA を停止します
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - global.ini を編集して、HANA システム レプリケーションのホスト マッピングを追加します。`hsr` サブネットの IP アドレスを使用します。  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - 両方のサイトで HANA を開始します
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   詳細については、[システム レプリケーションのホスト名前解決](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)に関するページを参照してください。  

5. **[AH]** ファイアウォールを再度有効にします。  
   - ファイアウォールを再度有効にする
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - 必要なファイアウォール ポートを開きます。 HANA インスタンス番号に合わせてポートを調整する必要があります。  

       > [!IMPORTANT]
       > HANA のノード間通信とクライアント トラフィックを許可するファイアウォール規則を作成します。 必要なポートは、[すべての SAP 製品の TCP/IP ポート](https://help.sap.com/viewer/ports)のページにあります。 次にコマンドは 1 つの例にすぎません。 このシナリオでは、システム番号 03 が使用されています。

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md)」 (Azure で Red Hat Enterprise Linux に Pacemaker を設定する) の手順に従って、この HANA サーバーに対して基本的な Pacemaker クラスターを作成します。
クラスターのマジョリティ メーカーを含む、すべての仮想マシンを含めます。  

> [!IMPORTANT]
> 2 ノード クラスターではないため、`quorum expected-votes` を 2 に設定しないでください。  
> ノード フェンシングが逆シリアル化されるように、クラスター プロパティ `concurrent-fencing` が有効になっていることを確認します。   

## <a name="create-file-system-resources"></a>ファイル システム リソースの作成

1. **[1,2]** 両方のレプリケーション サイトで SAP HANA を停止します。 <sid\>adm として実行します。  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[AH]** インストールのためにすべての HANA DB VM に一時的にマウントされていた、ファイル システム `/hana/shared` をマウント解除します。 マウントを解除する前に、ファイル システムを使用しているプロセスとセッションを停止する必要があります。 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** 無効な状態の `/hana/shared` 用のファイル システム クラスター リソースを作成します。 リソースは `--disabled` オプションを使用して作成されます。これは、マウントを有効にする前に場所の制約を定義する必要があるためです。  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   監視操作に `OCF_CHECK_LEVEL=20` 属性を追加して、監視操作でファイル システムの読み取り/書き込みテストを実行できるようにします。 この属性がない場合、監視操作ではファイル システムがマウントされていることのみを確認します。 接続が失われると、アクセス不可能であるにもかかわらずファイル システムがマウントされたままになる場合があるため、これは問題になる可能性があります。  

   `on-fail=fence` 属性も監視操作に追加されます。 このオプションを使用すると、ノードで監視操作が失敗した場合、そのノードはすぐにフェンスされます。 このオプションがない場合、既定の動作では、障害が発生したリソースに依存するすべてのリソースを停止し、障害が発生したリソースを再起動してから、障害が発生したリソースに依存するすべてのリソースを起動します。 障害が発生したリソースに SAPHana リソースが依存している場合、この動作は時間がかかる可能性があるだけでなく、完全に失敗する可能性もあります。 HANA バイナリを保持している NFS 共有にアクセスできない場合、SAPHana リソースは正常に停止できません。  

4. **[1]** ノード属性を構成して検証します。 レプリケーション サイト 1 のすべての SAP HANA DB ノードには属性 `S1` が割り当てられ、レプリケーション サイト 2 のすべての SAP HANA DB ノードには属性 `S2` が割り当てられます。  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** NFS ファイル システムをマウントする場所を決定する制約を構成し、ファイル システム リソースを有効にします。  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   ファイル システム リソースを有効にすると、クラスターによって `/hana/shared` ファイル システムがマウントされます。
 
6. **[AH]** 両方のサイトのすべての HANA DB VM で、`/hana/shared` に ANF ボリュームがマウントされていることを確認します。

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** 属性リソースを構成します。 `hana/shared` の NFS マウントがマウントされている場合に、属性を `true` に設定する制約を構成します。  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 構成に NFS でマウントされている /`hana/shared` 以外の他のファイル システムが含まれている場合は、ファイル システム間に順序の依存関係がなくなるよう、`sequential=false` オプションを含めます。 NFS でマウントされたすべてのファイル システムは、対応する属性リソースの前に起動する必要がありますが、互いに決まった順序で起動する必要はありません。 詳細については、[HANA ファイル システムが NFS 共有である場合の、Pacemaker クラスターでの SAP HANA スケールアウト HSR の構成方法](https://access.redhat.com/solutions/5423971)に関するページを参照してください。  

8. **[1]** HANA クラスター リソースの作成準備として、pacemaker をメンテナンス モードにします。  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA クラスター リソースの作成

1. **[A]** マジョリティ メーカーを含むすべてのクラスター ノードに HANA スケールアウト リソース エージェントをインストールします。    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > お使いの OS リリースでサポートされている最小バージョンのパッケージ `resource-agents-sap-hana-scaleout` については、[RHEL HA クラスターのサポート ポリシー - クラスターでの SAP HANA の管理](https://access.redhat.com/articles/3397471)に関するページを参照してください。  

2. **[1,2]** HANA "システム レプリケーション フック" をインストールします。 フックは、各システム レプリケーション サイトの HANA DB ノードの 1 つにインストールする必要があります。 SAP HANA は、まだ停止している必要があります。        

   1. フックを `root` で準備します 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. `global.ini` を調整します
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[AH]** クラスターでは、<sid\>adm のクラスター ノードで sudoers 構成が必要です。 この例では、新しいファイルを作成することで実現します。 `root` としてコマンドを実行します。    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1,2]** 両方のレプリケーション サイトで SAP HANA を開始します。 <sid\>adm として実行します。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** フックのインストールを確認します。 アクティブな HANA システム レプリケーション サイトで、<sid\>adm として実行します。   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** HANA クラスター リソースを作成します。 次のコマンドを `root` で実行します。    
   1. クラスターが既にメンテナンス モードになっていることを確認します。  
    
   2. 次に、HANA トポロジ リソースを作成します。  
      RHEL **7.x** クラスターを構築する場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      RHEL **8.x** クラスターを構築する場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. 次に、HANA インスタンス リソースを作成します。  
      > [!NOTE]
      > この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。  
 
      RHEL **7.x** クラスターを構築する場合は、次のコマンドを使用します。    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      RHEL **8.x** クラスターを構築する場合は、次のコマンドを使用します。  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > 失敗したプライマリ インスタンスが自動的にセカンダリとして登録されるのを防ぐために、完全なフェールオーバー テストを実行している間、AUTOMATED_REGISTER を **no** に設定することをベスト プラクティスとしてお勧めします。 フェールオーバー テストが正常に完了したら、AUTOMATED_REGISTER を **yes** に設定します。こうすることで、引き継ぎ後のシステム レプリケーションが自動的に再開されるようになります。 

   4. 仮想 IP と関連するリソースを作成します。  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. クラスターの制約を作成します  
      RHEL **7.x** クラスターを構築する場合は、次のコマンドを使用します。  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      RHEL **8.x** クラスターを構築する場合は、次のコマンドを使用します。  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** クラスターのメンテナンス モードを解除します。 クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 上記の構成のタイムアウトはほんの一例であり、特定の HANA のセットアップに適合させる必要がある場合があります。 たとえば、SAP HANA データベースの起動により時間がかかる場合は、開始タイムアウトを長くする必要がある可能性があります。
  
## <a name="test-sap-hana-failover"></a>SAP HANA フェールオーバーのテスト 

1. テストを開始する前に、クラスターと SAP HANA システムのレプリケーション状態を確認します。  

   a. 失敗したクラスター アクションがないことを確認します  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. SAP HANA システム レプリケーションが同期されていることを確認します

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. ノードが NFS 共有 (`/hana/shared`) へのアクセスを失ったときの障害シナリオに備えてクラスター構成を検証します。  

   SAP HANA リソース エージェントでは、フェールオーバー中の操作の実行は `/hana/shared` に保存されるバイナリに依存しています。 提示されている構成では、ファイル システム `/hana/shared` は NFS 経由でマウントされています。 実行できるテストは、`/hana/shared` ファイル システムを "*読み取り専用*" として再マウントすることです。 この方法では、アクティブなシステム レプリケーション サイトで `/hana/shared` へのアクセスが失われた場合に、クラスターがフェールオーバーされることを検証します。  

   **予測される結果**: `/hana/shared` を "*読み取り専用*" として再マウントすると、ファイル システムに対して読み取り/書き込み操作を実行する監視操作は失敗します。これは、ファイル システムへの書き込みができないためです。そして、HANA リソースのフェールオーバーがトリガーされます。 HANA ノードが NFS 共有へのアクセスを失った場合も、同じ結果が予想されます。  
     
   クラスター リソースの状態を確認するには、`crm_mon` または `pcs status` を実行します。 テスト開始前のリソースの状態:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   プライマリ レプリケーション サイトの VM のいずれかで `/hana/shared` の失敗をシミュレートするには、次のコマンドを実行します。
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   `/hana/shared` へのアクセスが失われた HANA VM は、クラスター構成に応じて再起動または停止する必要があります。 クラスター リソースは、他の HANA システム レプリケーション サイトに移行されます。  
         
   再起動された VM でクラスターが開始されていない場合は、次を実行してクラスターを開始します。 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   クラスターが起動すると、ファイル システム `/hana/shared` が自動的にマウントされます。     
   AUTOMATED_REGISTER="false" に設定した場合は、セカンダリ サイトで SAP HANA システム レプリケーションを構成する必要があります。 この場合、次のコマンドを実行して、SAP HANA をセカンダリとして再構成することができます。   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   テスト後のリソースの状態は次のようになります。 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


[RHEL 上の Azure VM の SAP HANA の高可用性](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)に関するページに記載されているテストも行い、SAP HANA クラスター構成を十分にテストすることをお勧めします。


## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。