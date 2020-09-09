---
title: RHEL での ANF を使用した SAP HANA スケールアップの高可用性 | Microsoft Docs
description: Azure 仮想マシン (VM) で ANF を使用して SAP HANA の高可用性を実現します。
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: radeltch
ms.openlocfilehash: 030677276fa077c06a95e7c677fec956b9c2a947
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556189"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux で Azure NetApp Files を使用した SAP HANA スケールアップの高可用性

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

この記事では、HANA ファイル システムが Azure NetApp Files (ANF) を使用して NFS 経由でマウントされている場合に、SAP HANA システム レプリケーションをスケールアップ デプロイで構成する方法について説明します。 サンプルの構成およびインストール コマンドでは、インスタンス番号として **03**、HANA システム ID として **HN1** が使用されています。 SAP HANA レプリケーション は、1 つのプライマリ ノードと、少なくとも 1 つのセカンダリ ノードで構成されています。

このドキュメントの各手順に付いているプレフィックスとその意味は次のとおりです。

- **[A]** :この手順はすべてのノードに適用されます
- **[1]** :この手順は node1 にのみ適用されます
- **[2]** :この手順は node2 にのみ適用されます
 
はじめに、次の SAP Note およびガイドを確認してください

- SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533): 次の情報が含まれています。
    - SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧。
    - Azure VM サイズの容量に関する重要な情報。
    - サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ。
    - Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン。
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553): SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827): HANA 環境の推奨ファイル システムが記載されています。
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167): Red Hat Enterprise Linux 用の OS 設定が推奨されています。
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879): Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています。
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632): Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498): Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692): Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351): Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
- [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) には、Linux に必要なすべての SAP Note が掲載されています。
- [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
- [Linux 上の SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
- [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
- [Pacemaker クラスターでの SAP HANA システム レプリケーション](https://access.redhat.com/articles/3004101)
- 一般的な RHEL ドキュメント
    - [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [HANA ファイル システムが NFS 共有にある場合に Pacemaker クラスターでのスケールアップで SAP HANA システム レプリケーションを構成する](https://access.redhat.com/solutions/5156571)
- Azure 固有の RHEL ドキュメント:
    - [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
    - [Microsoft Azure で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)
    - [Microsoft Azure で使用するために Red Hat Enterprise Linux に SAP HANA をインストールする](https://access.redhat.com/solutions/3193782)
    - [HANA ファイル システムが NFS 共有にある場合 Pacemaker クラスターで SAP HANA スケールアップ システム レプリケーションを構成する](https://access.redhat.com/solutions/5156571)
- [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>概要

従来、スケールアップ環境では、SAP HANA のすべてのファイル システムはローカル ストレージからマウントされていました。 Red Hat Enterprise Linux での SAP HANA システム レプリケーションの高可用性のセットアップは、[RHEL での SAP HANA システムレプリケーションのセットアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)に関するガイドで公開されています。

[Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) NFS 共有でスケールアップ システムの SAP HANA 高可用性を実現するには、1 つのノードで ANF 上の NFS 共有へのアクセスが失われたときに HANA リソースが回復するために、追加のリソース構成がクラスターに必要です。  クラスターは NFS マウントを管理して、リソースの正常性を監視できるようにします。 ファイル システム マウントと SAP HANA リソース間の依存関係が適用されます。  

![ANF での SAP HANA HA スケールアップ](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA ファイル システムは、各ノードで Azure NetApp Files を使用して NFS 共有にマウントされます。 ファイル システム /hana/data、/hana/log、/hana/shared は各ノードに固有です。 

node1 にマウント (**hanadb1**)

- /hana/data の 10.32.2.4:/**hanadb1**-data-mnt00001
- /hana/log の 10.32.2.4:/**hanadb1**-log-mnt00001
- /hana/shared の 10.32.2.4:/**hanadb1**-shared-mnt00001

node2 にマウント (**hanadb2**)

- /hana/data の 10.32.2.4:/**hanadb2**-data-mnt00001
- /hana/log の 10.32.2.4:/**hanadb2**-log-mnt00001
- /hana/shared の 10.32.2.4:/**hanadb2**-shared-mnt00001

> [!NOTE]
> /hana/shared、/hana/data、/hana/log の各ファイル システムは 2 つのノード間で共有されません。 各クラスター ノードには、独自の独立したファイル システムがあります。   

SAP HANA システム レプリケーションの構成では、専用の仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 ロード バランサーの構成を次に示します。

- フロントエンド構成:IP アドレス 10.32.0.10 (hn1-db)
- バックエンド構成:HANA システム レプリケーションに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
- プローブ ポート:ポート 62503
- 負荷分散規則:30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP (基本的な Azure ロード バランサーを使用している場合)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Azure NetApp Files インフラストラクチャの設定

Azure NetApp Files インフラストラクチャの設定を続行する前に、Azure [NetApp Files のドキュメント](https://docs.microsoft.com/azure/azure-netapp-files/)の内容をよく理解しておいてください。

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 選択した Azure リージョンで Azure NetApp Files が提供されているかどうかを確認してください。

Azure リージョン別に Azure NetApp Files を利用できるかどうかについては、[NetApp Files を利用できる Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)に関するページをご覧ください。

Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)」の手順に従って、Azure NetApp Files へのオンボードを要求してください。

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ

以下の手順では、お使いの [Azure 仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)が既にデプロイされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内にデプロイする必要があります。

1. リソースをまだデプロイしていない場合は、[Azure NetApp Files へのオンボード](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)を要求してください。

2. 「[NetApp アカウントを作成する](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)」の手順に従って、選択した Azure リージョンに NetApp アカウントを作成します。

3.  [Azure NetApp Files の容量プールの設定](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)に関するページの手順に従って、Azure NetApp Files の容量プールを設定します。

    この記事で示されている HANA アーキテクチャでは、"*Ultra* サービス" レベルで 1 つの Azure NetApp Files 容量プールが使用されています。 Azure 上の HANA ワークロードの場合、Azure NetApp Files の *Ultra* または *Premium* [サービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)を使用することをお勧めします。

4.  「[サブネットを Azure NetApp Files に委任する](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)」の手順に従って、サブネットを Azure NetApp Files に委任します。

5.  「[Azure NetApp Files の NFS ボリュームを作成する](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)」の手順に従って、Azure NetApp Files のボリュームをデプロイします。

    ボリュームをデプロイするときは、NFSv4.1 バージョンを必ず選択してください。 指定された Azure NetApp Files のサブネット内にボリュームをデプロイします。 Azure NetApp ボリュームの IP アドレスは、自動的に割り当てられます。

    Azure NetApp Files のリソースと Azure VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内に配置する必要があることに注意してください。 たとえば、hanadb1-data-mnt00001、hanadb1-log-mnt00001 などはボリューム名で、nfs://10.32.2.4/hanadb1-data-mnt00001、nfs://10.32.2.4/hanadb1-log-mnt00001 などは Azure NetApp Files ボリュームのファイル パスです。
    
    **hanadb1** では次のとおりです。

    - ボリューム hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - ボリューム hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - ボリューム hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **hanadb2** では次のとおりです。

    - ボリューム hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - ボリューム hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - ボリューム hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>重要な考慮事項

SAP HANA スケールアップ システム用の Azure NetApp Files を作成するときは、次の考慮事項に注意してください。

- 最小容量のプールは 4 テビバイト (TiB) です。
- 最小ボリューム サイズは 100 ギビバイト (GiB) です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)内に存在する必要があります。
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp Files ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム クォータとサービス レベルの機能です。 HANA Azure NetApp ボリュームのサイズを設定するときは、そのスループットが HANA システム要件を満たしていることを確認してください。
- Azure NetApp Files の[エクスポート ポリシー](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)では、ユーザーが制御できるのは、許可されたクライアントと、アクセスの種類 (読み取りと書き込み、読み取り専用など) です。
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、その機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Azure NetApp Files での HANA データベースのサイズ指定

Azure NetApp Files ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム サイズとサービス レベルの機能です。

Azure で SAP 用のインフラストラクチャを設計するときは、SAP による最小ストレージ要件に注意する必要があります。これは、最小スループット特性につながります。

- /hana/log での読み取り/書き込みは、1 MB の I/O サイズで、毎秒 250 メガバイト (MB/秒)。
- /hana/data での読み取りアクティビティは、16 MB および 64 MB の I/O サイズで、400 MB/秒以上。
- /hana/data での書き込みアクティビティは、16 MB および 64 MB の I/O サイズで、250 MB/秒以上。

ボリューム クォータの 1 TiB あたりの [Azure NetApp Files スループットの上限](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)は次のとおりです。

- Premium Storage 層 - 64 MiB/秒
- Ultra Storage 層 - 128 MiB/秒

/hana/data と /hana/log に対する SAP の最小スループット要件、および /hana/shared のガイドラインを満たすため、推奨されるサイズは次のようになります。

|    ボリューム    | Premium Storage 層のサイズ | Ultra Storage 層のサイズ | サポートされる NFS プロトコル |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6.3 TiB            |          3.2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 または v4.1    |


> [!NOTE]
> ここで説明されている Azure NetApp Files のサイズ設定の推奨事項は、SAP がインフラストラクチャ プロバイダーに対して推奨している最小要件を満たすことを目標としています。 実際の顧客のデプロイとワークロードのシナリオでは、これらのサイズでは十分ではない場合があります。 これらの推奨事項は開始点として利用し、ご自分の固有のワークロードの要件に合わせて調整してください。

> [!TIP]
> Azure NetApp Files ボリュームは、ボリュームを "*マウント解除*" したり、仮想マシンを停止したり、SAP HANA を停止したりする必要なく、動的にサイズ変更することができます。 この方法により、アプリケーションの予想されるスループット要求と予期しないスループット要求の両方を柔軟に満たすことができます。

> [!NOTE]
> この記事で /hana/shared をマウントするすべてのコマンドは、NFSv4.1 の /hana/shared ボリュームに対して示されています。
> /hana/shared ボリュームを NFSv3 ボリュームとしてデプロイした場合は、必ず /hana/shared のマウント コマンドを NFSv3 用に調整してください。


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure portal を使用した Linux 仮想マシンのデプロイ 

最初に Azure NetApp Files ボリュームを作成する必要があります。 その後、次の手順を行います。

1.  リソース グループを作成します。
2.  仮想ネットワークを作成します。
3.  可用性セットを作成します。 更新ドメインの最大数を設定します。
4.  ロード バランサー (内部) を作成します。 Standard Load Balancer をお勧めします。
    手順 2 で作成した仮想ネットワークを選択します。
5.  仮想マシン 1 (**hanadb1**) を作成します。 
6.  仮想マシン 2 (**hanadb2**) を作成します。  
7.  仮想マシンの作成中は、すべてのマウント ポイントが Azure NetApp Files の NFS 共有にあるため、ディスクは追加しません。 
8.  Standard Load Balancer を使用している場合は、次の構成手順に従います。
    1.  まず、フロントエンド IP プールを作成します。
        1.  ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
        1.  新規のフロントエンド IP プールの名前を入力します (例: **hana-frontend**)。
        1.  **[割り当て]** を **[静的]** に設定し、IP アドレスを入力します (例: **10.32.0.10**)。
        1.  **[OK]** を選択します。
        1.  新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。
    1.  次に、バックエンド プールを作成します。
        1.  ロードバランサーを開き、 **[backend pools]\(バックエンド プール\)** を選択し、 **[Add]\(追加\)** を選択します。
        1.  新しいバックエンド プールの名前を入力します (例: **hana-backend**)。
        1.  **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。
        1.  ** 仮想マシン**を選択します。
        1.  SAP HANA クラスターの仮想マシンとその IP アドレスを選択します。
        1.  **[追加]** を選択します。
    1.  次に、正常性プローブを作成します。
        1.  ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しい正常性プローブの名前を入力します (例: **hana-hp**)。
        1.  プロトコルとして [TCP] を選択し、ポート 625**03** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
        1.  **[OK]** を選択します。
    1.  次に、負荷分散規則を作成します。
        1.  ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しいロード バランサー規則の名前を入力します (例: **hana-lb**)。
        1.  前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**、**hana-backend**、**hana-hp**)。
        1.  **[HA ポート]** を選択します。
        1.  **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
        1.  **Floating IP を有効にします**。
        1.  **[OK]** を選択します。

> [!NOTE] 
> パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)」を参照してください。

9. または、Basic Load Balancer を使用するシナリオの場合は、次の構成手順に従います。
    1.  ロードバランサーを構成します。 まず、フロントエンド IP プールを作成します。
        1.  ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
        1.  新規のフロントエンド IP プールの名前を入力します (例: **hana-frontend**)。
        1.  **[割り当て]** を **[静的]** に設定し、IP アドレスを入力します (例: **10.32.0.10**)。
        1.  **[OK]** を選択します。
        1.  新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。
    1.  次に、バックエンド プールを作成します。
        1.  ロードバランサーを開き、 **[backend pools]\(バックエンド プール\)** を選択し、 **[Add]\(追加\)** を選択します。
        1.  新しいバックエンド プールの名前を入力します (例: **hana-backend**)。
        1.  **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。
        1.  手順 3 で作成した可用性セットを選択します。
        1.  SAP HANA クラスターの仮想マシンを選択します。
        1.  **[OK]** を選択します。
    1.  次に、正常性プローブを作成します。
        1.  ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しい正常性プローブの名前を入力します (例: **hana-hp**)。
        1.  プロトコルとして **[TCP]** を選択し、ポート 625**03** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
        1.  **[OK]** を選択します。
    1.  SAP HANA 1.0 の場合は、負荷分散規則を作成します。
        1.  ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**15)。
        1.  前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
        1.  **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 15 を入力します。
        1.  **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
        1.  **Floating IP を有効にします**。
        1.  **[OK]** を選択します。
        1.  ポート 3**03**17 について、これらの手順を繰り返します。
    1.  SAP HANA 2.0 の場合は、システム データベースの負荷分散規則を作成します。
        1.  ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**13)。
        1.  前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
        1.  **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 13 を入力します。
        1.  **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
        1.  **Floating IP を有効にします**。
        1.  **[OK]** を選択します。
        1.  ポート 3**03**14 について、これらの手順を繰り返します。
    1.  SAP HANA 2.0 の場合は、まずテナント データベースの負荷分散規則を作成します。
        1.  ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
        1.  新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**40)。
        1.  前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
        1.  **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 40 を入力します。
        1.  **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
        1.  **Floating IP を有効にします**。
        1.  **[OK]** を選択します。
        1.  ポート 3**03**41 と 3**03**42 について、これらの手順を繰り返します。

SAP HANA に必要なポートについて詳しくは、[SAP HANA テナント データベース](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ガイドの[テナント データベースへの接続](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)に関する章または SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694) を参照してください。

> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください。 SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) も参照してください。

## <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームのマウント

1. **[A]** HANA データベース ボリュームのマウント ポイントを作成します。 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** NFS ドメイン設定を確認します。 ドメインが既定の Azure NetApp Files ドメイン (つまり、**defaultv4iddomain.com**) として構成され、マッピングが **nobody** に設定されていることを確認します。

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Azure NetApp Files の既定のドメイン構成 (**defaultv4iddomain.com**) と一致するように、VM 上の /etc/idmapd.conf に NFS ドメインを設定していることを確認します。 NFS クライアント (つまり、VM) と NFS サーバー (つまり、Azure NetApp 構成) のドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は nobody と表示されます。
    

3. **[1]** node1 (**hanadb1**) にノード固有のボリュームをマウントします 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** node2 (**hanadb2**) にノード固有のボリュームをマウントします
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** すべての HANA ボリュームが NFS プロトコル バージョン NFSv4 でマウントされていることを確認します。

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** **nfs4_disable_idmapping** を確認します。 これは、**Y** に設定されている必要があります。**nfs4_disable_idmapping** が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   **nfs_disable_idmapping** パラメーターを変更する方法については、[https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) を参照してください。 


## <a name="sap-hana-installation"></a>SAP HANA のインストール

1. **[A]** すべてのホストにホスト名解決を設定します。

   DNS サーバーを使用するか、すべてのノードの /etc/hosts ファイルを変更することができます。 この例では、/etc/hosts ファイルを使用する方法を示しています。 次のコマンドの IP アドレスとホスト名を置き換えます。

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** HANA 構成のための RHEL

   RHEL のバージョンに応じて、次の SAP Note で説明されているように RHEL を構成します。

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB:RHEL 8 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux:GCC 6.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux:GCC 7.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux:GCC 9.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** SAP HANA をインストールします

   HANA 2.0 SPS 01 から、MDC が既定のオプションです。 HANA システムをインストールすると、SYSTEMDB と、同じ SID を持つテナントが一緒に作成されます。 既定のテナントを使用しない場合もあります。 インストール時に初期テナントを作成しない場合、SAP Note [2629711](https://launchpad.support.sap.com/#/notes/2629711) の指示に従うことができます。

    HANA DVD から **hdblcm** プログラムを実行します。 プロンプトで次の値を入力します。  
    Choose installation (インストールの選択):「**1**」を入力します (インストールの場合)  
    Select additional components for installation (追加でインストールするコンポーネントの選択):**1** を入力します。  
    Enter Installation Path (インストール パスの入力) [/hana/shared]: Enter キーを押して既定値をそのまま使用します  
    Enter Local Host Name (ローカル ホスト名の入力) [..]:Enter キーを押して既定値をそのまま使用します  
    Do you want to add additional hosts to the system? (システムに別のホストを追加しますか?) (y/n) [n]: **n**  
    Enter SAP HANA System ID (SAP HANA のシステム ID を入力):「**HN1**」を入力します。  
    Enter Instance Number [00] \(インスタンス番号 (00) の入力):「**03**」を入力します  
    Select Database Mode / Enter Index (データベース モードの選択/インデックスを入力) [1]: Enter キーを押して既定値をそのまま使用します  
    Select System Usage / Enter Index (システム用途の選択/インデックスを入力) [4]: 「**4**」を入力します (カスタム)  
    Enter Location of Data Volumes (データ ボリュームの場所を入力) [/hana/data]: Enter キーを押して既定値をそのまま使用します  
    Enter Location of Log Volumes (ログ ボリュームの場所を入力) [/hana/log]: Enter キーを押して既定値をそのまま使用します  
    Restrict maximum memory allocation? (メモリの最大割り当てを制限しますか?) [n]: Enter キーを押して既定値をそのまま使用します  
    Enter Certificate Host Name For Host '...' (ホスト '...' の証明書ホスト名を入力) [...]: Enter キーを押して既定値をそのまま使用します  
    Enter SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを入力):ホスト エージェントのユーザー パスワードを入力します  
    Confirm SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを確認):確認用にホスト エージェントのユーザー パスワードを再入力します  
    Enter System Administrator (hn1adm) Password\(システム管理者 (hn1adm) のパスワードを入力\):システム管理者のパスワードを入力します  
    Confirm System Administrator (hn1adm) Password (システム管理者 (hn1adm) のパスワードを確認):確認用にシステム管理者のパスワードを再入力します  
    Enter System Administrator Home Directory (システム管理者のホーム ディレクトリを入力) [/usr/sap/HN1/home]: Enter キーを押して既定値をそのまま使用します  
    Enter System Administrator Login Shell (システム管理者のログイン シェルを入力) [/bin/sh]: Enter キーを押して既定値をそのまま使用します  
    Enter System Administrator User ID (システム管理者のユーザー ID を入力) [1001]: Enter キーを押して既定値をそのまま使用します  
    Enter ID of User Group (sapsys) (ユーザー グループ (sapsys) のID を入力) [79]: Enter キーを押して既定値をそのまま使用します  
    Enter Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを入力):データベース ユーザーのパスワードを入力します  
    Confirm Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを確認):確認用にデータベース ユーザーのパスワードを再入力します  
    Restart system after machine reboot? (コンピューターの再起動後にシステムを再起動しますか?) [n]: Enter キーを押して既定値をそのまま使用します  
    Do you want to continue? (続行してもよろしいですか?) (y/n):概要を確認します。 「**y**」と入力して続行します  

4. **[A]** SAP Host Agent をアップグレードします

   [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) から最新の SAP Host Agent アーカイブをダウンロードし、次のコマンドを実行してエージェントをアップグレードします。 アーカイブのパスを置き換えて、ダウンロードしたファイルを示すようにします。

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** ファイアウォールを構成します

   Azure ロード バランサーのプローブ ポート用にファイアウォール規則を作成します。

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA システム レプリケーションの構成

[SAP HANA システム レプリケーション](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication)のセットアップの手順に従って、SAP HANA システム レプリケーションを構成します。 

## <a name="cluster-configuration"></a>クラスター構成

このセクションでは、Azure NetApp Files を使用して NFS 共有に SAP HANA をインストールしている場合に、クラスターをシームレスに運用するために必要な手順について説明します。 

### <a name="create-a-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)」の手順に従って、この HANA サーバーに対して基本的な Pacemaker クラスターを作成します。

### <a name="configure-filesystem-resources"></a>Filesystem リソースの構成

この例では、各クラスター ノードに独自の HANA NFS ファイル システム /hana/shared、/hana/data、および /hana/log があります。   

1. **[1]** クラスターをメンテナンス モードにします。

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** **hanadb1** マウント用の Filesystem リソースを作成します。

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** **hanadb2** マウント用の Filesystem リソースを作成します。

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    監視ごとにファイル システムの読み取り/書き込みテストを実行するよう、`OCF_CHECK_LEVEL=20` 属性が監視操作に追加されます。 この属性がない場合、監視操作ではファイル システムがマウントされていることを確認するだけです。 接続が失われると、アクセス不可能であるにもかかわらずファイル システムがマウントされたままになる場合があるため、これは問題になる可能性があります。

    `on-fail=fence` 属性も監視操作に追加されます。 このオプションを使用すると、ノードで監視操作が失敗した場合、そのノードはすぐにフェンスされます。 このオプションがない場合、既定の動作では、障害が発生したリソースに依存するすべてのリソースを停止し、障害が発生したリソースを再起動してから、障害が発生したリソースに依存するすべてのリソースを起動します。 障害が発生したリソースに SAPHana リソースが依存している場合、この動作は時間がかかる可能性があるだけでなく、完全に失敗する可能性もあります。 HANA 実行可能ファイルを保持している NFS サーバーにアクセスできない場合、SAPHana リソースは正常に停止できません。

4. **[1]** 場所の制約の構成

   場所の制約を構成して、hanadb1 固有のマウントを管理するリソースを hanadb2 で決して実行できないこと、またその逆を保証します。

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never` オプションを設定するのは、各ノード固有のマウントで同じマウント ポイントを共有するからです。 たとえば、`hana_data1` はマウント ポイント `/hana/data` を使用し、`hana_data2` もマウント ポイント `/hana/data` を使用します。 これにより、クラスター起動時にリソースの状態をチェックするときにプローブ操作の誤検出が起きる可能性があり、その結果として不要な回復動作が発生する可能性があります。 これは `resource-discovery=never` を設定することで回避できます。

5. **[1]** 属性リソースの構成

   属性リソースを構成します。 これらの属性は、ノードの NFS マウント (/hana/data、/hana/log、および /hana/data) がすべてマウントされている場合は true に設定され、そうでない場合は false に設定されます。

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** 場所の制約の構成

   hanadb1 の属性リソースが hanadb2 で決して実行されず、その逆も起きないように場所の制約を構成します。

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 順序制約の作成

   ノードの NFS マウントがすべてマウントされた後でないとノードの属性リソースが起動しないように、順序制約を構成します。
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 構成にグループ `hanadb1_nfs` または `hanadb2_nfs` の外部のファイル システムが含まれる場合、ファイル システム間に順序の依存関係がなくなるよう、`sequential=false` オプションを含めます。 すべてのファイル システムは、相対的な起動順序の制約はありませんが、`hana_nfs1_active` よりも先に起動しなければなりません。 詳細については、[HANA ファイル システムが NFS 共有にある場合に Pacemaker クラスターでのスケールアップで SAP HANA システム レプリケーションを構成する方法](https://access.redhat.com/solutions/5156571)に関するページを参照してください。

### <a name="configure-sap-hana-cluster-resources"></a>SAP HANA クラスター リソースの構成

1. 「[SAP HANA クラスター リソースの作成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#create-sap-hana-cluster-resources)」の手順に従って、クラスターに SAP HANA リソースを作成します。 SAP HANA リソースを作成したら、SAP HANA リソースとファイル システム (NFS マウント) の間に場所ルール制約を作成する必要があります。

2. **[1]** SAP HANA リソースと NFS マウント間の制約の構成

   ノードの NFS マウントがすべてマウントされている場合にのみ SAP HANA リソースをノードで実行できるよう、場所ルール制約を設定します。

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   クラスターとすべてのリソースの状態のチェック

    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

ここでは、設定をテストする方法について説明します。 

1. テストを開始する前に、Pacemaker に (pcs status での) 失敗したアクションがないこと、予期しない場所の制約 (たとえば移行テストの残り物) がないこと、HANA システム レプリケーションが (たとえば systemReplicationStatus で) 同期していることを確認します。

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. ノードが NFS 共有 (/hana/shared) へのアクセスを失ったときの障害シナリオに備えてクラスター構成を検証します。

   SAP HANA リソース エージェントでは、フェールオーバー中の操作の実行は `/hana/shared` に保存されるバイナリに依存しています。 提示されるシナリオでは、ファイル システム `/hana/shared` は NFS 経由でマウントされます。  
   サーバーの 1 つが NFS 共有へのアクセスを失う障害をシミュレートすることは困難です。 実行できるテストは、ファイル システムを読み取り専用として再マウントすることです。
   この方法では、アクティブ ノードで `/hana/shared` へのアクセスが失われた場合にクラスターがフェールオーバーできることを検証します。     


   **予測される結果:** `/hana/shared` を読み取り専用ファイル システムとして作成すると、ファイル システムの読み取り/書き込み操作を実行するリソース `hana_shared1` の `OCF_CHECK_LEVEL` 属性は、ファイル システムに何も書き込めないため失敗し、HANA リソースのフェールオーバーを実行します。  HANA ノードが NFS 共有へのアクセスを失った場合も、同じ結果が予想されます。 

   テスト開始前のリソースの状態:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   次のコマンドを使用して、アクティブなクラスター ノードで /hana/shared を読み取り専用モードにできます。

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 は、stonith (`pcs property show stonith-action`) で設定されたアクションに基づいて、再起動または電源オフのいずれかを行います。  サーバー (hanadb1) がダウンすると、HANA リソースは hanadb2 に移動します。 クラスターの状態は hanadb2 から確認できます。

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   [RHEL での SAP HANA システム レプリケーションのセットアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup)に関するページで説明されているテストも実行して、SAP HANA クラスター構成を十分にテストすることをお勧めします。   