---
title: "Azure Virtual Machines (VM) 上の SAP HANA の高可用性 | Microsoft Docs"
description: "Azure Virtual Machines (VM) 上の SAP HANA の高可用性を実現する。"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure Virtual Machines (VM) 上の SAP HANA の高可用性 | Microsoft Docs

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

オンプレミスでは、HANA システム レプリケーションまたは共有記憶域を使用して、SAP HANA の高可用性を実現できます。
現在は、Azure での HANA システム レプリケーションのセットアップのみがサポートされています。 SAP HANA レプリケーション は、1 つのマスター ノードと、少なくとも 1 つのスレーブ ノードで構成されています。 マスター ノードのデータに対する変更は、スレーブ ノードに同期的または非同期的にレプリケートされます。

この記事では、仮想マシンのデプロイおよび構成方法、クラスター フレームワークのインストール方法、SAP HANA システム レプリケーションのインストールおよび構成方法について説明します。
サンプルの構成では、インストールのコマンドで、インスタンス番号として 03、HANA システム ID としてHDB が使用されています。

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
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事
* [SAP HANA SR Performance Optimized Scenario (SAP HANA SR パフォーマンス最適化シナリオ)][suse-hana-ha-guide] このガイドには、SAP HANA システム レプリケーションをオンプレミスでセットアップするときに必要なすべての情報が記載されています。 このガイドをベースラインとして使用します。

## <a name="deploying-linux"></a>Linux のデプロイ

SAP HANA のリソース エージェントは、SUSE Linux Enterprise Server for SAP Applications に含まれています。
Azure Marketplace には、BYOS (サブスクリプション持ち込み) 付き SUSE Linux Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい仮想マシンのデプロイに使用できます。

### <a name="manual-deployment"></a>手動によるデプロイ

1. リソース グループを作成します
1. 仮想ネットワークを作成します
1. ストレージ アカウントを 2 つ作成します
1. 可用性セットを作成します  
   更新ドメインの最大数を設定します
1. ロード バランサー (内部) を作成します  
   上記手順の VNET を選択します
1. 仮想マシン 1 を作成します  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   ストレージ アカウント 1 を選択します  
   可用性セットを選択します  
1. 仮想マシン 2 を作成します  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   ストレージ アカウント 2 を選択します   
   可用性セットを選択します  
1. データ ディスクを追加します
1. ロード バランサーを構成します
    1. フロントエンド IP プールの作成
        1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
        1. 新規のフロントエンド IP プールの名前を入力します (例: hana-frontend)
       1. [OK] をクリックします
        1. 新規のフロントエンド IP プールが作成されたら、その IP アドレスをメモします
    1. バックエンド プールの作成
        1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
        1. 新規のバックエンド プールの名前を入力します (例: hana-backend)
        1. [仮想マシンの追加] をクリックします
        1. 前の手順で作成した可用性セットを選択します
        1. SAP HANA クラスターの仮想マシンを選択します
        1. [OK] をクリックします
    1. 正常性プローブの作成
       1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
        1. 新しい正常性プローブの名前を入力します (例: hana-hp)
        1. プロトコルに TCP、ポートに 625**03** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
        1. [OK] をクリックします
    1. 負荷分散規則の作成
        1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
        1. 新しい負荷分散規則の名前を入力します (例: hana-lb-3**03**15)
        1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: hana-frontend)
        1. プロトコルは TCP のままにし、ポートに「3**03**15」を入力します
        1. アイドル タイムアウトを 30 分に増やします
       1. **Floating IP を有効にします**
        1. [OK] をクリックします
        1. ポート 3**03**17 に対しても上記の手順を繰り返します

### <a name="deploy-with-template"></a>テンプレートを使用したデプロイ
Github にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイすることができます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。テンプレートをデプロイするには、次の手順に従います。

1. Azure Portal で、[データベース テンプレート][template-multisid-db]または[集約型テンプレート][template-converged]を開きます。データベース テンプレートでは、データベースの負荷分散規則しか作成できませんが、集約型テンプレートでは、ASCS/SCS および ERS (Linux のみ) のインスタンスの負荷分散規則も作成できます。 SAP NetWeaver ベースのシステムをインストールして、同じコンピューターに ASCS/SCS インスタンスもインストールする場合は、[集約型テンプレート][template-converged]を使用します。
1. 次のパラメーターを入力します
    1. SAP システム ID  
       インストールする SAP システムの SAP システム ID を入力します。 ID は、デプロイされるリソースのプレフィックスとして使われます。
    1. スタックの種類 (集約型テンプレートを使用する場合のみ適用可能)  
       SAP NetWeaver のスタックの種類を選択します
    1. OS の種類  
       いずれかの Linux ディストリビューションを選択します。 この例では、SLES 12 BYOS を選択します
    1. データベースの種類  
       HANA を選択します
    1. Sap System Size (SAP システムのサイズ)  
       新しいシステムで提供する SAPS の量。 システムで必要な SAPS の量が不明な場合、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
    1. システムの可用性  
       [HA] を選択します
    1. [管理ユーザー名] と[管理パスワード]  
       コンピューターへのログオンで使用できる新しいユーザーが作成されます。
    1. New Or Existing Subnet (新規または既存のサブネット)  
       新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、既存のものを選択します。
    1. サブネット ID  
    仮想マシンを接続するサブネットの ID。 仮想マシンをオンプレミス ネットワークに接続する VPN または Expressroute 仮想ネットワークのサブネットを選択します。 通常、ID は /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> のようになります。

## <a name="setting-up-linux-ha"></a>Linux HA のセットアップ

次の各手順の先頭には、[A] - 全ノードが該当、[1] - ノード 1 のみ該当、[2] - ノード 2 のみ該当、のいずれかが付いています。

1. [A] SLES for SAP BYOS のみ - SLES を登録してリポジトリを使用できるようにします
1. [A] - SLES for SAP BYOS のみ - パブリック クラウド モジュールを追加します
1. [A] SLES を更新します
    ```bash
    sudo zypper update

    ```

1. [1] SSH アクセスを有効にします
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] SSH アクセスを有効にします
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] SSH アクセスを有効にします
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] HA の拡張機能をインストールします
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] ディスク レイアウトをセットアップします
    1. LVM  
    通常、データおよびログ ファイルを格納するボリュームには、LVM を使用することを推奨しています。 次の例は、仮想マシンに 4 つのデータ ディスクがアタッチされていて、これを使用して 2 つのボリュームを作成するということを前提としています。
        * 使用するすべてのディスクの物理ボリュームを作成します。
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * データ ファイルのボリューム グループ、ログ ファイルのボリューム グループ、SAP HANA の共有ディレクトリのボリューム グループを作成します
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * 論理ボリュームを作成します
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * マウント ディレクトリを作成し、すべての論理ボリュームの UUID をコピーします
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * 3 つの論理ボリュームの fstab エントリを作成します
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    この行を /etc/fstab に挿入します
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * 新しいボリュームをマウントします
    <pre><code>
    sudo mount -a
    </code></pre>
    1. プレーン ディスク  
       小さなシステムまたはデモ システムの場合、HANA のデータおよびログ ファイルを 1 つのディスクに配置することができます。 次のコマンドで、/dev/sdc にパーティションを作成し、xfs でフォーマットします。
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>/dev/sdc1 の ID をメモしておきます
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] すべてのホストにホスト名解決をセットアップします  
    DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます
    ```bash
    sudo vi /etc/hosts
    ```
    次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] クラスターをインストールします
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] クラスターにノードを追加します
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] hacluster のパスワードを同じパスワードに変更します
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] 他のトランスポートを使用したり、ノードリストを追加したりできるように、corosync を構成します。 この作業をしないとクラスターが機能しません。
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    次の太字の内容をファイルに追加します。
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    その後、corosync サービスを再起動します

    ```bash
    sudo service corosync restart
    
    ```

1. [A] HANA HA パッケージをインストールします  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>SAP HANA のインストール

[SAP HANA SR Performance Optimized Scenario (SAP HANA SR パフォーマンス最適化シナリオ) ガイド][suse-hana-ha-guide]の 4 章に従って、SAP HANA システム レプリケーションをインストールします。

1. [A] HANA DVD から hdblcm を実行します
    * Choose installation (インストールの選択) -> 1
    * Select additional components for installation (追加でインストールするコンポーネントの選択) -> 1
    * Enter Installation Path [/hana/shared] \(インストールのパス (/hana/shared) の入力): -> ENTER
    * Enter Local Host Name [..] \(ローカルのホスト名 (..) の入力): -> ENTER
    * Do you want to add additional hosts to the system? (システムに別のホストを追加しますか?)  (y/n) [n]: -> ENTER
    * Enter SAP HANA System ID (SAP HANA のシステム ID を入力): <SID of HANA e.g. HDB>
    * Enter Instance Number [00] \(インスタンス番号 (00) の入力):   
  HANA のインスタンス番号です。 Azure テンプレートを使用した場合、または上記サンプルに従った場合は、「03」を使用します
    * Select Database Mode / Enter Index [1] \(データベース モードの選択/インデックス (1) の入力): -> ENTER
    * Select System Usage / Enter Index [4] \(システム使用率の選択/インデックス (4) の入力):  
  システムの使用率を選択します
    * Enter Location of Data Volumes [/hana/data/HDB] \(データ ボリュームの場所 (/hana/data/HDB) の入力): -> ENTER
    * Enter Location of Log Volumes [/hana/log/HDB] \(ログ ボリュームの場所 (/hana/log/HDB) の入力): -> ENTER
    * Restrict maximum memory allocation? (メモリの最大割り当てを制限しますか?)  [n]: -> ENTER
    * Enter Certificate Host Name For Host '...' (ホスト '...' の証明書のホスト名を入力)  [...]: -> ENTER
    * Enter SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを入力):
    * Confirm SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを確認):
    * Enter System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを入力):
    * Confirm System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを確認):
    * Enter System Administrator Home Directory [/usr/sap/HDB/home] \(システム管理者のホーム ディレクトリ (/usr/sap/HDB/home) を入力): -> ENTER
    * Enter System Administrator Login Shell [/bin/sh] \(システム管理者のログイン シェル (/bin/sh) を入力): -> ENTER
    * Enter System Administrator User ID [1001] \(システム管理者のユーザー ID (1001) を入力): -> ENTER
    * Enter ID of User Group (sapsys) [79] \(ユーザー グループ (sapsys) の ID (79) を入力): -> ENTER
    * Enter Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを入力):
    * Confirm Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを確認):
    * Restart system after machine reboot? (コンピューターの再起動後にシステムを再起動しますか?)  [n]: -> ENTER
    * Do you want to continue? (続行してもよろしいですか?)  (y/n):  
  内容を確認し、「y」を入力して続行します
1. [A] SAP Host Agent をアップグレードします  
  [SAP ソフトウェアセンター][sap-swcenter]から最新の SAP Host Agent アーカイブをダウンロードし、次のコマンドを実行してエージェントをアップグレードします。 アーカイブのパスを置き換えて、ダウンロードしたファイルを示すようにします。
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] HANA レプリケーションを作成します (root として)  
    次のコマンドを実行します。 太字の文字列 (HANA システム ID: HDB、インスタンス番号: 03 ) を、実際の SAP HANA のインストールの値に置き換えてください。
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] キーストア エントリを作成します (root として)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] データベースをバックアップします (root として)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] sapsid ユーザー (例: hdbadm) に切り替えて、プライマリ サイトを作成します。
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] sapsid ユーザー (例: hdbadm) に切り替えて、セカンダリ サイトを作成します。
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>クラスター フレームワークの構成

既定の設定を変更します

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>STONITH デバイスの作成

STONITH デバイスは、サービス プリンシパルを使用して Microsoft Azure を承認します。 サービス プリンシパルを作成するには、次に手順に従ってください。

1. <https://portal.azure.com> に移動します
1. [Azure Active Directory] ブレードを開きます  
   [プロパティ] に移動し、ディレクトリの ID をメモします。 これは、**テナント ID** です。
1. [アプリの登録] を選択します
1. [追加] をクリックします。
1. 名前を入力して、アプリケーションの種類に [Web アプリ/API] を選択し、サインオン URL (例: http://localhost) を入力します。その後、[作成] をクリックします
1. サインオン URL は使用されず、任意の有効な URL を指定することができます
1. 新しいアプリを選択し、[設定] タブで [キー] をクリックします
1. 新しいキーの説明を入力し、[Never expires] \(有効期限なし) を選択して [保存] をクリックします
1. 値をメモします。 この値は、サービス プリンシパルの**パスワード**として使用します
1. アプリケーション ID をメモします。 この値は、サービス プリンシパルのユーザー名 (下記の手順の**ログイン ID**) として使用します

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 クラスターのすべての仮想マシンを開始および停止 (割り当て解除) する権限を、サービス プリンシパルに付与する必要があります。

1. https://portal.azure.com に移動します
1. [All resources] \(すべてのリソース) ブレードを開きます
1. 仮想マシンを選択します
1. [アクセス制御 (IAM)] を選択します
1. [追加] をクリックします。
1. [所有者] のロールを選択します
1. 上記で作成したアプリケーションの名前を入力します
1. [OK] をクリックします

仮想マシンのアクセス許可を編集したあとで、クラスターの STONITH デバイスを構成できます。

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>SAP HANA リソースの作成

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>クラスターのセットアップのテスト
次の章では、セットアップをテストする方法について説明します。 テストはすべて root で行い、仮想マシン saphanavm1 で SAP HANA マスターが実行されていることを前提とします。

#### <a name="fencing-test"></a>フェンスのテスト

フェンス エージェントのセットアップをテストするには、ノード saphanavm1 でネットワーク インターフェイスを無効にします。

<pre><code>
sudo ifdown eth0
</code></pre>

クラスターの構成によって、仮想マシンが再起動するか停止します。
stonith-action を off に設定すると、仮想マシンが停止し、実行中の仮想マシンにリソースが移行します。

AUTOMATED_REGISTER を false に設定した場合、仮想マシンを再起動すると、SAP HANA リソースがセカンダリとしての起動に失敗します。 この場合、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成する必要があります。

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>手動フェールオーバーのテスト

手動フェールオーバーをテストするには、ノード saphanavm1 で Pacemaker サービスを停止します。
<pre><code>
service pacemaker stop
</code></pre>

フェールオーバー後、サービスを再度開始できます。 AUTOMATED_REGISTER を false に設定した場合、saphanavm1 の SAP HANA リソースがセカンダリとしての起動に失敗します。 この場合、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成する必要があります。

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>移行のテスト

SAP HANA マスター ノードは、次のコマンドを実行すると移行できます
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

この場合、SAP HANA マスター ノードおよび仮想 IP アドレスを含むグループが saphanavm2 に移行します。
AUTOMATED_REGISTER を false に設定した場合、saphanavm1 の SAP HANA リソースがセカンダリとしての起動に失敗します。 この場合、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成する必要があります。

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

移行では場所の制約が作成されますが、これは再度削除する必要があります。

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

また、セカンダリ ノードのリソースの状態をクリーンアップする必要もあります

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>次のステップ
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、障害の復旧を計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性と障害復旧](hana-overview-high-availability-disaster-recovery.md)」を参照してください。 
