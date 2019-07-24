---
title: VMware で StorSimple Virtual Array をプロビジョニングする | Microsoft Docs
description: StorSimple Virtual Array のデプロイ シリーズの 2 番目のチュートリアルには、VMware での仮想デバイスのプロビジョニングが含まれます。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247845"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple Virtual Array をデプロイする - VMware でプロビジョニングする
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>概要
このチュートリアルでは、VMware ESXi 5.0、5.5、6.0、または 6.5 を実行するホスト システム上に StorSimple Virtual Array をプロビジョニングして接続する方法について説明します。 この記事は、Azure Portal および Microsoft Azure Government Cloud での StorSimple Virtual Array のデプロイに適用されます。

仮想デバイスをプロビジョニングして、そのデバイスに接続するには、管理者特権が必要です。 プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。

## <a name="provisioning-prerequisites"></a>プロビジョニングの前提条件
VMware ESXi 5.0、5.5、6.0、または 6.5 を実行しているホスト システムで仮想デバイスをプロビジョニングするための前提条件は次のとおりです。

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの場合
開始する前に次の点を確認します。

* [StorSimple Virtual Array のポータルの準備](storsimple-virtual-array-deploy1-portal-prep.md)に関するページの手順がすべて完了していること。
* Azure ポータルから VMware の仮想デバイスのイメージをダウンロードしていること。 詳細については、「**手順 3: 仮想デバイスのイメージをダウンロードする**」 ([StorSimple Virtual Array 用ポータルの準備ガイド](storsimple-virtual-array-deploy1-portal-prep.md)) を参照してください。

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 仮想デバイスの場合
仮想デバイスをデプロイする前に次の点を確認します。

* Hyper-V (2008 R2 以降) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。

  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 ファイル サーバーとして仮想アレイを構成する予定がある場合、8 GB でサポートできるのは 2 百万個未満のファイルです。 2 百万から 4 百万個のファイルをサポートするには 16 GB の RAM が必要です。
  * ネットワーク インターフェイス 1 つ。
  * システム データ用の 500 GB の仮想ディスク。

### <a name="for-the-network-in-datacenter"></a>データセンターのネットワークの場合
開始する前に次の点を確認します。

* StorSimple 仮想デバイスをデプロイするためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成していること。 

## <a name="step-by-step-provisioning"></a>プロビジョニングの手順
仮想デバイスをプロビジョニングし、そのデバイスに接続するには、次の手順を実行する必要があります。

1. ホスト システムに仮想デバイスの最小要件を満たすための十分なリソースがあることを確認します。
2. ハイパーバイザーで仮想デバイスをプロビジョニングします。
3. 仮想デバイスを起動し、IP アドレスを取得します。

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>手順 1:ホスト システムが仮想デバイスの最小要件を満たしていることを確認する
仮想デバイスを作成するには、次の要件が必要です。

* VMware ESXi Server 5.0、5.5、6.0、または 6.5 を実行しているホスト システムにアクセスできること。
* システムに、ESXi ホストを管理するための VMware vSphere client があること。

  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 ファイル サーバーとして仮想アレイを構成する予定がある場合、8 GB でサポートできるのは 2 百万個未満のファイルです。 2 百万から 4 百万個のファイルをサポートするには 16 GB の RAM が必要です。
  * トラフィックをインターネットにルーティングできるネットワークに接続している 1 つのネットワーク インターフェイス。 インターネットの最小帯域幅は、デバイスが最適に動作するように 5 Mbps にする必要があります。
  * データ用の 500 GB の仮想ディスク。

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>手順 2:ハイパーバイザーで仮想デバイスをプロビジョニングする
ハイパーバイザーで仮想デバイスをプロビジョニングするには、次の手順を実行します。

1. システム上にある仮想デバイスのイメージをコピーします。 この仮想イメージは、Azure Portal からダウンロードしました。

   1. 最新のイメージ ファイルがダウンロードされていることを確認します。 以前にイメージをダウンロードした場合は、必ず最新のイメージを保つため、再度ダウンロードします。 最新のイメージには 2 つのファイルがあります (1 つではありません)。
   2. このイメージは後で使用するため、コピー先はメモしておいてください。

2. vSphere client を使用して、ESXi サーバーにログインします。 仮想マシンを作成するには、管理者特権が必要です。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. vSphere client の左側のウィンドウのインベントリ セクションで、ESXi サーバーを選択します。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. VMDK を ESXi サーバーにアップロードします。 右側のウィンドウの **[Configuration]** タブに移動します。 **[Hardware]** で **[Storage]** を選択します。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. 右側のウィンドウの **[Datastores]** で VMDK をアップロードするデータストアを選択します。 データストアには、OS とデータ ディスク用の十分な空き領域が必要です。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. 右クリックして **[Browse Datastore]** を選択します。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. **[Datastore Browser]** ウィンドウが表示されます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. ツール バーで ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) アイコンをクリックして、新しいフォルダーを作成します。 フォルダー名を指定して、メモしておきます。 このフォルダー名は後で仮想マシンを作成するときに使用します (推奨されるベスト プラクティス)。 Click **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. **Datastore Browser** の左側のウィンドウに新しいフォルダーが表示されます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. アップロード アイコン ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) をクリックして、**[Upload File]** を選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. ダウンロードした VMDK ファイルを参照して、ポイントします。 2 つのファイルがあります。 アップロードするファイルを選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. **[開く]** をクリックします。 指定したデータストアへの VMDK ファイルのアップロードが開始されます。 ファイルのアップロードには数分かかる場合があります。
13. アップロードが完了すると、作成したフォルダーのデータストアにファイルが表示されます。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    ここで、2 つ目の VMDK ファイルを同じデータストアにアップロードします。
14. vSphere client のウィンドウに戻ります。 ESXi サーバーを選択して右クリックし、 **[新しい仮想マシン]** を選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **[New Virtual Machine]** ウィンドウが表示されます。 **[Configuration]** ページで **[Custom]** を選択します。 **[次へ]** をクリックします。
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. **[Name and Location]** ページで、仮想マシンの名前を指定します。 この名前は、手順 8 で指定したフォルダー名と一致する必要があります (推奨されるベスト プラクティス)。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. **[Storage]** ページで、VM のプロビジョニングに使用するデータストアを選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. **[仮想マシンのバージョン]** ページで **[仮想マシンバージョン: 8]** を選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. **[Guest Operating System]** ページで、**[Guest Operating System]** に **[Windows]** を選択します。 **[Version]** には、ドロップダウンリストから **[Microsoft Windows Server 2012 (64-bit)]** を選択します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. **[CPUs]** ページで、**[Number of virtual sockets]** と **[Number of cores per virtual socket]** を **[Total number of cores]** が 4 (またはそれ以上) になるように調整します。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. **[Memory]** ページで RAM を 8 GB (またはそれ以上) に指定します。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. **[Network]** ページでネットワーク インターフェイスの数を指定します。 ネットワーク インターフェイスの最小要件は 1 つです。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. **[SCSI Controller]** ページで、規定値の **[LSI Logic SAS]** をそのまま使用します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. **[Select a Disk]** ページで **[Use an existing virtual disk]** を選択します。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. **[Select Existing Disk]** ページの **[Disk File Path]** で **[Browse]** をクリックします。 **[Browse Datastores]** ダイアログ ボックスが開きます。 VMDK をアップロードした場所に移動します。 最初にアップロードした 2 つのファイルがマージされたため、データストアには 1 つのファイルのみが表示されます。 ファイルを選択し、 **[OK]** をクリックします。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. **[Advanced Options]** ページで既定値をそのまま使用して、**[Next]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. **[Ready to Complete]** ページで、新しい仮想マシンに関連するすべての設定を確認します。 **[Edit the virtual machine settings before completion]** をオンにします。 **[続行]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. **[Virtual Machines Properties]** ページの **[Hardware]** タブでデバイス ハードウェアを見つけます。 **[New Hard Disk]** を選択します。 **[追加]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. **[Add Hardware]** ウィンドウが表示されます。 **[Device Type]** ページの **[Choose the type of device you wish to add]** から **[Hard Disk]** を選択して、**[Next]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. **[Select a Disk]** ページで **[Create a new virtual disk]** を選択します。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. **[Create a Disk]** ページで **[Disk Size]** を 500 GB (またはそれ以上) に変更します。 500 GB は最小要件ですが、より容量の大きいディスクを常にプロビジョニングできます。 一度ディスクをプロビジョニングすると、展開も圧縮もできなくなることに注意してください。 プロビジョニングするディスク サイズについて詳しくは、[ベスト プラクティス](storsimple-ova-best-practices.md) ドキュメントの「サイズ変更」セクションをご確認ください。 **[Disk Provisioning]** で **[Thin Provision]** を選択します。 **[次へ]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. **[Advanced Options]** ページで既定値をそのまま使用します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. **[Ready to Complete]** ページでディスク オプションを確認します。 **[完了]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. [Virtual Machine Properties] ページに戻ります。 新しいハード ディスクが仮想マシンに追加されます。 **[Finish]** をクリックします。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. 仮想マシンを選択した状態で、右側のウィンドウで **[Summary]** タブに移動します。仮想マシンの設定を確認します。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

これで仮想マシンがプロビジョニングされました。 次はこのマシンを起動して IP アドレスを取得します。

> [!NOTE]
> (上でプロビジョニングしたように) 仮想アレイには VMware ツールをインストールしないことをお勧めします。 VMware ツールをインストールすると、サポートされていない構成になります。

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>手順 3:仮想デバイスを起動して IP アドレスを取得する
仮想デバイスを起動して接続するには、次の手順を実行します。

#### <a name="to-start-the-virtual-device"></a>仮想デバイスを起動するには
1. 仮想デバイスを起動します。 vSphere Configuration Manager の左側のウィンドウでデバイスを選択して右クリックし、コンテキスト メニューを表示します。 **[Power]**、**[Power on]** の順に選択します。 これで、仮想マシンの電源がオンになります。 vSphere client のページ下部の **[Recent Tasks]** ウィンドウで状態を確認できます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. 設定タスクの完了には数分かかります。 デバイスが起動したら **[Console]** タブに移動します。Ctrl + Alt + Del キーを押して、デバイスにログインします。 [Console] ウィンドウにカーソルをポイントして Ctrl + Alt + Insert キーを押すこともできます。 既定のユーザーは *StorSimpleAdmin* で、既定のパスワードは *Password1* です。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. セキュリティ上の理由から、デバイス管理者のパスワードは初回ログオン後に有効期限が切れます。 このため、パスワードを変更するよう求められます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. 8 文字以上を含むパスワードを入力します。 パスワードは、4 つの要件 (大文字、小文字、数字、および特殊文字) のうち 3 つを満たす必要があります。 確認のためにパスワードを再入力します。 パスワードが変更されたことが通知されます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. パスワードが正常に変更されると、仮想デバイスが再起動することがあります。 再起動が完了するのを待ちます。 デバイスの Windows PowerShell コンソールが、進行状況バーと共に表示される場合があります。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. 手順 6. ～ 8. は、非 DHCP 環境での起動時にのみ適用されます。 DHCP 環境の場合は、手順 6 ～ 8 をスキップし、手順 9 に進みます。 非 DHCP 環境でデバイスを起動した場合は、次の画面が表示されます。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   次に、ネットワークを構成します。
7. `Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効なネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 例を次に示します。

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. 初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。 デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。 この IP アドレスを使用して、仮想デバイスの Web UI に接続し、ローカル セットアップと登録を行います。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (省略可能) デバイスを Government Cloud にデプロイする場合にのみ、この手順を実行します。 デバイスで米国連邦情報処理標準 (FIPS) モードを有効にできるようになります。 FIPS 140 標準には、機密データ保護のために米国連邦政府のコンピューター システムに使用することが承認されている暗号化アルゴリズムが定義されています。

    1. FIPS モードを有効にするには、次のコマンドレットを実行します。

        `Enable-HcsFIPSMode`
    2. FIPS モードを有効にした後はデバイスを再起動して、暗号化の検証が有効になるようにします。

       > [!NOTE]
       > デバイスで FIPS モードを有効または無効にすることができます。 デバイスの FIPS モードと非 FIPS モードを切り替えることはサポートされていません。
       >
       >

デバイスが最小構成要件を満たしていない場合は、バナー テキストにエラーが表示されます (下記参照)。 最小要件を満たすための十分なリソースを確保するようにデバイスの構成を変更する必要があります。 その後、再起動し、デバイスに接続します。 「[手順 1: ホスト システムが仮想デバイスの最小要件を満たしていることを確認する](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)」の最小構成要件を参照してください。

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

ローカル Web UI を使って初期構成を行っている間に他のエラーが発生した場合は、次のワークフローを参照してください。

* 診断テストを実行して [Web UI のセットアップのトラブルシューティング](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)を行う。
* [ログ パッケージを生成してログ ファイルを表示する](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## <a name="next-steps"></a>次の手順
* [StorSimple Virtual Array をファイル サーバーとして設定する](storsimple-virtual-array-deploy3-fs-setup.md)
* [StorSimple Virtual Array を iSCSI サーバーとして設定する](storsimple-virtual-array-deploy3-iscsi-setup.md)
