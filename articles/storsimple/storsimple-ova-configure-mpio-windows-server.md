---
title: "StorSimple Virtual Array ホストで MPIO を構成する | Microsoft Docs"
description: "Windows Server 2012 R2 を実行するホストに接続されている StorSimple Virtual Array のマルチパス I/O (MPIO) を構成する方法を説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57b54af8-ca0d-4a89-bb65-5f4b9eb740ea
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7775214cc187222e97a9168e6e5fd3fbbc8e2006


---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Windows Server ホストで StorSimple Virtual Array 向けにマルチパス I/O を構成する
## <a name="overview"></a>概要
この記事では、Windows Server ホストにマルチパス I/O (MPIO) 機能をインストールし、StorSimple 専用ボリュームの構成設定を適用して、StorSimple ボリュームに対する MPIO を検証する方法について説明します。 次の手順は、2 つのネットワーク インターフェイスを持つ StorSimple 1200 Virtual Array が 2 つのネットワーク インターフェイスを持つ Windows Server ホストに接続されていることを前提としています。 この記事に記載されている内容は、StorSimple Virtual Array にのみ適用されます。 StorSimple 8000 シリーズ デバイスについては、「 [StorSimple デバイスのマルチパス IO の構成](storsimple-configure-mpio-windows-server.md)」を参照してください。 

Windows Server で MPIO 機能を使用することで、フォールト トレランスと可用性に優れたストレージ構成を構築できます。 MPIO では、アダプター、ケーブル、スイッチなどの冗長な物理パス コンポーネントを使用して、サーバーとストレージ デバイスの間に論理パスを作成します。 コンポーネントのエラーによって論理パスに障害が発生した場合は、マルチパス ロジックで I/O の代替パスが使用され、アプリケーションから引き続きデータにアクセスできるようにします。 さらに構成によっては、これらのパスの間で負荷を分散することで、パフォーマンスの向上にも貢献します。 詳細については、「 [MPIO の概要](https://technet.microsoft.com/library/cc725907.aspx "MPIO の概要 and features")」を参照してください。  

StorSimple ソリューションの可用性を高めるには、StorSimple 1200 Virtual Array (オンプレミスの仮想デバイスとも呼ばれます) に接続している Windows Server ホストで MPIO を構成します。 構成後、ホスト サーバーはリンク、ネットワーク、またはインターフェイスの障害を許容できるようになります。 

MPIO を構成する手順は次のとおりです。 

* 構成の前提条件
* 手順 1. Windows Server ホストに MPIO をインストールする
* 手順 2. StorSimple ボリュームの MPIO を構成する
* 手順 3. ホストに StorSimple ボリュームをマウントする

これらの各手順を以下のセクションで説明します。

## <a name="prerequisites"></a>前提条件
このセクションでは、Windows Server ホストと仮想アレイの構成に関する前提条件について説明します。

### <a name="on-windows-server-host"></a>Windows Server ホスト
* Windows Server ホストで 2 つのネットワーク インターフェイスが有効になっていることを確認します。

### <a name="on-storsimple-virtual-array"></a>StorSimple Virtual Array
* 仮想アレイを iSCSI サーバーとして構成する必要があります。 詳細については、「 [StorSimple Virtual Array をデプロイする – 仮想デバイスを iSCSI サーバーとしてセットアップする](storsimple-ova-deploy3-iscsi-setup.md)」を参照してください。 アレイで 1 つ以上のネットワーク インターフェイスを有効にする必要があります。   
* 仮想アレイのネットワーク インターフェイスに Windows Server ホストから到達できる必要があります。
* StorSimple Virtual Array に 1 つ以上のボリュームを作成する必要があります。 詳細については、StorSimple 1200 Virtual Array に [ボリュームを追加する](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) 方法を参照してください。 この手順では、仮想アレイに 3 つのボリューム (下図のとおり、ローカル固定ボリューム 2 つと階層化ボリューム 1 つ) を作成しました。
  
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>StorSimple Virtual Array のハードウェア構成
次の図は、この手順で使用する Windows Server ホストと StorSimple Virtual Array で高可用性と負荷分散のマルチパスを実装するためのハードウェア構成を示しています。  

![mpio hardware configuration](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

前の図から次のことがわかります。

* Hyper-V にプロビジョニングされている StorSimple Virtual Array は、iSCSI サーバーとして構成されている単一ノード構成のアクティブなデバイスです。
* アレイでは、2 つの仮想ネットワーク インターフェイスが有効になっています。 下図のように、1200 Virtual Array のローカル Web UI で **[ネットワーク設定]** に移動し、2 つのネットワーク インターフェイスが有効になっていることを確認します。
  
    ![Network interfaces enabled on 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
  
    有効になっているネットワーク インターフェイス (既定ではイーサネットとイーサネット 2) の IPv4 アドレスをメモし、後で使用できるようにホスト上に保存します。
* Windows Server ホストで 2 つのネットワーク インターフェイスが有効になっています。 ホストとアレイの接続されたインターフェイスが同じサブネット上にある場合、利用できるパスは 4 つあります。 この手順がこれに当てはまります。 ただし、アレイの各ネットワーク インターフェイスとホスト インターフェイスが別の IP サブネット上にある (およびルーティングできない) 場合、利用できるのは 2 つのパスのみです。

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>手順 1. Windows Server ホストに MPIO をインストールする
MPIO はオプションの機能であり、Windows サーバーに既定ではインストールされていません。 サーバー マネージャーを使用して、機能としてインストールする必要があります。 Windows Server ホストにこの機能をインストールするには、次の手順を実行します。

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>手順 2. StorSimple ボリュームの MPIO を構成する
StorSimple ボリュームを識別するには、MPIO を構成する必要があります。 StorSimple ボリュームを認識するように MPIO を構成するには、次の手順を実行します。

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>手順 3. ホストに StorSimple ボリュームをマウントする
Windows Server で MPIO が構成されると、StorSimple アレイに作成されたボリュームをマウントし、MPIO を活用して冗長性を確保できます。 ボリュームをマウントするには、次の手順を実行します。

#### <a name="to-mount-volumes-on-the-host"></a>ホストにボリュームをマウントするには
1. Windows Server ホストで、 **[iSCSI イニシエーターのプロパティ]** ウィンドウを開きます。 **[サーバー マネージャー]、[ダッシュボード]、[ツール]、[iSCSI イニシエーター]** の順にクリックします。
2. **[iSCSI イニシエーターのプロパティ]** ダイアログ ボックスで、[探索] タブをクリックし、**[ターゲット ポータルの探索]** をクリックします。
3. **[ターゲット ポータルの探索]** ダイアログ ボックスで、次の手順を実行します。
   
   * StorSimple Virtual Array で有効になっている 1 つ目のネットワーク インターフェイスの IP アドレスを入力します。 既定では、このネットワーク インターフェイスは **Ethernet**です。 
   * **[OK]** をクリックして、**[iSCSI イニシエーターのプロパティ]** ダイアログ ボックスに戻ります。
     
     > [!IMPORTANT]
     > **iSCSI 接続のプライベート ネットワークを使用している場合は、プライベート ネットワークに接続されている DATA ポートの IP アドレスを入力します。**
     > 
     > 
4. アレイの 2 つ目のネットワーク インターフェイス (Ethernet 2 など) に対して手順 2. ～ 3. を繰り返します。 
5. **[iSCSI イニシエーターのプロパティ]** ダイアログ ボックスで、**[ターゲット]** タブを選択します。 仮想アレイの場合、 **[検出されたターゲット]**に各ボリュームがターゲットとして表示されます。 この例では、(3 つのボリュームに対応する) 3 つのターゲットが表示されます。
   
    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)
6. **[接続]** をクリックして、StorSimple アレイとの iSCSI セッションを確立します。 **[ターゲットへの接続]** ダイアログ ボックスが表示されます。 **[複数パスを有効にする]** チェック ボックスをオンにします。 **[詳細設定]**をクリックします。
   
    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)
7. **[詳細設定]** ダイアログ ボックスで、次の手順を実行します。                                        
   
   * **[ローカル アダプター]** ボックスの一覧で、**[Microsoft iSCSI イニシエーター]** を選択します。
   * **[イニシエーター IP]** ボックスの一覧で、ホストの IP アドレスを選択します。
   * **[ターゲット ポータル IP]** ボックスの一覧で、アレイ インターフェイスの IP を選択します。
   * **[OK]** をクリックして、**[iSCSI イニシエーターのプロパティ]** ダイアログ ボックスに戻ります。
     
     ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)
8. **[プロパティ]**をクリックします。 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
9. **[プロパティ]** ダイアログ ボックスで、**[セッションの追加]** をクリックします。
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. **[ターゲットへの接続]** ダイアログ ボックスで、**[複数パスを有効にする]** チェック ボックスをオンにします。 **[詳細設定]**をクリックします。
11. **[詳細設定]** ダイアログ ボックスで、次の手順を実行します。                                        
    
    * **[ローカル アダプター]** ボックスの一覧で、[Microsoft iSCSI イニシエーター] を選択します。
    * **[イニシエーター IP]** ボックスの一覧で、ホストに対応する IP アドレスを選択します。 この例では、アレイ上の 2 つのネットワーク インターフェイスを、ホスト上の 1 つのネットワーク インターフェイスに接続しています。 そのため、このインターフェイスは最初のセッションで指定されたものと同じです。
    * **[ターゲット ポータル IP]** ボックスの一覧で、アレイで有効になっている 2 つ目のデータ インターフェイスの IP アドレスを選択します。
    * **[OK]** をクリックして、[iSCSI イニシエーターのプロパティ] ダイアログ ボックスに戻ります。 2 番目のセッションをターゲットに追加しました。
      
       ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)
    * 目的のセッション (パス) を追加した後、**[iSCSI イニシエーターのプロパティ]** ダイアログ ボックスで、ターゲットを選択し、**[プロパティ]** をクリックします。 **[プロパティ]** ダイアログ ボックスの [セッション] タブで、可能なパスの順列に対応する 4 つのセッション ID を確認します。 セッションを取り消すには、セッション ID の横にあるチェック ボックスをオンにして、 **[切断]**をクリックします。
    * セッション内に示されるデバイスを表示するには、 **[デバイス]** タブを選択します。 選択したデバイスの MPIO ポリシーを構成するには、 **[MPIO]**をクリックします。 **
    * [詳細]** ダイアログ ボックスが表示されます。**[MPIO]** タブで、適切な **[負荷分散ポリシー]** の設定を選択できます。**[アクティブ]** または **[スタンバイ]** のパスの種類も表示することができます。
12. 追加のセッション (パス) をターゲットに追加するには、手順 8. ～ 11. を繰り返します。 ホスト上の 2 つのインターフェイスと仮想アレイ上の 2 つのインターフェイスを使用して、各ターゲットに合計 4 つのセッションを追加できます。 
    
    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)
13. (ターゲットとして表示されている) ボリュームごとにこれらの手順を繰り返す必要があります。
    
    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)
14. **[サーバー マネージャー]、[ダッシュボード]、[コンピューターの管理]** の順に移動して、**[コンピューターの管理]** を開きます。 左側のウィンドウで、**[ストレージ] の [ディスクの管理]** をクリックします。 このホストに表示される、StorSimple Virtual Array 上に作成したボリュームは、新しいディスクとして **[ディスクの管理]** に表示されます。
15. ディスクを初期化して、新しいボリュームを作成します。 フォーマット処理中に 64 KB のアロケーション ユニット サイズ (AUS) を選択します。 利用可能なすべてのボリュームに対して、このプロセスを繰り返します。
    
    ![[ディスクの管理]](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)
16. **[ディスクの管理]** で、**[ディスク]** を右クリックし、**[プロパティ]** を選択します。
17. **[マルチパス ディスクのデバイス プロパティ]** ダイアログ ボックスで、**[MPIO]** タブをクリックします。
    
    ![Disk Properties](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)
18. **[DSM 名]** セクションで、**[詳細]** をクリックし、パラメーターが既定のパラメーターに設定されていることを確認します。 既定のパラメーターは次のとおりです。
    
    * パス確認期間 = 30
    * 再試行回数 = 3
    * PDO 削除期間 = 20
    * 再試行間隔 = 1
    * パス確認を有効化する = オフ
    
    > [!NOTE]
    > **既定のパラメーターは変更しないでください。**
    > 
    > 

## <a name="next-steps"></a>次のステップ
[StorSimple Manager サービスを使用した StorSimple Virtual Array の管理](storsimple-ova-manager-service-administration.md)の詳細を確認してください。




<!--HONumber=Nov16_HO3-->


