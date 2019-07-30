---
title: Azure 仮想マシン (VM) 上で SAP HANA システム レプリケーションを設定する | Microsoft Docs
description: Azure 仮想マシン (VM) 上で SAP HANA の高可用性を実現します。
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 66e1e4603602835d6ed5be9af58eb09a24b00b63
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709118"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

オンプレミス開発の場合、HANA システム レプリケーションまたは共有記憶域を使用して、SAP HANA の高可用性を実現できます。
Azure 仮想マシン (VM) 上では、Azure VM HANA システム レプリケーションが現在サポートされている唯一の高可用性機能です。
SAP HANA レプリケーション は、1 つのプライマリ ノードと、少なくとも 1 つのセカンダリ ノードで構成されています。 プライマリ ノードのデータに対する変更は、セカンダリ ノードに同期的または非同期的にレプリケートされます。

この記事では、仮想マシンのデプロイおよび構成方法、クラスター フレームワークのインストール方法、SAP HANA システム レプリケーションのインストールおよび構成方法について説明します。
サンプルの構成では、インストールのコマンドで、インスタンス番号として **03**、HANA システム ID として **HN1** が使用されています。

はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧。
  * Azure VM サイズの容量に関する重要な情報。
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ。
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン。
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Pacemaker クラスターでの SAP HANA システム レプリケーション](https://access.redhat.com/articles/3004101)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)
  * [Microsoft Azure で使用するために Red Hat Enterprise Linux に SAP HANA をインストールする](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>概要

高可用性を実現するために、SAP HANA は 2 台の仮想マシンにインストールされます。 データは、HANA システム レプリケーションを使用してレプリケートされます。

![SAP HANA の高可用性の概要](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA システム要件の設定では、専用の仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 ロード バランサーの構成を次に示します。

* フロントエンド構成:IP アドレス 10.0.0.13 (hn1-db)
* バックエンド構成:HANA システム レプリケーションに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート:ポート 62503
* 負荷分散規則:30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP

## <a name="deploy-for-linux"></a>Linux 用デプロイ

Azure Marketplace には Red Hat Enterprise Linux 7.4 for SAP HANA のイメージが含まれており、これを新しい仮想マシンのデプロイに使用できます。

### <a name="deploy-with-a-template"></a>テンプレートを使用したデプロイ

GitHub にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイできます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。
テンプレートをデプロイするには、次の手順に従います。

1. Azure portal で[データベース テンプレート][template-multisid-db]を開きます。
1. 次のパラメーターを入力します。
    * **[Sap System Id]\(SAP システム ID\)** :インストールする SAP システムの SAP システム ID を入力します。 この ID は、デプロイされるリソースのプレフィックスとして使われます。
    * **[OS Type]\(OS の種類\)** :いずれかの Linux ディストリビューションを選択します。 この例では、 **[RHEL 7]** を選択します。
    * **[Db Type]\(データベースの種類\)** : **[HANA]** を選択します。
    * **[Sap System Size]\(SAP システムのサイズ\)** :新しいシステムが提供する SAPS の数を入力します。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
    * **[System Availability]\(システムの可用性\)** : **[HA]** を選択します。
    * **[Admin Username, Admin Password or SSH key]\(管理ユーザー名、管理パスワード、SSH キー\)** :コンピューターへのサインインに使用できる新しいユーザーが作成されます。
    * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、この ID は、 **/subscriptions/\<サブスクリプション ID>/resourceGroups/\<リソース グループ名>/providers/Microsoft.Network/virtualNetworks/\<仮想ネットワーク名>/subnets/\<サブネット名>** のようになります。 新しい仮想ネットワークを作成する場合は、空白のままにします

### <a name="manual-deployment"></a>手動デプロイ

1. リソース グループを作成します。
1. 仮想ネットワークを作成します。
1. 可用性セットを作成します。  
   更新ドメインの最大数を設定します。
1. ロード バランサー (内部) を作成します。
   * 手順 2 で作成した仮想ネットワークを選択します。
1. 仮想マシン 1 を作成します。  
   Red Hat Enterprise Linux 7.4 for SAP HANA 以降を使用します。 この例では、Red Hat Enterprise Linux 7.4 for SAP HANA のイメージを使用しています<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 手順 3 で作成した可用性セットを選択します。
1. 仮想マシン 2 を作成します。  
   Red Hat Enterprise Linux 7.4 for SAP HANA 以降を使用します。 この例では、Red Hat Enterprise Linux 7.4 for SAP HANA のイメージを使用しています<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 手順 3 で作成した可用性セットを選択します。
1. データ ディスクを追加します。
1. ロードバランサーを構成します。 まず、フロントエンド IP プールを作成します。

   1. ロード バランサーを開き、 **[frontend IP pool]\(フロントエンド IP プール\)** を選択して **[Add]\(追加\)** を選択します
   1. 新規のフロントエンド IP プールの名前を入力します (例: **hana-frontend**)。
   1. **[Assignment]\(割り当て\)** を **[Static]\(静的\)** に設定し、IP アドレスを入力します (例: **10.0.0.13**)。
   1. **[OK]** を選択します。
   1. 新しいフロントエンド IP プールが作成されたら、プールの IP アドレスを書き留めます。

1. 次に、バックエンド プールを作成します。

   1. ロードバランサーを開き、 **[backend pools]\(バックエンド プール\)** を選択し、 **[Add]\(追加\)** を選択します。
   1. 新しいバックエンド プールの名前を入力します (例: **hana-backend**)。
   1. **[Add a virtual machine]\(仮想マシンの追加\)** を選択します。
   1. 手順 3 で作成した可用性セットを選択します。
   1. SAP HANA クラスターの仮想マシンを選択します。
   1. **[OK]** を選択します。

1. 次に、正常性プローブを作成します。

   1. ロード バランサーを開き、 **[health probes]\(正常性プローブ\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しい正常性プローブの名前を入力します (例: **hana-hp**)。
   1. プロトコルとして **[TCP]** を選択し、ポート 625**03** を選択します。 **[Interval]\(間隔\)** の値を 5 に設定し、 **[Unhealthy threshold]\(異常しきい値\)** の値を 2 に設定します。
   1. **[OK]** を選択します。

1. SAP HANA 1.0 の場合は、負荷分散規則を作成します。

   1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**15)。
   1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
   1. **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 15 を入力します。
   1. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
   1. **Floating IP を有効にします**。
   1. **[OK]** を選択します。
   1. ポート 3**03**17 について、これらの手順を繰り返します。

1. SAP HANA 2.0 の場合は、システム データベースの負荷分散規則を作成します。

   1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**13)。
   1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
   1. **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 13 を入力します。
   1. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
   1. **Floating IP を有効にします**。
   1. **[OK]** を選択します。
   1. ポート 3**03**14 について、これらの手順を繰り返します。

1. SAP HANA 2.0 の場合は、まずテナント データベースの負荷分散規則を作成します。

   1. ロード バランサーを開き、 **[load balancing rules]\(負荷分散規則\)** を選択して **[Add]\(追加\)** を選択します。
   1. 新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**40)。
   1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: **hana-frontend**)。
   1. **[Protocol]\(プロトコル\)** を **[TCP]** に設定し、ポート 3**03** 40 を入力します。
   1. **[idle timeout]\(アイドル タイムアウト\)** を 30 分に増やします
   1. **Floating IP を有効にします**。
   1. **[OK]** を選択します。
   1. ポート 3**03**41 と 3**03**42 について、これらの手順を繰り返します。

SAP HANA に必要なポートについて詳しくは、[SAP HANA テナント データベース](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ガイドの[テナント データベースへの接続](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)に関する章または [SAP Note 2388694][2388694] を参照してください。

> [!IMPORTANT]
> Azure Load Balancer の背後に配置された Azure VM では TCP タイムスタンプを有効にしないでください。 TCP タイムスタンプを有効にすると正常性プローブが失敗することになります。 パラメーター **net.ipv4.tcp_timestamps** は **0** に設定します。 詳しくは、「[Load Balancer の正常性プローブ](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)」を参照してください。
> SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421) も参照してください。 

## <a name="install-sap-hana"></a>SAP HANA のインストール

このセクションの手順では、次のプレフィックスを使用します。

* **[A]** :この手順はすべてのノードに適用されます。
* **[1]** :この手順はノード 1 にのみ適用されます。
* **[2]** :この手順は Pacemaker クラスターのノード 2 にのみ適用されます。

1. **[A]** ディスク レイアウトの設定:**論理ボリューム マネージャー (LVM)** 。

   データおよびログ ファイルを格納するボリュームには、LVM を使用することをお勧めします。 次の例は、仮想マシンに 4 つのデータ ディスクがアタッチされていて、これを使用して 2 つのボリュームを作成するということを前提としています。

   すべての使用できるディスクの一覧を出力します。

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   出力例:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   使用するすべてのディスクの物理ボリュームを作成します。

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   データ ファイル用のボリューム グループを作成します。 ログ ファイル用に 1 つ、SAP HANA の共有ディレクトリ用に 1 つのボリューム グループを作成します。

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   論理ボリュームを作成します。 `-i` スイッチを指定せずに `lvcreate` を使用すると、線形のボリュームが作成されます。 I/O パフォーマンスを向上するためにストライプ ボリュームを作成することをお勧めします。`-i` 引数は、基になる物理ボリュームの番号にする必要があります。 このドキュメントでは、2 つの物理ボリュームが使用されるため、`-i` スイッチ引数は **2** に設定されます。 ログ ボリューム用に物理ボリュームが 1 つ使用されるため、`-i` スイッチは明示的には使用されません。 データ、ログ、または共有ボリュームごとに複数の物理ボリュームを使用する場合は、`-i` スイッチを使用して基になる物理ボリュームの番号に設定します。

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   マウント ディレクトリを作成し、すべての論理ボリュームの UUID をコピーします

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   3 つの論理ボリュームの `fstab` エントリを作成します。

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   `/etc/fstab` ファイルに次の行を挿入します。

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   新しいボリュームをマウントします。

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** ディスク レイアウトの設定:**プレーン ディスク**。

   デモ システムの場合、ご自身の HANA のデータとログ ファイルを 1 つのディスクに配置することができます。 /dev/disk/azure/scsi1/lun0 にパーティションを作成し、xfs でフォーマットします。

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   /etc/fstab ファイルに次の行を挿入します。

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   ターゲット ディレクトリを作成してディスクをマウントします。

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** すべてのホストにホスト名解決を設定します。

   DNS サーバーを使用するか、すべてのノードの /etc/hosts ファイルを変更することができます。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   次の行を /etc/hosts ファイルに挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します。

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** HANA の構成のための RHEL

   RHEL を SAP Note [2292690] と [2455582] と <https://access.redhat.com/solutions/2447641> のとおりに構成します。

1. **[A]** SAP HANA をインストールします

   SAP HANA システム レプリケーションをインストールするには、<https://access.redhat.com/articles/3004101> の手順に従います。

   * HANA DVD から **hdblcm** プログラムを実行します。 プロンプトで次の値を入力します。
   * Choose installation (インストールの選択):**1** を入力します。
   * Select additional components for installation (追加でインストールするコンポーネントの選択):**1** を入力します。
   * Enter Installation Path (インストール パスの入力) [/hana/shared]:Enter キーを押します。
   * Enter Local Host Name (ローカル ホスト名の入力) [..]:Enter キーを押します。
   * Do you want to add additional hosts to the system? (システムに別のホストを追加しますか?) (y/n) \[n]:Enter キーを押します。
   * Enter SAP HANA System ID (SAP HANA のシステム ID を入力):HANA の SID を入力します。例:**HN1**。
   * Enter Instance Number [00] \(インスタンス番号 (00) の入力):HANA のインスタンス番号を入力します。 Azure テンプレートを使用した場合、またはこの記述の手動デプロイに関するセクションに従った場合は、「**03**」を入力します。
   * Select Database Mode / Enter Index (データベース モードの選択/インデックスの入力) [1]:Enter キーを押します。
   * Select System Usage / Enter Index [4] \(システム使用率の選択/インデックス (4) の入力):システムの使用率の値を選択します。
   * Enter Location of Data Volumes (データ ボリュームの場所の入力) [/hana/data/HN1]:Enter キーを押します。
   * Enter Location of Log Volumes (ログ ボリュームの場所の入力) [/hana/log/HN1]:Enter キーを押します。
   * Restrict maximum memory allocation? (メモリの最大割り当てを制限しますか?) [n]:Enter キーを押します。
   * Enter Certificate Host Name For Host '...' (ホスト '...' の証明書のホスト名を入力):Enter キーを押します。
   * Enter SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを入力):ホスト エージェントのユーザー パスワードを入力します。
   * Confirm SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを確認):確認用にホスト エージェントのユーザー パスワードを再入力します。
   * Enter System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを入力):システム管理者のパスワードを入力します。
   * Confirm System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを確認):確認用にシステム管理者のパスワードを再入力します。
   * Enter System Administrator Home Directory (システム管理者のホーム ディレクトリの入力) [/usr/sap/HN1/home]:Enter キーを押します。
   * Enter System Administrator Login Shell (システム管理者のログイン シェルの入力) [/bin/sh]:Enter キーを押します。
   * Enter System Administrator User ID (システム管理者のユーザー ID の入力) [1001]:Enter キーを押します。
   * Enter ID of User Group (sapsys) (ユーザー グループ (sapsys) の ID を入力) [79]:Enter キーを押します。
   * Enter Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを入力):データベース ユーザーのパスワードを入力します。
   * Confirm Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを確認):確認用にデータベース ユーザーのパスワードを再入力します。
   * Restart system after machine reboot? (コンピューターの再起動後にシステムを再起動しますか?) [n]:Enter キーを押します。
   * Do you want to continue? (続行してもよろしいですか?) (y/n):概要を確認します。 「**y**」と入力して続行します。

1. **[A]** SAP Host Agent をアップグレードします。

   [SAP Software Center][sap-swcenter] から最新の SAP Host Agent アーカイブをダウンロードし、次のコマンドを実行してエージェントをアップグレードします。 アーカイブのパスを置き換えて、ダウンロードしたファイルを示すようにします。

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** ファイアウォールを構成します

   Azure ロード バランサーのプローブ ポート用にファイアウォール規則を作成します。

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 システム レプリケーションの構成

このセクションの手順では、次のプレフィックスを使用します。

* **[A]** :この手順はすべてのノードに適用されます。
* **[1]** :この手順はノード 1 にのみ適用されます。
* **[2]** :この手順は Pacemaker クラスターのノード 2 にのみ適用されます。

1. **[A]** ファイアウォールを構成します

   HANA システム レプリケーションおよびクライアント トラフィックを許可するファイアウォール規則を作成します。 必要なポートは、[すべての SAP 製品の TCP/IP ポート](https://help.sap.com/viewer/ports)のページにあります。 次のコマンドは、HANA 2.0 システム レプリケーションと、データベース SYSTEMDB、HN1 および NW1 へのクライアント トラフィックを許可する単なる例です。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** テナント データベースを作成します。

   SAP HANA 2.0 または MDC を使用している場合は、ご自身の SAP NetWeaver システムに対してテナント データベースを作成します。 **NW1** をご自身の SAP システムの SID に置き換えます。

   <hanasid\>adm として次のコマンドを実行します。

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 最初のノードでシステム レプリケーションを構成します

   <hanasid\>adm としてデータベースをバックアップします。

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   システム PKI ファイルをセカンダリ サイトにコピーします。

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   プライマリ サイトを作成します。

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** 2 番目のノードでシステム レプリケーションを構成します
    
   2 番目のノードを登録して、システム レプリケーションを開始します。 <hanasid\>adm として次のコマンドを実行します。

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** レプリケーションの状態をチェックする

   レプリケーションの状態をチェックし、すべてのデータベースが同期されるまで待機します。状態が不明な場合、ファイアウォール設定を確認します。

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 システム レプリケーションの構成

このセクションの手順では、次のプレフィックスを使用します。

* **[A]** :この手順はすべてのノードに適用されます。
* **[1]** :この手順はノード 1 にのみ適用されます。
* **[2]** :この手順は Pacemaker クラスターのノード 2 にのみ適用されます。

1. **[A]** ファイアウォールを構成します

   HANA システム レプリケーションおよびクライアント トラフィックを許可するファイアウォール規則を作成します。 必要なポートは、[すべての SAP 製品の TCP/IP ポート](https://help.sap.com/viewer/ports)のページにあります。 次のコマンドは、HANA 2.0 システム レプリケーションを許可する単なる例です。 お使いの SAP HANA 1.0 インストールにそれを適用します。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** 必要なユーザーを作成します。

   root として次のコマンドを実行します。 太字の文字列 (HANA システム ID **HN1**、インスタンス番号 **03**) を、ご自身の SAP HANA のインストールの値に置き換えてください。

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** キーストア エントリを作成します。

   root として次のコマンドを実行して、新しいキーストア エントリを作成します。

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** データベースをバックアップします。

   root としてデータベースをバックアップします。

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   マルチテナント インストールを使用する場合は、テナント データベースもバックアップします。

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 最初のノードでシステム レプリケーションを構成します。

   <hanasid\>adm としてプライマリ サイトを作成します。

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** セカンダリ ノードでシステム レプリケーションを構成します。

   <hanasid\>adm としてセカンダリ サイトを登録します。

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md)」 (Azure で Red Hat Enterprise Linux に Pacemaker を設定する) の手順に従って、この HANA サーバーに対して基本的な Pacemaker クラスターを作成します。

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA クラスター リソースの作成

**すべてのノード**に、SAP HANA リソース エージェントをインストールします。 このパッケージを含むリポジトリを必ず有効にします。

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

次いで HANA トポロジを作成します。 Pacemaker クラスター ノードのいずれかで、次のコマンドを実行します。

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

次に、HANA リソースを作成します。

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>クラスターの設定をテストする

ここでは、設定をテストする方法について説明します。 テストを開始する前に、Pacemaker に (pcs status での) 失敗したアクションがないこと、予期しない場所の制約 (たとえば移行テストの残り物) がないこと、HANA が (たとえば systemReplicationStatus で) 同期していることを確認します。

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>移行をテストする

テスト開始前のリソースの状態:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

SAP HANA マスター ノードは、次のコマンドを実行すると移行できます。

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

`AUTOMATED_REGISTER="false"` が設定されている場合、このコマンドでは、SAP HANA マスター ノードおよび hn1-db-1 への仮想 IP アドレスを含むグループを移行できます。

移行が完了すると、'sudo pcs status' の出力は次のようになります。

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

hn1-db-0 上の SAP HANA リソースは停止されます。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

移行では場所の制約が作成されますが、これは再度削除する必要があります。

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

'pcs status' を使用して HANA リソースの状態を監視します。 hn1-db-0 上で HANA が起動されている場合、出力は次のようになります

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Azure フェンス エージェントをテストする

テスト開始前のリソースの状態:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA がマスターとして実行されているノードで、ネットワーク インターフェイスを無効にして Azure フェンス エージェントのセットアップをテストできます。
ネットワーク エラーをシミュレートする方法の説明については、[Red Hat のサポート情報記事 79523](https://access.redhat.com/solutions/79523) を参照してください。 この例では、net_breaker スクリプトを使用して、ネットワークに対するすべてのアクセスをブロックします。

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

クラスターの構成によっては、仮想マシンが再起動するか停止します。
`stonith-action` 設定を off に設定すると、仮想マシンが停止し、実行中の仮想マシンにリソースが移行されます。

> [!NOTE]
> 仮想マシンがオンラインになるまで、最大 15 分かかります。

`AUTOMATED_REGISTER="false"` を設定した場合、仮想マシンを再起動すると、SAP HANA リソースがセカンダリとしての起動に失敗します。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

テスト後のリソースの状態:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>手動フェールオーバーをテストする

テスト開始前のリソースの状態:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

hn1-db-0 ノードでクラスターを停止して、手動フェールオーバーをテストできます。

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

フェールオーバー後、クラスターを再度開始できます。 `AUTOMATED_REGISTER="false"` を設定した場合、hn1-db-0 ノードの SAP HANA リソースはセカンダリとして起動できません。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

テスト後のリソースの状態:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>次の手順

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA L インスタンスの高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください
