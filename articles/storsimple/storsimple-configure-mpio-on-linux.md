---
title: StorSimple Linux ホストの MPIO の構成 | Microsoft Docs
description: CentOS 6.6 を実行している Linux ホストに接続されている StorSimple で MPIO を構成します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: d6d4a5b9688540e5aa96dd8789dbb609aedeca97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077847"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>CentOS を実行している StorSimple ホスト上の MPIO の構成
この記事では、Centos 6.6 ホスト サーバー上でマルチパス IO (MPIO) を構成するために必要な手順を説明します。 ホスト サーバーは、iSCSI イニシエーターを使用して高可用性を実現するために、Microsoft Azure StorSimple デバイスに接続します。 マルチパス デバイスの自動検出と StorSimple ボリューム専用の具体的な設定について詳しく説明します。

この手順は StorSimple 8000 シリーズのデバイスのすべてのモデルに適用できます。

> [!NOTE]
> この手順は、StorSimple Cloud Appliance では使用できません。 詳細については、クラウド アプライアンスのホスト サーバーを構成する方法を参照してください。


## <a name="about-multipathing"></a>マルチパスについて
マルチパス機能を使用すると、ホスト サーバーとストレージ デバイス間に複数の I/O パスを構成することができます。 これらの I/O パスは、別個のケーブル、スイッチ、ネットワーク インターフェイス、コントローラーを使用することができる物理 SAN 接続です。 マルチパスは I/O パスを集約して、集約済みのすべてのパスに関連付けられた新しいデバイスを構成します。

マルチパスの目的は 2 つあります。

* **高可用性**: I/O パスのいずれかの要素 (ケーブル、スイッチ、ネットワーク インターフェイス、コントローラーなど) に障害が生じた場合に代替パスを提供します。
* **負荷分散**: ストレージ デバイスの構成に応じて、I/O パス上の負荷を検出し、その負荷を動的に再調整して、パフォーマンスを向上できます。

### <a name="about-multipathing-components"></a>マルチパス コンポーネントについて
Linux のマルチパスは、次の表のように、カーネル コンポーネントとユーザースペース コンポーネントで構成されます。

* **カーネル**: 主なコンポーネントは、*device-mapper* です。これは、I/O を経路変更し、パスとパス グループのフェールオーバーをサポートします。

* **ユーザースペース**: device-mapper マルチパス モジュールに指示してマルチパス デバイスを管理する *multipath-tools* です。 このツールは次の要素で構成されます。
   
   * **Multipath**: マルチパス デバイスを一覧表示および構成します。
   * **Multipathd**: マルチパスを実行し、パスを監視するデーモンです。
   * **Devmap-name**: devmap の udev へ意味のあるデバイス名を提供します。
   * **Kpartx**: 線形 devmap をデバイス パーティションにマップし、マルチパス マップを分割可能にします。
   * **Multipath.conf**: 組み込みの構成テーブルを上書きするために使用するマルチパス デーモンの構成ファイルです。

### <a name="about-the-multipathconf-configuration-file"></a>multipath.conf 構成ファイルについて
構成ファイル `/etc/multipath.conf` によって、マルチパス機能の多くをユーザーが構成できます。 `multipath` コマンドとカーネル デーモン `multipathd` では、このファイルに記載された情報が使用されます。 ファイルが参照されるのは、マルチパス デバイスの構成中のみです。 `multipath` コマンドを実行する前に、すべての変更を加えたことを確認してください。 ファイルを後で変更した場合は、その変更が有効になるように、multipathd を停止してからもう一度開始する必要があります。

multipath.conf には次の 5 つのセクションがあります。

- **システム レベルの既定値** *(defaults)* : システム レベルの既定値をオーバーライドすることができます。
- **ブラックリストに載っているデバイス** *(blacklist)* : device-mapper で制御してはいけないデバイスの一覧を指定することができます。
- **ブラックリストの例外** *(blacklist_exceptions)* : ブラックリストに載っている場合でもマルチパス デバイスとして扱う特定のデバイスを指定することができます。
- **記憶域コントローラー固有の設定** *(devices)* : 仕入先および製品の情報を持つデバイスに適用する構成設定を指定することができます。
- **デバイス固有の設定** *(multipaths)* : このセクションは、個々の LUN の構成設定を微調整するために使用することができます。

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Linux ホストに接続されている StorSimple でマルチパスを構成する
Linux ホストに接続されている StorSimple デバイスを構成して、高可用性と負荷分散を実現することができます。 たとえば、SAN に接続されている 2 つのインターフェイスが Linux ホストに存在し、デバイスには SAN に接続する 2 つのインターフェイスがあり、これらのインターフェイスが同じサブネット上にある場合、4 つのパスを利用できるようになります。 ただし、デバイス上の各 DATA インターフェイスとホスト インターフェイスが別の IP サブネット上にある (およびルーティングできない) 場合、利用できるのは 2 つのパスのみです。 マルチパスを構成して、使用可能なすべてのパスの自動検出、これらのパスの負荷分散アルゴリズムの選択、StorSimple 専用ボリュームの特定の構成設定の適用、マルチパスの有効化と確認を実行することができます。

次の手順では、2 つのネットワーク インターフェイスを持つ StorSimple デバイスが 2 つのネットワーク インターフェイスを持つホストに接続されている場合に、マルチパスを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件
このセクションでは、CentOS のサーバーと StorSimple デバイスの構成の前提条件について説明します。

### <a name="on-centos-host"></a>CentOS ホスト
1. CentOS ホストに、有効な 2 つのネットワーク インターフェイスがあることを確認します。 型:
   
    `ifconfig`
   
    次の例は、2 つのネットワーク インターフェイス (`eth0` と `eth1`) がホスト上に存在する場合の出力を示しています。
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. CentOS サーバーに *iSCSI-initiator-utils* をインストールします。 次の手順を実行して、 *iSCSI-initiator-utils*をインストールします。
   
   1. CentOS ホストに `root` としてログオンします。
   1. *iSCSI-initiator-utils*をインストールします。 型:
      
       `yum install iscsi-initiator-utils`
   1. *iSCSI-Initiator-utils* が正常にインストールされた後で、iSCSI サービスを開始します。 型:
      
       `service iscsid start`
      
       時折、`iscsid` が実際には開始されず、`--force` オプションが必要になる場合があります。
   1. ブート時に iSCSI イニシエーターが確実に有効であるようにするには、 `chkconfig` コマンドを使用してサービスを有効にします。
      
       `chkconfig iscsi on`
   1. 正しく設定されていることを確認するには、次のコマンドを実行します。
      
       `chkconfig --list | grep iscsi`
      
       サンプル出力を次に示します。
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       上の例から、iSCSI 環境では、2、3、4、5 の実行レベルでのブート時に実行されることが確認できます。
1. *device-mapper-multipath*をインストールします。 型:
   
    `yum install device-mapper-multipath`
   
    インストールが開始されます。 確認を求められたら、「 **Y** 」を入力して続行します。

### <a name="on-storsimple-device"></a>StorSimple デバイス
StorSimple デバイスに必要なものは次のとおりです。

* iSCSI 用に有効になっている 2 つ以上のインターフェイス。 StorSimple デバイス上の 2 つのインターフェイスが iSCSI に対応していることを確認するには、StorSimple デバイスの Azure クラシック ポータルで次の手順を実行します。
  
  1. StorSimple デバイスのクラシック ポータルにログインします。
  1. StorSimple Manager サービスを選択し、 **[デバイス]** をクリックして特定の StorSimple デバイスを選択します。 **[構成]** をクリックし、ネットワーク インターフェイスの設定を確認します。 2 つの iSCSI 対応ネットワーク インターフェイスを示したスクリーン ショットは次のとおりです。 DATA 2 と DATA 3 の両方で、iSCSI の 10 GbE インターフェイスが有効になっています。
     
      ![MPIO StorSimple DATA 2 の構成](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 の構成](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      **[構成]** ページで次の手順に従います。
     
     1. 両方のネットワーク インターフェイスが確実に iSCSI に対応するようにします。 **[iSCSI 対応]** フィールドを **[はい]** に設定する必要があります。
     1. ネットワーク インターフェイスの速度が同じで、両方が 1 GbE または 10 GbE になるようにします。
     1. iSCSI 対応インターフェイスの IPv4 アドレスをメモし、ホスト上で後ほど使用するために保存します。
* StorSimple デバイスの iSCSI インターフェイスは、CentOS サーバーから到達できる必要があります。
      これを確認するには、ホスト サーバーの StorSimple iSCSI 対応ネットワーク インターフェイスの IP アドレスを指定する必要があります。 使用するコマンドと DATA2 (10.126.162.25) および DATA3 (10.126.162.26) の対応する出力を次に示します。
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>ハードウェア構成
冗長性実現のために別のパスで 2 つの iSCSI ネットワーク インターフェイスを接続することをお勧めします。 次の図は、CentOS サーバーと StorSimple デバイスにおける高可用性と負荷分散のマルチパスのための推奨ハードウェア構成を示しています。

![Linux ホストに対する StorSimple 用の MPIO ハードウェア構成](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

前の図から次のことがわかります。

* StorSimple デバイスは、2 つのコントローラーを持つアクティブ/パッシブ構成にあります。
* 2 つの SAN スイッチが、デバイス コントローラーに接続されています。
* 2 つの iSCSI イニシエーターが、StorSimple デバイスで有効になります。
* 2 つのネットワーク インターフェイスが、CentOS ホストで有効になります。

ホストとデータのインターフェイスがルーティング可能な場合は、前の図の構成によって、デバイスとホスト間に 4 つの独立したパスが生成されます。

> [!IMPORTANT]
> * マルチパスでは、1 GbE と 10 GbE のネットワーク インターフェイスを混在させることはできません。 2 つのネットワーク インターフェイスを使用しているときは、どちらも同じ種類である必要があります。
> * StorSimple デバイス上で、DATA0、DATA1、DATA4、および DATA5 が 1 GbE インターフェイスで、DATA2 および DATA3 が 10 GbE ネットワーク インターフェイスです。
> 
> 

## <a name="configuration-steps"></a>構成の手順
マルチパスの構成手順には、自動検出のための使用可能なパスの構成、使用する負荷分散アルゴリズムの指定、マルチパスの有効化、最後に構成の検証が必要になります。 これらの各手順を以下のセクションで詳しく説明します。

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>手順 1:マルチパスを自動検出用に構成する
マルチパスがサポートされているデバイスを自動的に検出して構成できます。

1. `/etc/multipath.conf` ファイルを初期化します。 型:
   
     `mpathconf --enable`
   
    上のコマンドによって、 `sample/etc/multipath.conf` ファイルが作成されます。
1. マルチパス サービスを開始します。 型:
   
    `service multipathd start`
   
    次の出力が表示されます。
   
    `Starting multipathd daemon:`
1. マルチパスの自動検出を有効にします。 型:
   
    `mpathconf --find_multipaths y`
   
    これによって、 `multipath.conf` の既定値のセクションが次のように変更されます。
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>手順 2:マルチパスを StorSimple ボリューム用に構成する
既定では、すべてのデバイスが multipath.conf ファイルのブラック リストに記載されており、バイパスされます。 ブラックリストの例外を作成して、StorSimple デバイスのボリュームのマルチパスを許可する必要があります。

1. `/etc/mulitpath.conf` ファイルを編集します。 型:
   
    `vi /etc/multipath.conf`
1. multipath.conf ファイルの blacklist_exceptions セクションを見つけます。 StorSimple デバイスを、ブラックリストの例外としてこのセクションに記載する必要があります。 このファイル内の関連する行のコメントを解除して、次のように変更することができます (使用するデバイスの特定のモデルのみを使用します)。
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>手順 3:ラウンド ロビン マルチパスを構成する
この負荷分散アルゴリズムは、バランスの取れたラウンド ロビン方式で、アクティブ コントローラーへの使用可能なすべてのマルチパスを使用します。

1. `/etc/multipath.conf` ファイルを編集します。 型:
   
    `vi /etc/multipath.conf`
1. `defaults` セクションの `path_grouping_policy` を `multibus` に設定します。 `path_grouping_policy` では、指定されていないマルチパスに適用する既定のパスグループ化ポリシーを指定します。 既定値のセクションは次のようになります。
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> `path_grouping_policy` の最も一般的な値は次のとおりです。
> 
> * failover = 優先順位グループごとに 1 つのパス
> * multibus = 1 つの優先順位グループにすべての有効なパス
> 
> 

### <a name="step-4-enable-multipathing"></a>手順 4:マルチパスを有効にする
1. `multipathd` デーモンを再起動します。 型:
   
    `service multipathd restart`
1. 出力は次のようになります。
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>手順 5:マルチパスを確認する
1. まず、iSCSI 接続が、StorSimple デバイスで次のように確立されていることを確認します。
   
   a. StorSimple デバイスを検出します。 型:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    DATA0 の IP アドレスが 10.126.162.25 であり、StorSimple デバイスで送信 iSCSI トラフィック用にポート 3260 が開いているとき、出力は次のようになります。
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    前の出力から、StorSimple デバイスの IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`をコピーします。

   b. ターゲット IQN を使用してデバイスに接続します。 ここでは StorSimple デバイスが iSCSI ターゲットです。 型:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    次の例は、 `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`のターゲット IQN を使用した出力を示します。 この出力は、デバイスの 2 つの iSCSI 対応ネットワーク インターフェイスに正常に接続されていることを示しています。

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    ここに表示されているのが 1 つのホスト インターフェイスと 2 つのパスのみの場合は、iSCSI 用にホストの両方のインターフェイスを有効にする必要があります。 [Linux ドキュメントの詳細な手順](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)を参照してください。

1. ボリュームは、StorSimple デバイスから CentOS サーバーに公開されます。 詳細については、「[手順 6: ボリュームを作成する (StorSimple デバイス上で Azure portal を使用)](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)」を参照してください。

1. 使用可能なパスを確認します。 型:

      ```
      multipath –l
      ```

      次の例は、2 つの使用可能なパスで 1 つのホスト ネットワーク インターフェイスに接続されている StorSimple デバイスの、2 つのネットワーク インターフェイスの出力を示しています。

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>マルチパスのトラブルシューティング
このセクションでは、マルチパスの構成中に問題が発生した場合に役に立つヒントを紹介します。

Q. `multipath.conf` ファイルの変更内容が有効かどうかがわかりません。

A. `multipath.conf` ファイルに変更を加えた場合は、マルチパス サービスを再起動する必要があります。 次のコマンドを入力します。

    service multipathd restart

Q. StorSimple デバイスの 2 つのネットワーク インターフェイスと、ホストの 2 つのネットワーク インターフェイスを有効にしました。 使用可能なパスの一覧を取得すると、表示されるのは 2 つのパスのみです。 使用可能なパスは 4 つ表示されると予想していました。

A. 2 つのパスが同じサブネットにあり、ルーティング可能であることを確認してください。 ネットワーク インターフェイスが別の vLAN にあり、ルーティングできない場合は、2 つのパスのみが表示されます。 これを確認する方法の 1 つに、StorSimple デバイスのネットワーク インターフェイスから、両方のホスト インターフェイスにアクセスできること確認する方法があります。 この検証を実行できるのはサポート セッション経由のみのため、[Microsoft サポートに問い合わせ](storsimple-8000-contact-microsoft-support.md)る必要があります。

Q. 使用可能なパスの一覧を表示しても、出力に何も表示されません。

A. 通常、マルチパスのパスが表示されないのはマルチパス デーモンに問題があると考えられ、多くの場合、`multipath.conf` ファイルに問題があります。

マルチパスの一覧から応答を得られないのは、ディスクがないことを意味している可能性もあるため、ターゲットに接続した後で一部のディスクが実際に表示されることを確認する必要もあります。

* 次のコマンドを使用して SCSI バスを再スキャンします。
  
    `$ rescan-scsi-bus.sh` (sg3_utils パッケージの一部)
* 次のコマンドを入力します。
  
    `$ dmesg | grep sd*`
     
     または
  
    `$ fdisk –l`
  
    これらによって、最近追加したディスクの詳細が返されます。
* これが StorSimple ディスクであるかどうかを判断するには、次のコマンドを使用します。
  
    `cat /sys/block/<DISK>/device/model`
  
    これが StorSimple ディスクであるかどうかを決定する文字列を返します。

比較的可能性は低いですが、古い iscsid pid も原因として考えられます。 次のコマンドを使用して iSCSI セッションからログオフします。

    iscsiadm -m node --logout -p <Target_IP>

ご使用の StorSimple デバイスである iSCSI ターゲットの、接続されているすべてのネットワーク インターフェイスにこのコマンドを繰り返します。 すべての iSCSI セッションからログオフした後で、iSCSI ターゲット IQN を使用して、iSCSI セッションを再確立します。 次のコマンドを入力します。

    iscsiadm -m node --login -T <TARGET_IQN>


Q. 自分のデバイスがホワイトリストに登録されているかどうかわかりません。

A. ご使用のデバイスがホワイトリストに登録されているかどうかを確認するには、次のトラブルシューティング用の対話型コマンドを使用します。

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


詳細については、[マルチパスのトラブルシューティング](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)に関するページを参照してください。

## <a name="list-of-useful-commands"></a>便利なコマンドの一覧
| 確認を求められたら、「 | command | 説明 |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |iSCSI サービスを開始する |
| &nbsp; |`service iscsid stop` |iSCSI サービスを停止する |
| &nbsp; |`service iscsid restart` |iSCSI サービスを再開する |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |指定されたアドレスで使用できるターゲットを検出する |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |ISCSI ターゲットにログインする |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |iSCSI ターゲットからログアウトする |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |iSCSI イニシエーターの名前を出力する |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |ホストで検出された iSCSI セッションとボリュームの状態を確認する |
| &nbsp; |`iscsi –m session` |ホストと StorSimple デバイス間で確立したすべての iSCSI セッションを表示する |
|  | | |
| **マルチパス** |`service multipathd start` |マルチパス デーモンを開始する |
| &nbsp; |`service multipathd stop` |マルチパス デーモンを停止する |
| &nbsp; |`service multipathd restart` |マルチパス デーモンを再開する |
| &nbsp; |`chkconfig multipathd on` </br> または </br> `mpathconf –with_chkconfig y` |ブート時にマルチパス デーモンを開始するようにする |
| &nbsp; |`multipathd –k` |トラブルシューティングのために対話型コンソールを起動する |
| &nbsp; |`multipath –l` |マルチパス接続とデバイスを一覧表示する |
| &nbsp; |`mpathconf --enable` |`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>次の手順
Linux ホストで MPIO を構成しているため、CentoS 6.6 の次のドキュメントも参照することが必要になる場合があります。

* [CentOS での MPIO の設定](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux トレーニング ガイド](http://linux-training.be/linuxsys.pdf)

