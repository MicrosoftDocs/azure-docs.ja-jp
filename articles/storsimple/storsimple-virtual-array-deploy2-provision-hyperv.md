---
title: Hyper-V で StorSimple Virtual Array をプロビジョニングする | Microsoft Docs
description: StorSimple Virtual Array のデプロイのこの 2 番目のチュートリアルでは、Hyper-V で仮想アレイをプロビジョニングします。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87070596"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple Virtual Array をデプロイする - Hyper-V でプロビジョニングする
![仮想アレイをデプロイするために必要な手順を示す図。 2 つ目の手順の最初の部分には、"Provision on Hyper-V" (Hyper-V でのプロビジョニング) というラベルが付けられ、強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>概要

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

このチュートリアルでは、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行しているホスト システムで、StorSimple Virtual Array をプロビジョニングする方法について説明します。 この記事は、Azure Portal および Microsoft Azure Government Cloud での StorSimple Virtual Array のデプロイに適用されます。

仮想アレイをプロビジョニングして構成するには、管理者特権が必要です。 プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。

## <a name="provisioning-prerequisites"></a>プロビジョニングの前提条件
ここでは、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行しているホスト システムで、仮想アレイをプロビジョニングする際の前提条件について説明します。

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの場合
開始する前に次の点を確認します。

* [StorSimple Virtual Array のポータルの準備](storsimple-virtual-array-deploy1-portal-prep.md)に関するページの手順がすべて完了していること。
* Azure Portal から Hyper-V の仮想アレイのイメージをダウンロードしていること。 詳細については、「**手順 3: 仮想アレイのイメージをダウンロードする**」([StorSimple Virtual Array 用ポータルの準備ガイド](storsimple-virtual-array-deploy1-portal-prep.md)) を参照してください。

  > [!IMPORTANT]
  > StorSimple Virtual Array で実行されているソフトウェアは、StorSimple デバイス マネージャー サービスでのみ使用できます。
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>StorSimple Virtual Array の場合
仮想アレイをデプロイする前に次の点を確認します。

* Windows Server 2008 R2 以降の Hyper-V を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想アレイのプロビジョニング専用に使用できること。

  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 ファイル サーバーとして仮想アレイを構成する予定がある場合、8 GB でサポートできるのは 2 百万個未満のファイルです。 2 百万から 4 百万個のファイルをサポートするには 16 GB の RAM が必要です。
  * ネットワーク インターフェイス 1 つ。
  * データ用の 500 GB の仮想ディスク。

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合
作業を始める前に、StorSimple Virtual Array をデプロイして、データセンター ネットワークを適切に構成するためのネットワーク要件を確認します。 詳細については、 [StorSimple Virtual Array のネットワーク要件](storsimple-ova-system-requirements.md#networking-requirements)に関するセクションをご覧ください。

## <a name="step-by-step-provisioning"></a>プロビジョニングの手順
仮想アレイをプロビジョニングし、その仮想アレイに接続するには、次の手順を実行する必要があります。

1. ホスト システムに仮想アレイの最小要件を満たすためのリソースが十分にあることを確認します。
2. ハイパーバイザーで仮想アレイをプロビジョニングします。
3. 仮想アレイを起動し、IP アドレスを取得します。

これらの各手順を以下のセクションで説明します。

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>手順 1:ホスト システムが仮想アレイの最小要件を満たしていることを確認する
仮想アレイを作成するには、次が必要です。

* Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 にインストールされた Hyper-V ロール。
* ホストに接続されている Microsoft Windows クライアント上の Microsoft Hyper-V マネージャー。

仮想アレイを作成している基盤となるハードウェア (ホスト システム) で、次のリソースを仮想アレイ専用に使用できることを確認してください。

* 最小で 4 コア。
* 少なくとも 8 GB の RAM。 ファイル サーバーとして仮想アレイを構成する予定がある場合、8 GB でサポートできるのは 2 百万個未満のファイルです。 2 百万から 4 百万個のファイルをサポートするには 16 GB の RAM が必要です。
* ネットワーク インターフェイス 1 つ。
* システム データ用の 500 GB の仮想ディスク。

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>手順 2:ハイパーバイザーで仮想アレイをプロビジョニングする
ハイパーバイザーでデバイスをプロビジョニングするには、次の手順を実行します。

#### <a name="to-provision-a-virtual-array"></a>仮想アレイをプロビジョニングするには
1. Windows Server ホストで、仮想アレイのイメージをローカル ドライブにコピーします。 このイメージ (VHD または VHDX) は、Azure Portal からダウンロードしました。 このイメージは後で使用するため、コピー先はメモしておいてください。
2. **サーバー マネージャー** を開きます。 右上隅の **[ツール]** をクリックし、 **[Hyper-V マネージャー]** を選択します。

   ![Hyper-V マネージャーの項目が強調表示された、展開された [ツール] メニューを示すサーバー マネージャーのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Windows Server 2008 R2 を実行している場合は、Hyper-V マネージャーを開きます。 サーバー マネージャーで、 **[ロール] > [Hyper-V] > [Hyper-V マネージャー]** をクリックします。
3. **Hyper-V マネージャー** のスコープ ウィンドウで、システム ノードを右クリックしてコンテキスト メニューを開き、 **[新規]**  >  **[仮想マシン]** の順にクリックします。

   ![[新規] と [仮想マシン] の項目が強調表示された、システム ノードのショートカット メニューを示す Hyper-V マネージャーのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. 仮想マシンの新規作成ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。
5. **[名前と場所を指定]** ページで、仮想アレイの **名前** を入力します。 **[次へ]** をクリックします。

   ![[名前] ボックスに入力され、[次へ] ボタンが強調表示された [名前と場所の指定] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. **[世代の指定]** ページで、デバイスのイメージの種類を選択し、 **[次へ]** をクリックします。 Windows Server 2008 R2 を使用している場合、このページは表示されません。

   * Windows Server 2012 以降の .vhdx イメージをダウンロードした場合は、 **[第 2 世代]** を選択します。
   * Windows Server 2008 R2 以降の .vhd イメージをダウンロードした場合は、 **[第 1 世代]** を選択します。

   ![[第 2 世代] オプションが選択され、[次へ] ボタンが強調表示されている [世代の指定] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. **[メモリの割り当て]** ページで、**8192 MB** 以上の **起動メモリ** を指定します。動的メモリは有効にしないでください。 **[次へ]** をクリックします。

   ![[起動メモリ] ボックスに 8192 の値が表示された [メモリの割り当て] ページのスクリーンショット。 [次へ] ボタンが強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. **[ネットワークの構成]** ページで、インターネットに接続されている仮想スイッチを指定し、 **[次へ]** をクリックします。

   ![[接続] ボックスで選択した Hyper V 仮想スイッチ項目が表示され、[次へ] ボタンが強調表示された [ネットワークの構成] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. **[仮想ハード ディスクの接続]** ページで、 **[既存の仮想ハード ディスクを使用する]** を選択し、仮想アレイのイメージ (.vhdx または .vhd) の場所を指定して、 **[次へ]** をクリックします。

   ![[既存の仮想ハード ディスクを使用する] が選択され、場所が入力され、[次へ] ボタンが強調表示された [仮想ハード ディスクの接続] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. **[概要]** を確認し、 **[完了]** をクリックして仮想マシンを作成します。

    ![仮想マシンの説明が表示された [仮想マシンの新規作成ウィザードの完了] ページのスクリーンショット。 [完了] ボタンが強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 最小要件を満たすには、4 コアが必要です。 4 つの仮想プロセッサを追加するには、 **[Hyper-V マネージャー]** ウィンドウでホスト システムを選択します。 右側のウィンドウの **[仮想マシン]** の一覧で、先ほど作成した仮想マシンを見つけます。 マシン名を選択して右クリックし、 **[設定]** を選択します。

    ![[Hyper-V マネージャー] ウィンドウのスクリーンショット。 リストから 1 台のマシンが選択され、そのマシンのショートカット メニューで [設定] 項目が強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. **[設定]** ページの左側のウィンドウで **[プロセッサ]** をクリックします。 右側のウィンドウで、 **[仮想プロセッサの数]** を 4 (またはそれ以上) に設定します。 **[Apply]** をクリックします。

    ![[プロセッサ] 項目が強調表示され、[仮想プロセッサの数] が 4 に設定され、[適用] ボタンが強調表示された [設定] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. 最小要件を満たすには、500 GB の仮想データ ディスクを追加する必要もあります。 **[設定]** ページで次の操作を行います。

    1. 左側のウィンドウで **[SCSI コントローラー]** を選択します。
    2. 右側のウィンドウで **[ハード ドライブ]** を選択し、 **[追加]** をクリックします。

    ![[SCSI コントローラー] 項目が選択され、[ハード ドライブ] 項目が選択され、[追加] ボタンが強調表示された [設定] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. **[ハード ドライブ]** ページで **[仮想ハード ディスク]** を選択し、 **[新規]** をクリックします。 **仮想ハード ディスクの新規作成ウィザード** が開始されます。

    ![[ハード ドライブ] 項目が選択され、[仮想ハード ディスク] オプションが選択され、[新規] ボタンが強調表示された [設定] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. 仮想ハード ディスクの新規作成ウィザードの **[開始する前に]** ページで、 **[次へ]** をクリックします。
16. **[ディスク フォーマットの選択]** ページで、既定のオプションの **[VHDX]** 形式をそのまま使用します。 **[次へ]** をクリックします。 Windows Server 2008 R2 を実行している場合、この画面は表示されません。

    ![[VHDX] オプションが選択され、[次へ] ボタンが強調表示された [ディスク フォーマットの選択] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. **[ディスクの種類の選択]** ページで、仮想ハード ディスクの種類を **[容量可変]** に設定します (推奨)。 **固定サイズ** のディスクでも動作しますが、待機時間が長くなる可能性があります。 **[差分]** は使用しないことをお勧めします。 **[次へ]** をクリックします。 Windows Server 2012 R2 および Windows Server 2012 では、 **[容量可変]** オプションが既定で設定されていますが、Windows Server 2008 R2 では **[固定サイズ]** が既定の設定です。

    ![[容量可変] オプションが選択され、[次へ] ボタンが強調表示された [ディスクの種類の選択] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. **[名前と場所の指定]** ページで、データ ディスクの **名前** と **場所** を入力します (場所は参照することもできます)。 **[次へ]** をクリックします。

    ![[名前] および [場所] ボックスの値を示す [名前と場所の指定] ページのスクリーンショット。 [次へ] ボタンが強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. **[ディスクの構成]** ページで、 **[新しい空の仮想ハード ディスクを作成する]** を選択し、サイズを **500 GB** (またはそれ以上) に指定します。 500 GB は最小要件ですが、より容量の大きいディスクを常にプロビジョニングできます。 一度ディスクをプロビジョニングすると、展開も圧縮もできなくなることに注意してください。 プロビジョニングするディスク サイズについて詳しくは、[ベスト プラクティス](storsimple-ova-best-practices.md) ドキュメントの「サイズ変更」セクションをご確認ください。 **[次へ]** をクリックします。

    ![[新しい空の仮想ハード ディスクを作成する] が選択され、サイズが 500 に設定され、[次へ] ボタンが強調表示された [ディスクの構成] ページのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. **[概要]** ページで仮想データ ディスクの詳細を確認し、問題がなければ **[完了]** をクリックしてディスクを作成します。 ウィザードが終了し、仮想ハード ディスクがコンピューターに追加されます。

    ![仮想ハード ディスクの説明が表示された [仮想ハード ディスクの新規作成ウィザードの完了] ページのスクリーンショット。 [完了] ボタンが強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. **[設定]** ページに戻ります。 **[OK]** をクリックして **[設定]** ページを閉じ、[Hyper-V マネージャー] ウィンドウに戻ります。

    ![[設定] ページのスクリーンショット。 [OK] ボタンが強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>手順 3:仮想アレイを起動し、IP アドレスを取得する
仮想アレイを起動して、そのアレイに接続するには、次の手順を実行します。

#### <a name="to-start-the-virtual-array"></a>仮想アレイを起動するには
1. 仮想アレイを起動します。

   ![[Hyper-V マネージャー] ウィンドウのスクリーンショット。 リストで新しいアレイが選択され、そのアレイのショートカット メニューで [起動] 項目が強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. デバイスが起動したら、デバイスを選択して右クリックし、 **[接続]** を選択します。

   ![[Hyper-V マネージャー] ウィンドウのスクリーンショット。 新しいアレイが選択され、その状態が "実行中" になり、ショートカット メニューで [接続] 項目が強調表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. デバイスの準備ができるまでに 5 ～ 10 分かかる場合があります。 進行状況を示すステータス メッセージがコンソールに表示されます。 デバイスの準備ができたら **[アクション]** に移動します。 `Ctrl + Alt + Delete` キーを押して、仮想アレイにログインします。 既定のユーザーは *StorSimpleAdmin* で、既定のパスワードは *Password1* です。

   ![サインイン ボックスに不確定の文字が表示されている vSphere クライアントの [コンソール] タブのスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. セキュリティ上の理由から、デバイス管理者のパスワードは初回ログオン後に有効期限が切れます。 このため、パスワードを変更するよう求められます。

   ![vSphere クライアントの [コンソール] タブのスクリーンショット。ページ上のテキストは、パスワードを変更する必要があることを示しています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   8 文字以上を含むパスワードを入力します。 パスワードは、4 つの要件 (大文字、小文字、数字、および特殊文字) のうち少なくとも 3 つを満たす必要があります。 確認のためにパスワードを再入力します。 パスワードが変更されたことが通知されます。

   ![vSphere クライアントの [コンソール] タブのスクリーンショット。ページ上のテキストは、パスワードが変更されたことを示しています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. パスワードが正常に変更されると、仮想アレイが再起動することがあります。 デバイスが起動するのを待ちます。

   ![メインの StorSimpleAdmin ページのスクリーンショット。 ページ上のテキストは、システム イベント通知サービスを待つようにユーザーに求めています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    デバイスの Windows PowerShell コンソールと進行状況バーが表示されます。

   ![進行状況バーが表示されたコンソール ウィンドウを示すスクリーンショット。 ウィンドウ内のテキストは、初期設定が進行中であり、ユーザーに待機するように求めています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. 手順 6. ～ 8. は、非 DHCP 環境での起動時にのみ適用されます。 DHCP 環境の場合は、手順 6 ～ 8 をスキップし、手順 9 に進みます。 非 DHCP 環境でデバイスを起動した場合は、次の画面が表示されます。

   ![デバイスを説明するテキストを含むコンソール ウィンドウを示すスクリーンショット。 コマンド プロンプトに "コントローラー" と表示され、入力できる状態です。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    次に、ネットワークを構成します。
7. `Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効になっているネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

   ![Get-HcsIpAddress コマンドの出力を含むコンソール ウィンドウを示すスクリーンショット。 "Ethernet" がデバイスの名前として表示されています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 次の例を参照してください。

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Get-Help Set-HcsIpAddress コマンドの出力と Set-HcsIpAddress コマンドの正しい使用法が表示されたコンソール ウィンドウを示すスクリーンショット。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. 初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。 デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。 この IP アドレスを使用して、仮想アレイの Web UI に接続し、ローカル セットアップと登録を行います。

   ![デバイス バナー テキストのあるコンソール ウィンドウを示すスクリーンショット。 このテキストには、デバイスの IP アドレスと URL が含まれています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (省略可能) デバイスを Government Cloud にデプロイする場合にのみ、この手順を実行します。 デバイスで米国連邦情報処理標準 (FIPS) モードを有効にできるようになります。 FIPS 140 標準には、機密データ保護のために米国連邦政府のコンピューター システムに使用することが承認されている暗号化アルゴリズムが定義されています。

    1. FIPS モードを有効にするには、次のコマンドレットを実行します。

        `Enable-HcsFIPSMode`
    2. FIPS モードを有効にした後はデバイスを再起動して、暗号化の検証が有効になるようにします。

       > [!NOTE]
       > デバイスで FIPS モードを有効または無効にすることができます。 デバイスの FIPS モードと非 FIPS モードを切り替えることはサポートされていません。
       >
       >

デバイスが最小構成要件を満たしていない場合は、バナー テキストに次のエラーが表示されます (下記参照)。 最小要件を満たすだけのリソースにコンピューターが対応できるように、デバイスの構成を変更します。 その後、再起動し、デバイスに接続します。 最小構成要件については、「手順 1:ホスト システムが仮想アレイの最小要件を満たしていることを確認する」を参照してください。

![デバイス バナー テキストのあるコンソール ウィンドウを示すスクリーンショット。 そのテキストには、問題をトラブルシューティングするための URL が記載されたエラー メッセージが含まれています。](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

ローカル Web UI を使って初期構成を行っている間に他のエラーが発生した場合は、次のワークフローを参照してください。

* 診断テストを実行して [Web UI のセットアップのトラブルシューティング](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)を行う。
* [ログ パッケージを生成してログ ファイルを表示する](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## <a name="next-steps"></a>次のステップ
* [StorSimple Virtual Array をファイル サーバーとして設定する](storsimple-virtual-array-deploy3-fs-setup.md)
* [StorSimple Virtual Array を iSCSI サーバーとして設定する](storsimple-virtual-array-deploy3-iscsi-setup.md)
