---
title: "StorSimple Virtual Array の iSCSI サーバーのセットアップ | Microsoft Docs"
description: "初期セットアップを実行し、StorSimple iSCSI サーバーを登録して、デバイスのセットアップを完了する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 1a854bb3-3068-4db9-87b7-9f3692ab64e4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 715720d22b58ddd3d0e5042de151219e49549c5e


---
# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>StorSimple Virtual Array をデプロイする – 仮想デバイスを iSCSI サーバーとしてセットアップする
![iscsi セットアップ プロセス フロー](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>概要
このデプロイメント チュートリアルは、2016 年 3 月の一般公開 (GA) リリースを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたは StorSimple 仮想デバイスとも呼ばれます) に適用されます。 このチュートリアルでは、初期セットアップを実行し、StorSimple iSCSI サーバーを登録し、デバイス セットアップを完了した後、StorSimple 仮想デバイス iSCSI サーバーでボリュームを作成、マウント、初期化、フォーマットする方法を説明します。 この記事に記載されている StorSimple のセットアップ情報は、StorSimple Virtual Array にのみ適用されます。 

ここで説明する手順の完了には、約 30 分から 1 時間かかります。 この記事に記載されている情報は、StorSimple Virtual Array にのみ適用されます。

## <a name="setup-prerequisites"></a>セットアップの前提条件
StorSimple 仮想デバイスを構成およびセットアップする前に、以下のことを確認します。

* 「[StorSimple Virtual Array をデプロイする - Hyper-V で Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md)」または「[StorSimple Virtual Array をデプロイする - VMware で Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md)」の説明どおりに仮想デバイスをプロビジョニングして接続していること。
* StorSimple 仮想デバイスを管理するために作成した、StorSimple Manager サービスからのサービス登録キーがあること。 詳しくは、「 **StorSimple Virtual Array のデプロイ - ポータルの準備** 」の「 [手順 2: サービス登録キーを取得する](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)」を参照してください。
* 既存の StorSimple Manager サービスに登録する 2 番目または後続の仮想デバイスである場合は、サービス データ暗号化キーがあるはずです。 このキーは、最初のデバイスがこのサービスに正常に登録されたときに生成されています。 このキーを紛失した場合は、「 **Web UI を使用した StorSimple Virtual Array の管理** 」の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)」を参照してください。

## <a name="step-by-step-setup"></a>セットアップの手順
StorSimple 仮想デバイスをセットアップして構成するには、次の詳細な手順を実行します。

* [手順 1: ローカル Web UI のセットアップを完了し、デバイスを登録する](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [手順 2: 必要なデバイスのセットアップを完了する](#step-2-complete-the-required-device-setup)
* [手順 3: ボリュームを追加する](#step-3-add-a-volume)
* [手順 4: ボリュームをマウント、初期化、フォーマットする](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>手順 1: ローカル Web UI のセットアップを完了し、デバイスを登録する
#### <a name="to-complete-the-setup-and-register-the-device"></a>セットアップを完了し、デバイスを登録するには
1. ブラウザー ウィンドウを開き、以下を入力して Web UI に接続します。
   
    `https://<ip-address of network interface>`
   
    前の手順に記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを知らせるエラーが表示されます。 **[続行]**をクリックして、この Web ページに進みます。
   
    ![セキュリティ証明書エラー](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)
2. 仮想デバイスの Web UI に **StorSimpleAdmin**としてサインインします。 「[StorSimple Virtual Array をデプロイする - Hyper-V で仮想デバイスをプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md)」または「[StorSimple Virtual Array をデプロイする - VMware で仮想デバイスをプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md)」の「手順 3: 仮想デバイスを起動する」で変更したデバイス管理者のパスワードを入力します。
   
    ![サインイン ページ](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)
3. **[ホーム]** ページが表示されます。 このページは、仮想デバイスを StorSimple Manager サービスに構成、登録するのに必要なさまざまな設定を掲載しています。 **ネットワーク設定**、**Web プロキシの設定**、**時刻の設定**はオプションであることに注意してください。 必須の設定は、**デバイスの設定**と**クラウドの設定**のみです。
   
    ![ホーム ページ](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)
4. **[ネットワーク インターフェイス]** の **[ネットワーク設定]** ページで、DATA 0 が自動的に構成されます。 各ネットワーク インターフェイスは、既定で IP アドレスを自動的に取得するように設定されます (DHCP)。 そのため、IP アドレス、サブネット、およびゲートウェイは自動的に割り当てられます (IPv4 と IPv6 の両方に対して)。
   
    (ブロック記憶域をプロビジョニングするために) iSCSI サーバーとしてデバイスをデプロイする計画がある場合は、 **[IP アドレスを自動的に取得する]** オプションを無効にして、静的 IP アドレスを構成することをお勧めします。
   
    ![ネットワークの設定ページ](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)
   
    デバイスのプロビジョニング中に複数のネットワーク インターフェイスを追加した場合は、それらをここで構成できます。 ネットワーク インターフェイスは IPv4 だけで構成するか、IPv4 と IPv6 の両方で構成することができます。 IPv6 だけで構成することはできません。
5. DNS サーバーは必須です。これは、デバイスがクラウド ストレージのサービス プロバイダーとやり取りしたり、デバイスがファイル サーバーとして構成されている場合にデバイスを名前により解決したりする際に使用されます。 **[DNS サーバー]** の**ネットワークの設定**ページで、以下の操作を行います。
   
   1. プライマリおよびセカンダリ DNS サーバーが自動的に構成されます。 静的 IP アドレスを構成することを選択した場合は、DNS サーバーを指定できます。 高可用性を確保するため、プライマリとセカンダリ DNS サーバーを構成することをお勧めします。
   2. **[Apply]**をクリックします。 これにより、ネットワークの設定が適用、検証されます。
6. **デバイスの設定** ページで以下の操作を実行します。
   
   1. デバイスに一意の **名前** を割り当てます。 この名前は 1 ～ 15 文字を指定でき、文字、数字、ハイフンを含めることができます。
   2. 作成するデバイスの**種類**として、**[iSCSI サーバー]** アイコン ![iSCSI サーバー アイコン](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) をクリックします。 iSCSI サーバーでは、ブロック記憶域をプロビジョニングできます。
   3. このデバイスをドメインに参加させるかどうかを指定します。 デバイスが iSCSI サーバーの場合、ドメインへの参加はオプションです。 iSCSI サーバーをドメインに参加させない場合は、 **[適用]**をクリックし、設定が適用されるのを待機してから、次の手順に進みます。
      
       デバイスをドメインに参加させる場合は、 **[ドメイン名]** を入力し、**[適用]** をクリックします。
      
      > [!NOTE]
      > iSCSI サーバーをドメインに参加させるには、仮想アレイが Microsoft Azure Active Directory の独自の組織単位 (OU) にあり、グループ ポリシー オブジェクト (GPO) が適用されていないことを確認します。
      > 
      > 
   4. ダイアログ ボックスが表示されます。 ドメインの資格情報を指定された形式で入力します。 チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)」の説明どおりに仮想デバイスをプロビジョニングして接続していること。 ドメインの資格情報が検証されます。 資格情報が間違っていると、エラー メッセージが表示されます。
      
       ![資格情報](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
   5. **[Apply]**をクリックします。 これにより、デバイスの設定が適用、検証されます。
7. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。
   
    ![Web プロキシの構成](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)
   
    **[Web プロキシ設定]** ページで、以下のことを実行します。
   
   1. *http://*host-IP アドレスまたは FDQN*:ポート番号*の形式で、**[Web プロキシ URL]** を指定します。 HTTPS URL はサポートされていないことに注意してください。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、**[ユーザー名]** と **[パスワード]** も指定する必要があります。
   4. **[Apply]**をクリックします。 これにより、構成済みの Web プロキシ設定が検証され、適用されます。
8. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
   
    ![時刻の設定](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)
   
    **[時刻の設定]** ページで、次の操作を行います。
   
   1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **[タイム ゾーン]** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
   2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
   3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
   4. **[Apply]**をクリックします。 これにより、構成済みの時刻設定が検証され、適用されます。
9. デバイスのクラウドの設定を構成します。 この手順では、ローカル デバイスの構成を完了してから、StorSimple Manager サービスにデバイスを登録します。
   
   1. 「[StorSimple Virtual Array のデプロイ - ポータルの準備](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)」の「**手順 2. サービス登録キーを取得する**」で取得した**サービス登録キー**を入力します。
   2. デバイスがこのサービスに登録する最初のデバイスでない場合は、 **サービス データ暗号化キー**を指定する必要があります。 このキーは、StorSimple Manager サービスに追加のデバイスを登録する際にサービス登録キーと共に必要になります。 詳しくは、ローカル Web UI の「 [サービス データ暗号化キーの取得](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 」をご覧ください。
   3. **[登録]**をクリックします。 これにより、デバイスが再起動します。 デバイスが正常に登録されるまでに、2 ～ 3 分間待機する必要がある場合があります。 デバイスが再起動したら、サインイン ページが表示されます。
      
      ![デバイスの登録](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)
10. Azure クラシック ポータルに戻ります。 **[デバイス]** ページで、状態を参照して、デバイスが正常にサービスに接続されていることを確認します。 デバイスの状態は **"アクティブ"**と表示されます。
    
    ![[デバイス] ページ](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>手順 2: 必要なデバイスのセットアップを完了する
StorSimple デバイスのデバイス構成を完了するには、次の手順を実行する必要があります。

* このデバイスに関連付けるストレージ アカウントを選択します。
* クラウドに送信されるデータの暗号化の設定を選択します。

必要なデバイスのセットアップを完了するには、Azure クラシック ポータルで次の手順を実行します。

#### <a name="to-complete-the-minimum-device-setup"></a>デバイスの最小セットアップを完了するには
1. **[デバイス]** ページで、先ほど作成したデバイスを選択します。 このデバイスは **"アクティブ"**として表示されます。 デバイス名の横の矢印をクリックし、 **[クイック スタート]**をクリックします。
   
    ![[デバイス] ページ](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)
2. **[デバイスのセットアップの完了]** をクリックして、デバイスの構成ウィザードを開始します。
   
    ![デバイスの構成ウィザード](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)
3. デバイスの構成ウィザードの **[基本設定]** ページで、次の手順を実行します。
   
   1. このデバイスで使用するストレージ アカウントを指定します。 このサブスクリプションでは、ドロップダウン リストから既存のストレージ アカウントを選択したり、 **[追加]** を指定して、別のサブスクリプションのアカウントを選択したりできます。
   2. クラウドに送信される、すべての保存データの暗号化設定を定義します。 (StorSimple では、AES-256 暗号化を使用します。)データを暗号化するには、**[クラウド ストレージの暗号化の有効化]** チェック ボックスをオンにします。 32 文字を含むクラウド ストレージの暗号化キーを入力します。 確認のため、キーを再入力します。
   3. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)」の説明どおりに仮想デバイスをプロビジョニングして接続していること。
      
      ![[基本設定]](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)
      
      これで設定が更新されました。 設定が正常に更新されると、デバイスのセットアップの完了ボタンは使用できなくなります。 デバイスの **[クイック スタート]** ページが再び表示されます。                                                        

> [!NOTE]
> **[構成]** ページにアクセスすれば、その他すべてのデバイス設定をいつでも変更できます。
> 
> 

## <a name="step-3-add-a-volume"></a>手順 3: ボリュームを追加する
ボリュームを作成するには、Azure クラシック ポータルで次の手順を実行します。

#### <a name="to-create-a-volume"></a>ボリュームを作成するには
1. デバイスの **[クイック スタート]** ページで、**[ボリュームの追加]** をクリックします。 ボリュームの追加ウィザードが開始されます。
2. ボリュームの追加ウィザードの **[基本設定]**で、次の操作を行います。
   
   1. ボリュームの一意の名前を指定します。 名前は 3 ～ 127 文字を含む文字列である必要があります。
   2. ボリュームの説明を入力します。 説明は、ボリュームの所有者の特定に役立ちます。
   3. ボリュームの使用法の種類を選択します。 使用法の種類は、**[階層化ボリューム]** または **[ローカル固定ボリューム]** にすることができます  (**[階層化ボリューム]** が既定値です)。ローカルの保証、低待機時間、高パフォーマンスを必要とするワークロードでは、**[ローカル固定****ボリューム]** を選択します。 それ以外のデータの場合は、**[階層化****ボリューム]**を選択します。
      
       ローカル固定のボリュームはシック プロビジョニングされ、ボリューム上のプライマリ データがデバイスに残り、クラウドへの書き込みは行われません。 ローカル固定のボリュームを作成する場合、デバイスは、要求されたサイズのボリュームをプロビジョニングするため、ローカル階層で使用可能な領域を確認します。 ローカル固定のボリュームの作成によって、デバイスからクラウドに既存のデータが書き込まれる必要がある場合があるため、ボリュームの作成にかかる時間が長くなる可能性があります。 合計時間は、プロビジョニングされたボリューム、利用可能なネットワーク帯域幅、およびデバイスのデータのサイズによって異なります。
      
       一方、階層化ボリュームはシン プロビジョニングされ、非常に簡単に作成できます。 階層化ボリュームを作成した場合、領域の約 10% はローカル層にプロビジョニングされ、90% はクラウドにプロビジョニングされます。 たとえば、1 TB ボリュームをプロビジョニングした場合、データが階層化されるとき、100 GB はローカル領域に格納され、900 GB はクラウドに使用されます。 このため、これはデバイスのすべてのローカル領域が不足すると、階層化共有をプロビジョニングできないことを意味します (10% は利用できないため)。
   4. ボリュームのプロビジョニング容量を指定します。 利用できる容量より小さい容量を指定する必要があります。 階層化ボリュームを作成する場合、サイズは 500 GB ～ 5 TB にする必要があります。 ローカル固定ボリュームの場合、50 ～ 500 GB のボリューム サイズを指定します。 利用できる容量を、ボリュームのプロビジョニングのガイドとして使用します。 利用可能なローカル容量が 0 GB の場合、ローカル固定ボリュームまたは階層化ボリュームをプロビジョニングすることはできません。
      
       ![[基本設定]](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)
   5. 矢印アイコン  ![矢印アイコン](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) をクリックして、次のページに進みます。
3. **[追加設定]** ページで、新しい Access Control レコード (ACR) を追加します。
   
   1. ACR の **[名前]** を入力します。
   2. **[ISCSI イニシエーターの名前]**に Windows ホストの iSCSI 修飾名 (IQN) を指定します。 IQN がない場合は、「 [付録 A: Windows Server ホストの IQN を取得する](#appendix-a-get-the-iqn-of-a-windows-server-host)」をご覧ください。
   3. **[このボリュームの既定のバックアップの有効化]** チェック ボックスをオンにして、既定のバックアップを有効にすることをお勧めします。 既定のバックアップでは、毎日 22:30 (デバイスの時刻) に実行し、このボリュームのクラウド スナップショットを作成するというポリシーが作成されます。
      
       ![[追加設定]](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)
   4. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)」の説明どおりに仮想デバイスをプロビジョニングして接続していること。 ボリューム作成ジョブが開始されます。 次のような進行状況についてのメッセージが表示されます。
      
       ![進行状況についてのメッセージ](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)
      
       指定された設定でボリュームが作成されます。 既定では、監視とバックアップがボリュームに対して有効になります。
   5. ボリュームが正常に作成されたことを確認するには、 **[ボリューム]** ページに移動します。 ボリュームが一覧表示されます。
      
       ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>手順 4: ボリュームをマウント、初期化、フォーマットする
次の手順に従って、Windows Server ホストに StorSimple ボリュームをマウント、初期化、フォーマットします。

#### <a name="to-mount-initialize-and-format-a-volume"></a>ボリュームをマウント、初期化、フォーマットするには
1. Microsoft iSCSI イニシエーターを開始します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[探索]** タブで、**[ポータルの探索]** をクリックします。
   
    ![[ポータルの探索]](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)
3. **[ターゲット ポータルの探索]** ダイアログ ボックスで、iSCSI 対応ネットワーク インターフェイスの IP アドレスを入力し、**[OK]** をクリックします。
   
    ![IP アドレス](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)
4. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[ターゲット]** タブで、**[検出されたターゲット]** を見つけます。 (各ボリュームが検出されたターゲットになります。)デバイスの状態が **[非アクティブ]**になっています。
   
    ![[検出されたターゲット]](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)
5. ターゲット デバイスを選択し、**[接続]** をクリックします。 デバイスが接続されると、状態が **[接続]** に変わります  (Microsoft iSCSI イニシエーターの使用方法の詳細については、「[Installing and Configuring Microsoft iSCSI Initiator (Microsoft iSCSI イニシエーターのインストールと構成)][1]」をご覧ください)。
   
    ![ターゲット デバイスの選択](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)
6. Windows ホスト上で、Windows ロゴ キーを押しながら X キーを押し、 **[ファイル名を指定して実行]**をクリックします。
7. **[ファイル名を指定して実行]** ダイアログ ボックスに、「**Diskmgmt.msc**」と入力します。 **[OK]** をクリックすると、**[ディスクの管理]** ダイアログ ボックスが表示されます。 右側のウィンドウに、ホスト上のボリュームが表示されます。
8. 次の図に示すように、マウントされているボリュームが **[ディスクの管理]** ウィンドウに表示されます。 検出されたボリュームを右クリックし (ディスク名をクリック)、 **[オンライン]**をクリックします。
   
    ![[ディスクの管理]](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)
9. 右クリックし、 **[ディスクの初期化]**を選択します。
   
    ![ディスクの初期化 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)
10. ダイアログ ボックスで、初期化するディスクを選択し、 **[OK]**をクリックします。
    
    ![ディスクの初期化 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)
11. 新しいシンプル ボリューム ウィザードが起動します。 ディスクのサイズを選択し、 **[次へ]**をクリックします。
    
    ![新しいボリューム ウィザード 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)
12. ドライブ文字をボリュームに割り当て、 **[次へ]**をクリックします。
    
    ![新しいボリューム ウィザード 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)
13. ボリュームをフォーマットするパラメーターを入力します。 **Windows Server では、NTFS のみがサポートされています。** AUS を 64K に設定します。 ボリュームのラベルを指定します。 ベスト プラクティスとして、この名前を StorSimple 仮想デバイスに指定したボリューム名と同じにすることをお勧めします。 **[次へ]**をクリックします。
    
    ![新しいボリューム ウィザード 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)
14. ボリュームの値を確認し、 **[完了]**をクリックします。
    
    ![新しいボリューム ウィザード 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)
    
    ボリュームが **[オンライン]** on the **[ディスクの管理]** ページが表示されます。
    
    ![オンラインのボリューム](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>次のステップ
ローカル Web UI を使用して、 [StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>付録 A: Windows Server ホストの IQN を取得する
Windows Server 2012 を実行する Windows ホストの iSCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows ホストの IQN を取得するには
1. Windows ホストで、Microsoft iSCSI イニシエーターを起動します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[構成]** タブで、**[イニシエーター名]** フィールドの文字列を選択してコピーします。
   
    ![iSCSI イニシエーターのプロパティ](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)
3. この文字列を保存します。

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx






<!--HONumber=Dec16_HO2-->


