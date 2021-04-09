---
title: SLES で Azure NetApp Files を使用したスタンバイでの SAP HANA スケールアウト | Microsoft Docs
description: SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする方法について説明します。
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
ms.openlocfilehash: d7b6f1d7ed01009af5e21006240a21442492cb07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673638"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


この記事では、共有ストレージ ボリューム用の [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) を使用して、Azure 仮想マシン (VM) 上のスタンバイを使用するスケールアウト構成に高可用性の SAP HANA システムをデプロイする方法について説明します。  

構成例やインストール コマンドなどでは、HANA インスタンスは **03**、HANA システム ID は **HN1** です。 例は HANA 2.0 SP4 と SUSE Linux Enterprise Server for SAP 12 SP4 に基づいています。 

始める前に、次の SAP のノートとホワイトペーパーを参照してください。

* [Azure NetApp Files のドキュメント][anf-azure-doc] 
* SAP ノート [1928533] には次のものが含まれます。  
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* SAP ノート [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています
* SAP ノート [2205917]: SUSE Linux Enterprise Server for SAP Applications 向けの推奨の OS 設定が含まれます
* SAP ノート [1944799]: SUSE Linux Enterprise Server for SAP Applications の SAP ガイドラインが含まれます
* SAP ノート [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が含まれます
* SAP ノート [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが含まれます
* SAP ノート [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が含まれます
* SAP ノート [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が含まれます
* SAP ノート [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が含まれます
* SAP ノート [1900823]: SAP HANA ストレージ要件に関する情報が含まれます
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes):Linux に関するすべての必要な SAP ノートが含まれます
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SUSE SAP HA ベスト プラクティス ガイド][suse-ha-guide]: NetWeaver の高可用性および SAP HANA システム レプリケーションをオンプレミスに設定するために必要なすべての情報が含まれています (一般的なベースラインとして使用します。より詳細な情報が提供されます)
* [SUSE High Availability Extension 12 SP3 リリース ノート][suse-ha-12sp3-relnotes]
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)

## <a name="overview"></a>概要

HANA の高可用性を実現するための 1 つの方法は、ホストの自動フェールオーバーを構成することです。 ホストの自動フェールオーバーを構成するには、1 つ以上の仮想マシンを HANA システムに追加し、スタンバイ ノードとして構成します。 アクティブ ノードで障害が発生すると、スタンバイ ノードに自動的に引き継がれます。 提示されている Azure 仮想マシンの構成では、[Azure NetApp Files 上の NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md) を使用して自動フェールオーバーを実現します。  

> [!NOTE]
> スタンバイ ノードは、すべてのデータベース ボリュームにアクセスする必要があります。 HANA ボリュームは NFSv4 ボリュームとしてマウントする必要があります。 NFSv4 プロトコルでは、強化されたファイル リースベースのロック メカニズムが `I/O` フェンスに使用されます。 

> [!IMPORTANT]
> サポートされている構成を構築するには、HANA のデータ ボリュームとログ ボリュームを NFSv4.1 ボリュームとしてデプロイし、NFSv4.1 プロトコルを使用してそれらをマウントする必要があります。 スタンバイ ノードを使用した HANA ホストの自動フェールオーバー構成は、NFSv3 ではサポートされていません。

![SAP NetWeaver の高可用性の概要](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

上の図は SAP HANA ネットワークに関する推奨事項に従っており、次の 3 つのサブネットが 1 つの Azure 仮想ネットワーク内に表示されています。 
* クライアント通信用
* ストレージ システムとの通信用
* HANA 内部のノード間通信用

Azure NetApp ボリュームは、[Azure NetApp Files に委任された](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)別のサブネットにあります。  

この構成例では、サブネットは次のとおりです。  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files インフラストラクチャを設定する 

Azure NetApp Files インフラストラクチャの設定を続行する前に、「[Azure NetApp Files のドキュメント][anf-azure-doc]」の内容をよく理解しておいてください。 

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 選択した Azure リージョンで Azure NetApp Files が提供されているかどうかを確認してください。  

Azure リージョン別に Azure NetApp Files を利用できるかどうかについては、[NetApp Files を利用できる Azure リージョン][anf-avail-matrix]に関するページをご覧ください。  

Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する][anf-register]」の手順に従って、Azure NetApp Files へのオンボードを要求してください。 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ  

以下の手順では、お使いの [Azure 仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)が既にデプロイされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内にデプロイする必要があります。  

1. リソースをまだデプロイしていない場合は、[Azure NetApp Files へのオンボード](../../../azure-netapp-files/azure-netapp-files-register.md)を要求してください。  

2. 「[NetApp アカウントを作成する](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)」の手順に従って、選択した Azure リージョンに NetApp アカウントを作成します。  

3. [Azure NetApp Files の容量プールの設定](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)に関するページの手順に従って、Azure NetApp Files の容量プールを設定します。  

   この記事で示されている HANA アーキテクチャでは、"*Ultra サービス*" レベルで 1 つの Azure NetApp Files 容量プールが使用されています。 Azure 上の HANA ワークロードの場合、Azure NetApp Files の *Ultra* または *Premium* [サービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)を使用することをお勧めします。  

4. 「[サブネットを Azure NetApp Files に委任する](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)」の手順に従って、サブネットを Azure NetApp Files に委任します。  

5. 「[Azure NetApp Files の NFS ボリュームを作成する](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)」の手順に従って、Azure NetApp Files のボリュームをデプロイします。  

   ボリュームをデプロイするときは、**NFSv4.1** バージョンを必ず選択してください。 現在のところ、NFSv4.1 は許可リストに追加する必要があります。 指定された Azure NetApp Files の[サブネット](/rest/api/virtualnetwork/subnets)内にボリュームをデプロイします。 Azure NetApp ボリュームの IP アドレスは、自動的に割り当てられます。 
   
   Azure NetApp Files のリソースと Azure VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内に配置する必要があることに注意してください。 たとえば、**HN1**-data-mnt00001、**HN1**-log-mnt00001 などはボリューム名で、nfs://10.23.1.5/**HN1**-data-mnt00001、nfs://10.23.1.4/**HN1**-log-mnt00001 などは Azure NetApp Files ボリュームのファイル パスです。  

   * ボリューム **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * ボリューム **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * ボリューム **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * ボリューム **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * ボリューム **HN1**-shared (nfs://10.23.1.4/**HN1**-shared)
   
   この例では、HANA データとログ ボリュームごとに個別の Azure NetApp Files ボリュームが使用されています。 小規模または非運用システムでよりコストを最適化した構成にするために、すべてのデータ マウントとすべてのログ マウントを 1 つのボリュームに配置することができます。  

### <a name="important-considerations"></a>重要な考慮事項

SUSE High Availability アーキテクチャ上で SAP NetWeaver 用に Azure NetApp Files を作成するときは、以下の重要な考慮事項に注意してください。

- 最小容量のプールは 4 テビバイト (TiB) です。  
- 最小ボリューム サイズは 100 ギビバイト (GiB) です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)内に存在する必要があります。  
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp Files ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」に記載されているように、ボリューム クォータとサービス レベルの機能です。 HANA Azure NetApp ボリュームのサイズを設定するときは、そのスループットが HANA システム要件を満たしていることを確認してください。  
- Azure NetApp Files の[エクスポート ポリシー](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)では、ユーザーが制御できるのは、許可されたクライアントと、アクセスの種類 (読み取りと書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、その機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。  
-  

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files 上の HANA データベースのサイズ指定

Azure NetApp Files ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」に記載されているように、ボリューム サイズとサービス レベルの機能です。 

Azure で SAP 用のインフラストラクチャを設計するときは、SAP による最小ストレージ要件に注意する必要があります。これは、最小スループット特性につながります。

- /hana/log では、1 MB の I/O サイズで、毎秒 250 メガバイト (MB/秒) の読み取り/書き込みを有効にします。  
- /hana/data では、16 MB および 64 MB の I/O サイズで、400 MB/秒以上の読み取りアクティビティを有効にします。  
- /hana/data では、16 MB および 64 MB の I/O サイズで、250 MB/秒以上の書き込みアクティビティを有効にします。 

ボリューム クォータの 1 TiB あたりの [Azure NetApp Files スループットの上限](../../../azure-netapp-files/azure-netapp-files-service-levels.md)は次のとおりです。
- Premium Storage 層 - 64 MiB/秒  
- Ultra Storage 層 - 128 MiB/秒  

データとログに対する SAP の最小スループット要件、および /hana/shared のガイドラインを満たすため、推奨されるサイズは次のようになります。

| ボリューム | サイズ<br>Premium ストレージ層 | サイズ<br>Ultra ストレージ層 | サポートされる NFS プロトコル |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) | v3 または v4.1 |

Azure NetApp Files Ultra ストレージ層を使用している、この記事に記載されているレイアウトの SAP HANA 構成は、次のようになります。

| ボリューム | サイズ<br>Ultra ストレージ層 | サポートされる NFS プロトコル |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 または v4.1 |

> [!NOTE]
> ここで説明されている Azure NetApp Files のサイズ設定の推奨事項は、SAP がインフラストラクチャ プロバイダーに対して推奨している最小要件を満たすことを目標としています。 実際の顧客のデプロイとワークロードのシナリオでは、これらのサイズでは十分ではない場合があります。 これらの推奨事項は開始点として利用し、ご自分の固有のワークロードの要件に合わせて調整してください。  

> [!TIP]
> Azure NetApp Files ボリュームは、ボリュームを "*マウント解除*" したり、仮想マシンを停止したり、SAP HANA を停止したりする必要なく、動的にサイズ変更することができます。 この方法により、アプリケーションの予想されるスループット要求と予期しないスループット要求の両方を柔軟に満たすことができます。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure portal を使用して Linux 仮想マシンをデプロイする

最初に Azure NetApp Files ボリュームを作成する必要があります。 その後、次の手順を行います。
1. お使いの [Azure 仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)に [Azure 仮想ネットワーク サブネット](../../../virtual-network/virtual-network-manage-subnet.md)を作成します。 
1. VM をデプロイします。 
1. 追加のネットワーク インターフェイスを作成し、対応する VM にネットワーク インターフェイスを接続します。  

   各仮想マシンには、3 つの Azure 仮想ネットワーク サブネット (`client`、`storage`、`hana`) に対応する 3 つのネットワーク インターフェイスが備わっています。 

   詳しくは、[複数のネットワーク インターフェイス カードを使用して Linux 仮想マシンを Azure に作成する](../../linux/multiple-nics.md)方法に関するページをご覧ください。  

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 待ち時間の短縮を実現するには、Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが近接してデプロイされるようにします。 SAP HANA Azure NetApp Files を使用している[新しい SAP HANA システムをオンボードする](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)ときに、必要な情報を送信します。 
 
次の手順は、リソース グループ、Azure 仮想ネットワーク、3 つの Azure 仮想ネットワーク サブネット (`client`、`storage`、`hana`) が既に作成されていることを前提としています。 VM をデプロイするときに、クライアント ネットワーク インターフェイスが VM のプライマリ インターフェイスになるように、クライアント サブネットを選択します。 ストレージ サブネット ゲートウェイ経由で Azure NetApp Files の委任されたサブネットへの明示的なルートを構成する必要もあります。 

> [!IMPORTANT]
> 選択した OS が、使用している特定の VM の種類の SAP HANA に対して認定されていることを確認してください。 SAP HANA 認定 VM の種類と、その種類に対応する OS リリースの一覧については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページをご覧ください。 一覧表示されている VM の種類の詳細をクリックすると、その種類に対して SAP HANA でサポートされている OS のリリースの完全な一覧が表示されます。  

1. SAP HANA 用の可用性セットを作成します。 必ず、更新ドメインの最大数を設定してください。  

2. 次の手順を行って、3 つの仮想マシン (**hanadb1**、**hanadb2**、**hanadb3**) を作成します。  

   a. SAP HANA でサポートされている SLES4SAP イメージを、Azure ギャラリーから使用します。 この例では、SLES4SAP 12 SP4 イメージが使用されています。  

   b. 前に作成した SAP HANA 用の可用性セットを選択します。  

   c. クライアント Azure 仮想ネットワーク サブネットを選択します。 [高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)を選択します。  

   仮想マシンをデプロイすると、ネットワーク インターフェイス名が自動的に生成されます。 これらの手順をわかりやすくするために、クライアント Azure 仮想ネットワーク サブネット (**hanadb1-client**、**hanadb2-client**、**hanadb3-client**) に接続されている自動的に生成されたネットワーク インターフェイスについて説明します。 

3. `storage` 仮想ネットワーク サブネットに対して、仮想マシンごとに 1 つずつ、3 つのネットワーク インターフェイスを作成します (この例では、**hanadb1-storage**、**hanadb2-storage**、**hanadb3-storage**)。  

4. `hana` 仮想ネットワーク サブネットに対して、仮想マシンごとに 1 つずつ、3 つのネットワーク インターフェイスを作成します (この例では、**hanadb1-hana**、**hanadb2-hana**、**hanadb3-hana**)。  

5. 次の手順を行って、新しく作成した仮想ネットワーク インターフェイスを対応する仮想マシンに接続します。  

    a. [Azure portal](https://portal.azure.com/#home) で仮想マシンに移動します。  

    b. 左側のペインで、 **[Virtual Machines]** を選択します。 仮想マシン名でフィルター処理し (たとえば、**hanadb1**)、仮想マシンを選択します。  

    c. **[概要]** ペインで、 **[停止]** を選択して仮想マシンの割り当てを解除します。  

    d. **[ネットワーク]** を選択してから、ネットワーク インターフェイスを接続します。 **[ネットワーク インターフェイスの接続]** ドロップダウン リストで、`storage` および `hana` サブネットに対して既に作成したネットワーク インターフェイスを選択します。  
    
    e. **[保存]** を選択します。 
 
    f. 残りの仮想マシンについて、ステップ b から e を繰り返します (この例では **hanadb2** と **hanadb3**)。
 
    g. 今のところ、仮想マシンは停止状態のままにしておきます。 次に、新しく接続されたすべてのネットワーク インターフェイスに対して[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)を有効にします。  

6. 次の手順を行って、`storage` および `hana` サブネット用の追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    a. [Azure portal](https://portal.azure.com/#home) で [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。  

    b. 次のコマンドを実行して、`storage` および `hana` サブネットに接続された、追加のネットワーク インターフェイスに対して高速ネットワークを有効にします。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 次の手順を行って、仮想マシンを起動します。  

    a. 左側のペインで、 **[Virtual Machines]** を選択します。 仮想マシン名でフィルター処理し (たとえば、**hanadb1**)、仮想マシンを選択します。  

    b. **[概要]** ウィンドウで、 **[開始]** を選択します。  

## <a name="operating-system-configuration-and-preparation"></a>オペレーティング システムの構成と準備

次のセクションの手順の前には、次のいずれかが付いています。
* **[A]** :すべてのノードに適用できます
* **[1]** :ノード 1 にのみ適用できます
* **[2]** :ノード 2 にのみ適用できます
* **[3]** : ノード 3 にのみ適用できます

次の手順を行って、OS の構成と準備を行います。

1. **[A]** 仮想マシン上にホスト ファイルを維持します。 すべてのサブネットのエントリを含めます。 この例では、次のエントリが `/etc/hosts` に追加されています。  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** ストレージ用のネットワーク インターフェイスに対する DHCP とクラウドの構成設定を変更して、意図しないホスト名の変更を回避します。  

    次の手順は、ストレージ ネットワーク インターフェイスが `eth1` であることを前提としています。 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** ネットワーク ルートを追加して、Azure NetApp Files への通信がストレージ ネットワーク インターフェイス経由で行われるようにします。  

    次の手順は、ストレージ ネットワーク インターフェイスが `eth1` であることを前提としています。  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    VM を再起動して、変更をアクティブにします。  

3. **[A]** 「[Azure NetApp Files を使用した Microsoft Azure 上での NetApp SAP アプリケーション][anf-sap-applications-azure]」で説明されているように、NFS を使用して NetApp システムで SAP HANA を実行できるよう OS を準備します。 NetApp 構成設定用の構成ファイル */etc/sysctl.d/netapp-hana.conf* を作成します。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Azure 用の Microsoft の構成設定を使用して、構成ファイル */etc/sysctl.d/ms-az.conf* を作成します。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> SAP ホスト エージェントからポート範囲を管理できるように、sysctl 構成ファイルで明示的に net.ipv4.ip_local_port_range と net.ipv4.ip_local_reserved_ports を設定しないようにしてください。 詳細については、SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) を参照してください。  

4. **[A]** 「[Azure NetApp Files を使用した Microsoft Azure 上での NetApp SAP アプリケーション][anf-sap-applications-azure]」で推奨されているように、sunrpc 設定を調整します。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files ボリュームのマウント

1. **[A]** HANA データベース ボリュームのマウント ポイントを作成します。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **HN1**-shared で /usr/sap 用のノード固有ディレクトリを作成します。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS ドメイン設定を確認します。 ドメインが既定の Azure NetApp Files ドメイン (つまり、 **`defaultv4iddomain.com`** ) として構成され、マッピングが **nobody** に設定されていることを確認します。  

    > [!IMPORTANT]
    > Azure NetApp Files の既定のドメイン構成 ( **`defaultv4iddomain.com`** ) と一致するように、VM 上の `/etc/idmapd.conf` に NFS ドメインを設定していることを確認します。 NFS クライアント (つまり、VM) と NFS サーバー (つまり、Azure NetApp 構成) のドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は `nobody` と表示されます。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
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
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** SAP HANA グループとユーザーを手動で作成します。 グループ sapsys とユーザー **hn1** adm の ID は、オンボード時に指定したものと同じ ID に設定する必要があります。 (この例では、ID は **1001** に設定されています)。ID が正しく設定されていない場合、ボリュームにアクセスすることはできません。 グループ sapsys とユーザー アカウント **hn1** adm と sapadm の ID は、すべての仮想マシンで同じである必要があります。  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 共有 Azure NetApp Files ボリュームをマウントします。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1** にノード固有のボリュームをマウントします。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2** にノード固有のボリュームをマウントします。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3** にノード固有のボリュームをマウントします。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** すべての HANA ボリュームが NFS プロトコル バージョン **NFSv4** でマウントされていることを確認します。  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>インストール  

この例では、Azure でスタンバイ ノードを使用したスケールアウト構成で SAP HANA をデプロイするために、HANA 2.0 SP4 を使用しました。  

### <a name="prepare-for-hana-installation"></a>HANA のインストールの準備

1. **[A]** HANA をインストールする前に、ルート パスワードを設定します。 インストールが完了した後で、ルート パスワードを無効にすることができます。 `root` として `passwd` コマンドを実行します。  

2. **[1]** パスワードの入力を求められることなく、**hanadb2** および **hanadb3** に SSH を使用してログインできることを確認します。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 SP4 に必要な追加のパッケージをインストールします。 詳細については、SAP ノート [2593824](https://launchpad.support.sap.com/#/notes/2593824) をご覧ください。 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2]、[3]** SAP HANA の `data` と `log` のディレクトリの所有権を **hn1** adm に変更します。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA のインストール

1. **[1]** 「[SAP HANA 2.0 のインストールと更新ガイド](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)」の指示に従って、SAP HANA をインストールします。 この例では、マスター、1 つのワーカー、および 1 つのスタンバイ ノードを使用して、SAP HANA スケールアウトをインストールします。  

   a. HANA のインストール ソフトウェア ディレクトリから **hdblcm** プログラムを起動します。 `internal_network` パラメーターを使用して、内部 HANA のノード間通信に使用されるサブネットのアドレス空間を渡します。  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. プロンプトで次の値を入力します。

     * **[Choose an action]\(アクションを選択する\)** : 「**1**」と入力します (インストールの場合)。
     * **[Additional components for installation]\(追加でインストールするコンポーネント\)** : 「**2, 3**」と入力します。
     * [installation path]\(インストール パス\): Enter キーを押します (既定値は /hana/shared)
     * **[Local Host Name]\(ローカル ホスト名\)** : Enter キーを押して既定値をそのまま使用します
     * **[Do you want to add hosts to the system?]\(システムにホストを追加しますか?\)** : 「**y**」と入力します
     * **[comma-separated host names to add]\(追加するコンマ区切りホスト名\)** : 「**hanadb2, hanadb3**」と入力します
     * **[Root User Name]\(ルート ユーザー名\)** [root]\: Enter キーを押して既定値をそのまま使用します
     * **[Root User Password]\(ルート ユーザー パスワード\)** : ルート ユーザーのパスワードを入力します
     * [roles for host hanadb2]\(host hanadb2 のロール\)\: 「**1**」と入力します (ワーカーの場合)
     * ホスト hanadb2 の **[Host Failover Group]\(ホスト フェールオーバー グループ\)** [既定値]\: Enter キーを押して既定値をそのまま使用します
     * ホスト hanadb2 の **[Storage Partition Number]\(ストレージ パーティション番号\)** [<<assign automatically>>]\: Enter キーを押して既定値をそのまま使用します
     * ホスト hanadb2 の **[Worker Group]\(ワーカー グループ\)** [既定値]: Enter キーを押して既定値をそのまま使用します
     * ホスト hanadb3 の **[Select roles]\(ロールの選択\)** : 「**2**」と入力します (スタンバイ)
     * ホスト hanadb3 の **[Host Failover Group]\(ホスト フェールオーバー グループ\)** [既定値]\: Enter キーを押して既定値をそのまま使用します
     * ホスト hanadb3 の **[Worker Group]\(ワーカー グループ\)** [既定値]: Enter キーを押して既定値をそのまま使用します
     * **[SAP HANA System ID]\(SAP HANA システム ID\)** : 「**HN1**」と入力します
     * **[Instance number]\(インスタンス番号\)** [00]: 「**03**」と入力します
     * **[Local Host Worker Group]\(ローカル ホスト ワーカー グループ\)** [既定値]: Enter キーを押して既定値をそのまま使用します
     * **[Select System Usage / Enter index [4]\(システム用途の選択/インデックスを入力 [4]\)** \: 「**4**」と入力します (カスタム)
     * **[Location of Data Volumes]\(データ ボリュームの場所\)** [/hana/data/HN1]: Enter キーを押して既定値をそのまま使用します
     * **[Location of Log Volumes]\(ログ ボリュームの場所\)** [/hana/log/HN1]: Enter キーを押して既定値をそのまま使用します
     * **[Restrict maximum memory allocation?]\(メモリの最大割り当てを制限しますか?\)** [n]: 「**n**」と入力します
     * **[Certificate Host Name For Host hanadb1]\(ホスト hanadb1 の 証明書ホスト名\)** [hanadb1]: Enter キーを押して既定値をそのまま使用します
     * **[Certificate Host Name For Host hanadb2]\(ホスト hanadb2 の 証明書ホスト名\)** [hanadb2]: Enter キーを押して既定値をそのまま使用します
     * **[Certificate Host Name For Host hanadb3]\(ホスト hanadb3 の 証明書ホスト名\)** [hanadb3]: Enter キーを押して既定値をそのまま使用します
     * **[System Administrator (hn1adm) Password]\(システム管理者 (hn1adm) のパスワード\)** : パスワードを入力します
     * **[System Database User (system) Password]\(システム データベース ユーザー (system) のパスワード\)** : システムのパスワードを入力します
     * **[Confirm System Database User (system) Password]\(システム データベース ユーザー (system) のパスワードの確認\)** : システムのパスワードを入力します
     * **[Restart system after machine reboot?]\(コンピューターの再起動後にシステムを再起動しますか?\)** [n]: 「**n**」と入力します 
     * **[Do you want to continue (y/n)]\(続行しますか? (y/n)\)** : 概要を検証し、すべて問題がなさそうな場合は「**y**」と入力します


2. **[1]** global.ini を確認します  

   global.ini を表示し、内部 SAP HANA のノード間通信が正しく構成されていることを確認します。 **communication** セクションを確認します。 `hana` サブネットに対するアドレス空間があり、`listeninterface` が `.internal` に設定されている必要があります。 **internal_hostname_resolution** セクションを確認します。 `hana` サブネットに属する HANA 仮想マシンの IP アドレスが含まれている必要があります。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** ホスト マッピングを追加して、クライアントの通信でクライアントの IP アドレスが確実に使用されるようにします。 セクション `public_host_resolution` を追加し、クライアント サブネットから対応する IP アドレスを追加します。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** SAP HANA を再起動して、変更をアクティブにします。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** クライアント インターフェイスが `client` サブネットの IP アドレスを使用して通信するようになっていることを確認します。  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   構成を確認する方法については、SAP ノート 「[2183363 - SAP HANA 内部ネットワークの構成](https://launchpad.support.sap.com/#/notes/2183363)」を参照してください。  

6. 基になる Azure NetApp Files ストレージ向けに SAP HANA を最適化するには、次の SAP HANA パラメーターを設定します。

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **オン**
   - `async_write_submit_active` **オン**
   - `async_write_submit_blocks` **すべて**

   詳細については、「[Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]」を参照してください。 

   SAP HANA 2.0 以降のシステムでは、`global.ini` でパラメーターを設定できます。 詳細については、SAP ノート [1999930](https://launchpad.support.sap.com/#/notes/1999930) をご覧ください。  
   
   SAP HANA 1.0 システム バージョン SPS12 以前の場合は、SAP ノート [2267798](https://launchpad.support.sap.com/#/notes/2267798) で説明されているように、インストール時にこれらのパラメーターを設定できます。  

7. Azure NetApp Files で使用されるストレージには、16 テラバイト (TB) のファイル サイズ制限があります。 SAP HANA では、ストレージの制限が暗黙的に認識されず、ファイル サイズの上限の 16 TB に達したときに新しいデータ ファイルが自動的に作成されることはありません。 SAP HANA では 16 TB を超えてファイルを拡張しようとするため、エラーとなり、最終的にはインデックス サーバーがクラッシュします。 

   > [!IMPORTANT]
   > SAP HANA がストレージ サブシステムの [16 TB の制限](../../../azure-netapp-files/azure-netapp-files-resource-limits.md)を超えてデータ ファイルを拡張しようとするのを防ぐには、`global.ini` で次のパラメーターを設定します。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 詳細については、SAP ノート [2400005](https://launchpad.support.sap.com/#/notes/2400005) を参照してください。
   > SAP ノート [2631285](https://launchpad.support.sap.com/#/notes/2631285) に注意してください。 

## <a name="test-sap-hana-failover"></a>SAP HANA フェールオーバーのテスト 

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*マスター*" と "*スレーブ*" という用語への言及があります。 ソフトウェアからこれらの用語が削除された時点で、この記事から削除します。

1. SAP HANA ワーカー ノードでノード クラッシュをシミュレートします。 次の操作を行います。 

   a. ノード クラッシュをシミュレートする前に、**hn1** adm として次のコマンドを実行して環境の状態をキャプチャします。  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. ノード クラッシュをシミュレートするには、ワーカー ノード (この場合は **hanadb2**) で次のコマンドを root として実行します。  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. システムのフェールオーバーの完了を監視します。 フェールオーバーで状態のキャプチャが完了すると、次のように表示されます。  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > ノードでカーネル パニックが発生するときは、"*すべての*" HANA 仮想マシンで `kernel.panic` を 20 秒に設定することにより、SAP HANA のフェールオーバーによる遅延を回避します。 構成は `/etc/sysctl` で行われます。 仮想マシンを再起動して、変更をアクティブにします。 この変更を行わないと、ノードでカーネル パニックが発生したとき、フェールオーバーに 10 分以上かかることがあります。  

2. 次のようにして、ネーム サーバーを強制終了します。

   a. テストの前に、**hn1** adm として次のコマンドを実行し、環境の状態を確認します。  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. アクティブなマスター ノード (この場合は **hanadb1**) で **hn1** adm として次のコマンドを実行します。  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    スタンバイ ノード **hanadb3** がマスター ノードとして引き継ぎます。 フェールオーバー テスト完了後のリソースの状態は次にようになります。  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. **hanadb1** で (つまり、ネーム サーバーが強制終了されたのと同じ仮想マシンで) HANA インスタンスを再起動します。 **hanadb1** ノードが環境に再び参加し、スタンバイ ロールを保持します。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb1** で SAP HANA が起動されると、次のような状態になります。  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. やはり、現在アクティブなマスター ノード (つまり、ノード **hanadb3**) でネーム サーバーを強制終了します。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   ノード **hanadb1** が、マスター ノードのロールを再開します。 フェールオーバー テスト完了後の状態は、次のようになります。

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. **hanadb3** で SAP HANA を起動します。スタンバイ ノードとして機能する準備が整います。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb3** で SAP HANA が起動した後、状態は次のようになります。  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。