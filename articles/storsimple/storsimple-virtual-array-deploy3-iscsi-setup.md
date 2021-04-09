---
title: Microsoft Azure StorSimple Virtual Array の iSCSI サーバーのセットアップ | Microsoft Docs
description: 初期セットアップを実行し、StorSimple iSCSI サーバーを登録して、デバイスのセットアップを完了する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956553"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple Virtual Array をデプロイする - Azure Portal を介して iSCSI サーバーとしてセットアップする

![iscsi セットアップ プロセス フロー](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>概要

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

このデプロイ チュートリアルは、Microsoft Azure StorSimple Virtual Array に適用されます。 このチュートリアルでは、初期セットアップを実行し、StorSimple iSCSI サーバーを登録し、デバイス セットアップを完了した後、iSCSI サーバーとして構成された StorSimple Virtual Array でボリュームを作成、マウント、初期化、およびフォーマットする方法を説明します。 

ここで説明する手順の完了には、約 30 分から 1 時間かかります。 この記事に記載されている情報は、StorSimple Virtual Array にのみ適用されます。

## <a name="setup-prerequisites"></a>セットアップの前提条件

StorSimple Virtual Array を構成およびセットアップする前に、以下のことを確認します。

* 「[StorSimple Virtual Array をデプロイする - Hyper-V で Virtual Array をプロビジョニングする](./storsimple-virtual-array-deploy2-provision-hyperv.md)」または「[StorSimple Virtual Array をデプロイする - VMware で Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-vmware.md)」の説明に従って仮想アレイをプロビジョニングし、そのアレイに接続していること。
* StorSimple Virtual Array を管理するために作成した、StorSimple デバイス マネージャー サービスからのサービス登録キーがあること。 詳細については、「**手順 2: サービス登録キーを取得する**」 ([StorSimple Virtual Array のデプロイ - ポータルの準備](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)) を参照してください。
* 既存の StorSimple デバイス マネージャー サービスに登録する 2 番目以降の仮想アレイの場合は、サービス データ暗号化キーがあるはずです。 このキーは、最初のデバイスがこのサービスに正常に登録されたときに生成されています。 このキーを紛失した場合は、「 **Web UI を使用した StorSimple Virtual Array の管理** 」の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)」を参照してください。

## <a name="step-by-step-setup"></a>セットアップの手順

StorSimple Virtual Array をセットアップして構成するには、次の手順を実行します。

* [ステップ 1:ローカル Web UI の設定を完了し、デバイスを登録する](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* 手順 2:必要なデバイスのセットアップを完了する
* [ステップ 3:ボリュームを追加する](#step-3-add-a-volume)
* [手順 4:ボリュームをマウント、初期化、フォーマットする](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>手順 1:ローカル Web UI の設定を完了し、デバイスを登録する

#### <a name="to-complete-the-setup-and-register-the-device"></a>セットアップを完了し、デバイスを登録するには

1. ブラウザー ウィンドウを開きます。 Web UI の種類に接続するには:
   
    `https://<ip-address of network interface>`
   
    前の手順に記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを知らせるエラーが表示されます。 **[続行]** をクリックして、この Web ページに進みます。
   
    ![セキュリティ証明書エラー](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. 仮想デバイスの Web UI に **StorSimpleAdmin** としてサインインします。 デバイス管理者のパスワード (「[StorSimple Virtual Array をデプロイする - Hyper-V で仮想デバイスをプロビジョニングする](storsimple-virtual-array-deploy2-provision-hyperv.md)」または「[StorSimple Virtual Array をデプロイする - VMware で仮想デバイスをプロビジョニングする](storsimple-virtual-array-deploy2-provision-vmware.md)」の「手順 3: 仮想デバイスを起動する」で変更したパスワード) を入力します。
   
    ![サインイン ページ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. **[ホーム]** ページが表示されます。 このページは、仮想デバイスを構成し、StorSimple デバイス マネージャー サービスに登録するうえで必要なさまざまな設定を掲載しています。 **ネットワーク設定**、**Web プロキシの設定**、**時刻の設定** はオプションであることに注意してください。 必須の設定は、**デバイスの設定** と **クラウドの設定** のみです。
   
    ![ホーム ページ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. **[ネットワーク インターフェイス]** の **[ネットワーク設定]** ページで、DATA 0 が自動的に構成されます。 各ネットワーク インターフェイスは、既定で IP アドレスを自動的に取得するように設定されます (DHCP)。 そのため、IP アドレス、サブネット、およびゲートウェイは自動的に割り当てられます (IPv4 と IPv6 の両方に対して)。
   
    (ブロック記憶域をプロビジョニングするために) iSCSI サーバーとしてデバイスをデプロイする計画がある場合は、 **[IP アドレスを自動的に取得する]** オプションを無効にして、静的 IP アドレスを構成することをお勧めします。
   
    ![ネットワークの設定ページ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    デバイスのプロビジョニング中に複数のネットワーク インターフェイスを追加した場合は、それらをここで構成できます。 ネットワーク インターフェイスは IPv4 だけで構成するか、IPv4 と IPv6 の両方で構成することができます。 IPv6 だけで構成することはできません。
5. DNS サーバーは必須です。これは、デバイスがクラウド ストレージのサービス プロバイダーとやり取りしたり、デバイスがファイル サーバーとして構成されている場合にデバイスを名前により解決したりする際に使用されます。 **[DNS サーバー]** の **ネットワークの設定** ページで、以下の操作を行います。
   
   1. プライマリおよびセカンダリ DNS サーバーが自動的に構成されます。 静的 IP アドレスを構成することを選択した場合は、DNS サーバーを指定できます。 高可用性を確保するため、プライマリとセカンダリ DNS サーバーを構成することをお勧めします。
   2. **[Apply]** をクリックします。 これにより、ネットワークの設定が適用、検証されます。
6. **デバイスの設定** ページで以下の操作を実行します。
   
   1. デバイスに一意の **名前** を割り当てます。 この名前は 1 ～ 15 文字を指定でき、文字、数字、ハイフンを含めることができます。
   2. 作成するデバイスの **種類** として、 **[iSCSI サーバー]** アイコン ![iSCSI サーバー アイコン](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) をクリックします。 iSCSI サーバーでは、ブロック記憶域をプロビジョニングできます。
   3. このデバイスをドメインに参加させるかどうかを指定します。 デバイスが iSCSI サーバーの場合、ドメインへの参加はオプションです。 iSCSI サーバーをドメインに参加させない場合は、 **[適用]** をクリックし、設定が適用されるのを待機してから、次の手順に進みます。
      
       デバイスをドメインに参加させる場合は、 **[ドメイン名]** を入力し、 **[適用]** をクリックします。
      
      > [!NOTE]
      > iSCSI サーバーをドメインに参加させるには、仮想アレイが Microsoft Azure Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用されていないことを確認します。
      > 
      > 
   4. ダイアログ ボックスが表示されます。 ドメインの資格情報を指定された形式で入力します。 操作を完了するには、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). ドメインの資格情報が検証されます。 資格情報が間違っていると、エラー メッセージが表示されます。
      
       ![資格情報](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. **[Apply]** をクリックします。 これにより、デバイスの設定が適用、検証されます。
7. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。
   
    ![Web プロキシの構成](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    **[Web プロキシ設定]** ページで、以下のことを実行します。
   
   1. *http://\/host-IP アドレス* または *FQDN:ポート番号* の形式で、 **[Web プロキシ URL]** を指定します。 HTTPS URL はサポートされていないことに注意してください。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、 **[ユーザー名]** と **[パスワード]** も指定する必要があります。
   4. **[Apply]** をクリックします。 これにより、構成済みの Web プロキシ設定が検証され、適用されます。
8. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
   
    ![時刻の設定](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    **[時刻の設定]** ページで、次の操作を行います。
   
   1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **[タイム ゾーン]** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
   2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
   3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
   4. **[Apply]** をクリックします。 これにより、構成済みの時刻設定が検証され、適用されます。
9. デバイスのクラウドの設定を構成します。 この手順では、ローカル デバイスの構成を完了してから、StorSimple デバイス マネージャー サービスにそのデバイスを登録します。
   
   1. **サービス登録キー** (「**手順 2: サービス登録キーを取得する**」 ([StorSimple Virtual Array のデプロイ - ポータルの準備](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)) で取得したキー) を入力します。
   2. デバイスがこのサービスに登録する最初のデバイスでない場合は、 **サービス データ暗号化キー** を指定する必要があります。 このキーは、StorSimple デバイス マネージャー サービスに追加のデバイスを登録するときに、サービス登録キーと共に必要になります。 詳しくは、ローカル Web UI の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」をご覧ください。
   3. **[登録]** をクリックします。 これにより、デバイスが再起動します。 デバイスが正常に登録されるまでに、2 ～ 3 分間待機する必要がある場合があります。 デバイスが再起動したら、サインイン ページが表示されます。
      
      ![デバイスの登録](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Azure Portal に戻ります。
11. サービスの **[デバイス]** ブレードに移動します。 リソースが多数ある場合は、 **[すべてのリソース]** 、サービス名 (必要に応じて検索)、 **[デバイス]** の順にクリックします。
12. **[デバイス]** ブレードで、状態を参照して、デバイスが正常にサービスに接続されていることを確認します。 デバイスは **セットアップの準備が完了している** 状態になります。
    
    ![デバイスをデプロイする](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>手順 2:デバイスを iSCSI サーバーとして構成する

必要なデバイスのセットアップを完了するには、Azure Portal で次の手順を実行します。

#### <a name="to-configure-the-device-as-iscsi-server"></a>デバイスを iSCSI サーバーとして構成するには

1. StorSimple デバイス マネージャー サービスに移動し、 **[管理] > [デバイス]** の順に移動します。 **[デバイス]** ブレードで、先ほど作成したデバイスを選択します。 このデバイスは、**セットアップの準備が完了している** 状態として表示されます。
   
    ![デバイスを iSCSI サーバーとして構成](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. デバイスをクリックすると、デバイスのセットアップの準備が完了していることを示すバナー メッセージが表示されます。
   
    ![デバイスを iSCSI サーバーとして構成 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. デバイスのコマンド バーで **[構成]** をクリックします。 これにより **[構成]** ブレードが開きます。 **[構成]** ブレードで、次の操作を行います。
   
   * iSCSI サーバー名は自動的に設定されます。
   * クラウド ストレージ暗号化は必ず **[有効]** に設定します。 これによりデバイスからクラウドに送信されるデータが必ず暗号化されます。
   * 32 文字の暗号化キーを指定し、後で参照できるように、キー管理アプリケーションに記録します。
   * デバイスで使用するストレージ アカウントを選択します。 このサブスクリプションでは、既存のストレージ アカウントを選択したり、 **[追加]** をクリックして別のサブスクリプションのアカウントを選択したりできます。
     
     ![デバイスを iSCSI サーバーとして構成 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. **[構成]** をクリックして、iSCSI サーバーのセットアップを完了します。
   
    ![デバイスを iSCSI サーバーとして構成 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. iSCSI サーバーの作成が進行中であることが通知されます。 iSCSI サーバーが正常に作成されると、 **[デバイス]** ブレードが更新され、対応するデバイスの状態が "**オンライン**" になります。
   
    ![デバイスを iSCSI サーバーとして構成 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>手順 3:ボリュームを追加する

1. **[デバイス]** ブレードで、先ほど iSCSI サーバーとして構成したデバイスを選択します。 **[...]** をクリック (または、この行を右クリック) して、コンテキスト メニューから **[ボリュームの追加]** を選択します。 コマンド バーから **[+ ボリュームの追加]** をクリックすることもできます。 これにより **[ボリュームの追加]** ブレードが開きます。
   
    ![ボリュームを追加する](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. **[ボリュームの追加]** ブレードで、次の操作を行います。
   
   * **[ボリューム名]** フィールドに、ボリュームの一意の名前を入力します。 名前は 3 ～ 127 文字を含む文字列である必要があります。
   * **[種類]** ドロップダウン リストで、作成するボリュームの種類として **階層型** または **ローカル固定** のいずれかを指定します。 ローカルの保証、低待機時間、高パフォーマンスを必要とするワークロードでは、**ローカル固定** **ボリューム** を選択します。 それ以外のデータの場合は、**階層化** **ボリューム** を選択します。
   * **[容量]** フィールドで、ボリュームのサイズを指定します。 階層化ボリュームは 500 GB ～ 5 TB、ローカル固定ボリュームは 50 GB ～ 500 GB の範囲内で指定する必要があります。
     
     ローカル固定のボリュームはシック プロビジョニングされ、ボリューム上のプライマリ データがデバイスに残り、クラウドへの書き込みは行われません。
     
     一方、階層化ボリュームはシン プロビジョニングされます。 階層化ボリュームを作成した場合、領域の約 10% はローカル層にプロビジョニングされ、90% はクラウドにプロビジョニングされます。 たとえば、1 TB ボリュームをプロビジョニングした場合、データが階層化されるとき、100 GB はローカル領域に格納され、900 GB はクラウドに使用されます。 このため、これはデバイスのすべてのローカル領域が不足すると、階層化共有をプロビジョニングできないことを意味します (10% は利用できないため)。
     
     ![ボリュームを追加する 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * **[接続済みのホスト]** をクリックし、このボリュームに接続する iSCSI イニシエーターに対応するアクセス制御レコード (ACR) を選択して、 **[選択]** をクリックします。 <br><br> 
3. 新しい接続済みホストを追加するには、 **[新規追加]** をクリックし、ホストの名前とその iSCSI 修飾名 (IQN) を入力して、 **[追加]** をクリックします。 IQN がない場合は、「[付録 A: Windows Server ホストの IQN を取得する](#appendix-a-get-the-iqn-of-a-windows-server-host)」をご覧ください。
   
      ![ボリュームを追加する 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. ボリュームの構成が完了したら、 **[OK]** をクリックします。 指定した設定でボリュームが作成され、通知が表示されます。 既定では、監視とバックアップがボリュームに対して有効になります。
   
     ![ボリュームを追加する 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. ボリュームが正常に作成されたことを確認するには、 **[ボリューム]** ブレードに移動します。 ボリュームが一覧表示されます。
   
   ![ボリュームを追加する 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>手順 4:ボリュームをマウント、初期化、フォーマットする

次の手順に従って、Windows Server ホストに StorSimple ボリュームをマウント、初期化、フォーマットします。

#### <a name="to-mount-initialize-and-format-a-volume"></a>ボリュームをマウント、初期化、フォーマットするには

1. 適切なサーバーで **iSCSI イニシエーター** アプリケーションを開きます。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[探索]** タブで、 **[ポータルの探索]** をクリックします。
   
    ![[ポータルの探索]](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. **[ターゲット ポータルの探索]** ダイアログ ボックスで、iSCSI 対応ネットワーク インターフェイスの IP アドレスを入力し、 **[OK]** をクリックします。
   
    ![IP アドレス](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[ターゲット]** タブで、 **[検出されたターゲット]** を見つけます。 (各ボリュームが検出されたターゲットになります。)デバイスの状態が **[非アクティブ]** になっています。
   
    ![[検出されたターゲット]](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. ターゲット デバイスを選択し、 **[接続]** をクリックします。 デバイスが接続されると、状態が **[接続]** に変わります (Microsoft iSCSI イニシエーターの使用方法の詳細については、[Microsoft iSCSI イニシエーターのインストールと構成][1]に関する記事を参照してください)。
   
    ![ターゲット デバイスの選択](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Windows ホスト上で、Windows ロゴ キーを押しながら X キーを押し、 **[ファイル名を指定して実行]** をクリックします。
7. **[ファイル名を指定して実行]** ダイアログ ボックスに、「**Diskmgmt.msc**」と入力します。 **[OK]** をクリックすると、 **[ディスクの管理]** ダイアログ ボックスが表示されます。 右側のウィンドウに、ホスト上のボリュームが表示されます。
8. 次の図に示すように、マウントされているボリュームが **[ディスクの管理]** ウィンドウに表示されます。 検出されたボリュームを右クリックし (ディスク名をクリック)、 **[オンライン]** をクリックします。
   
    ![[ディスクの管理]](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. 右クリックし、 **[ディスクの初期化]** を選択します。
   
    ![ディスクの初期化 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. ダイアログ ボックスで、初期化するディスクを選択し、 **[OK]** をクリックします。
    
    ![ディスクの初期化 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. 新しいシンプル ボリューム ウィザードが起動します。 ディスクのサイズを選択し、 **[次へ]** をクリックします。
    
    ![新しいボリューム ウィザード 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. ドライブ文字をボリュームに割り当て、 **[次へ]** をクリックします。
    
    ![新しいボリューム ウィザード 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. ボリュームをフォーマットするパラメーターを入力します。 **Windows Server では、NTFS のみがサポートされています。** アロケーション ユニット サイズを 64 K に設定します。 ボリュームのラベルを指定します。 ベスト プラクティスとして、この名前は、StorSimple Virtual Array で指定したボリューム名と同じにすることをお勧めします。 **[次へ]** をクリックします。
    
    ![新しいボリューム ウィザード 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. ボリュームの値を確認し、 **[完了]** をクリックします。
    
    ![新しいボリューム ウィザード 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    ボリュームが **[オンライン]** on the **[ディスクの管理]** ページが表示されます。
    
    ![オンラインのボリューム](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>次のステップ

ローカル Web UI を使用して [StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>付録 A: Windows Server ホストの IQN を取得する

Windows Server 2012 を実行する Windows ホストの iSCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows ホストの IQN を取得するには

1. Windows ホストで、Microsoft iSCSI イニシエーターを起動します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[構成]** タブで、 **[イニシエーター名]** フィールドの文字列を選択してコピーします。
   
    ![[iSCSI イニシエーターのプロパティ]](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. この文字列を保存します。

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)