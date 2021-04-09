---
title: VMware での Azure Data Box Gateway のプロビジョニングに関するチュートリアル | Microsoft Docs
description: Azure Data Box Gateway をデプロイするための 2 番目のチュートリアルには、VMware での仮想デバイスのプロビジョニングが含まれます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 948005942be193c8b15c363282aa06c006eb244e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727619"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>チュートリアル:VMware で Azure Data Box Gateway をプロビジョニングする

## <a name="overview"></a>概要

このチュートリアルでは、VMware ESXi 6.0、6.5、または 6.7 を実行しているホスト システム上に Data Box Gateway をプロビジョニングする方法について説明します。 

仮想デバイスをプロビジョニングして、そのデバイスに接続するには、管理者特権が必要です。 プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ホストがデバイスの最小要件を満たしていることを確認する
> * VMware で仮想デバイスをプロビジョニングする
> * 仮想デバイスを起動し、IP アドレスを取得する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="prerequisites"></a>前提条件

VMware ESXi 6.0、6.5、または 6.7 を実行しているホスト システムで仮想デバイスをプロビジョニングするための前提条件は次のとおりです。

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway リソースの場合

開始する前に次の点を確認します。

* [Data Box Gateway 用のポータルの準備](data-box-gateway-deploy-prep.md)に関するページの手順がすべて完了していること。
* [Data Box Gateway 用のポータルの準備](data-box-gateway-deploy-prep.md)に関するページの説明に従って、VMware 用の仮想デバイス イメージを Azure portal からダウンロードしていること。

  > [!IMPORTANT]
  > Data Box Gateway 上で実行されるソフトウェアは、Data Box Gateway リソースに対してのみ使用できます。

### <a name="for-the-data-box-gateway-virtual-device"></a>Data Box Gateway 仮想デバイスの場合

仮想デバイスをデプロイする前に次の点を確認します。

* VMware (ESXi 6.0、6.5、または 6.7) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。

  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 RAM を 16 GB 以上にすることを強くお勧めします。
  * ネットワーク インターフェイス 1 つ。
  * 250 GB の OS ディスク。
  * システム データ用の 2 TB の仮想ディスク。

### <a name="for-the-network-in-datacenter"></a>データセンターのネットワークの場合

作業を開始する前に、次のことを行います。

- Data Box Gateway をデプロイするためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成します。 詳細については、[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md#networking-port-requirements)に関する記事を参照してください。
- インターネットの最小帯域幅が、デバイスの最適動作を可能にする 20 Mbps であることを確認します。

## <a name="check-the-host-system"></a>ホスト システムをチェックする

仮想デバイスを作成するには、以下が必要です。

* VMware ESXi Server 6.0、6.5、または 6.7 を実行しているホスト システムにアクセスできること。 ホスト システムは、次のリソースを仮想デバイス専用にすることができます。
 
  * 少なくとも 4 つの仮想プロセッサ。
  * 少なくとも 8 GB の RAM。 
  * トラフィックをインターネットにルーティングできるネットワークに接続している 1 つのネットワーク インターフェイス。
  * 250 GB の OS ディスク。
  * データ用の 2 TB の仮想ディスク。
* システムに、ESXi ホストを管理するための VMware vSphere client があること。


## <a name="provision-a-virtual-device-in-hypervisor"></a>ハイパーバイザーで仮想デバイスをプロビジョニングする

ハイパーバイザーで仮想デバイスをプロビジョニングするには、次の手順を実行します。

1. システム上にある仮想デバイスのイメージをコピーします。 この仮想イメージ (2 つのファイル) は、Azure portal からダウンロードしています。 このイメージは後で使用するため、コピー先はメモしておいてください。

2. ブラウザーで URL `https://<IP address of the ESXi server>` にアクセスして ESXi サーバーにサインインします。 仮想マシンを作成するには、管理者特権が必要です。

   ![サインイン ページ](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. VMDK を ESXi サーバーにアップロードします。 ナビゲーター ウィンドウで **[ストレージ]** を選択します。

   ![ESXi サーバー サイト上のページのスクリーンショット。[ナビゲーター] ペインで [ストレージ] オプションが選択されています。](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. 右側のウィンドウの **[Datastores]** で VMDK をアップロードするデータストアを選択します。 

    - データストアの種類は、VMFS5 である必要があります。 
    - データストアには、OS とデータ ディスク用の十分な空き領域も必要です。
   
5. 右クリックして **[Browse Datastore]** を選択します。

   ![データストアを参照する](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. **[Datastore Browser]** ウィンドウが表示されます。

   ![データストア ブラウザー](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. ツールバーの **[ディレクトリの作成]** アイコンをクリックして、新しいフォルダーを作成します。 フォルダー名を指定して、メモしておきます。 このフォルダー名は後で仮想マシンを作成するときに使用します (推奨されるベスト プラクティス)。 **[ディレクトリの作成]** をクリックします。

   ![ディレクトリの作成](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. **Datastore Browser** の左側のウィンドウに新しいフォルダーが表示されます。 **[アップロード]** アイコンをクリックし、 **[ファイルのアップロード]** を選択します。

    ![ファイルをアップロードする](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. ダウンロードした VMDK ファイルを参照して、ポイントします。 2 つのファイルがあります。 アップロードするファイルを選択します。

    ![アップロードするファイルを選択する](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. **[開く]** をクリックします。 指定したデータストアへの VMDK ファイルのアップロードが開始されます。 ファイルのアップロードには数分かかる場合があります。
11. アップロードが完了すると、作成したフォルダーのデータストアにファイルが表示されます。 ここで、2 つ目の VMDK ファイルを同じデータストアにアップロードします。 両方のファイルがアップロードされると、2 つのファイルは、1 つのファイルにマージされます。 ディレクトリ内に 1 つのファイルが表示されます。

    ![2 つの VMDK ファイルが 1 つのファイルにマージされる](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. vSphere client のウィンドウに戻ります。 ナビゲーター ウィンドウで **[仮想マシン]** を選択します。 右側のウィンドウで、 **[VM の作成/登録]** をクリックします。

    ![VM を作成または登録する](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. **[New Virtual Machine]\(新しい仮想マシン\)** が表示されます。 [Select creation type]\(作成の種類の選択\) で、 **[Create a new virtual machine]\(新しい仮想マシンの作成\)**  を選択し、 **[Next]\(次へ\)** をクリックします。
    ![[Select creation type]\(作成の種類の選択\) ページ](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. **[Select a Name and OS Name and Location]\(名前と OS 名と場所の選択\)**  ページで、仮想マシンの **[Name]\(名前\)** を指定します。 この名前は、手順 7 で指定したフォルダー名と一致させる必要があります (推奨されるベスト プラクティス)。 **[Guest OS family]\(ゲスト OS ファミリー\)** として [Windows] を、 **[Guest OS version]\(ゲスト OS のバージョン\)** として [Microsoft Windows Server 2016 (64-bit)] を選択します。 **[次へ]** をクリックします。

    ![[Select a Name and OS Name and Location]\(名前と OS 名と場所の選択\) ページ](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. **[Select storage]\(ストレージの選択\)** ページで、VM のプロビジョニングに使用するデータストアを選択します。 **[次へ]** をクリックします。

    ![[Select storage]\(ストレージの選択\) ページ](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. **[Customize settings]\(設定のカスタマイズ\)** ページで、 **[CPU]** を [4] に、 **[Memory]\(メモリ\)** を [8192 MB]\(以上) に、 **[Hard disk 1]\(ハード ディスク 1\)** を [2 TB]\(以上) に設定します。 追加する **SCSI ハード _ ディスク** を選択します。 ここでは、それは LSI Logic SAS でした。 **静的 IDE ディスクはサポートされていません。** **[Hard disk 1]\(ハード ディスク 1\)** は仮想データ ディスクです。 一度ディスクをプロビジョニングすると、圧縮できなくなることに注意してください。 ディスクを縮小しようとすると、デバイスのローカル データすべてが失われます。 

    ![[Customize settings]\(設定のカスタマイズ\) ページ](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    同じページで、 **[Add hard disk]\(ハード ディスクの追加\)** をクリックし、 **[Existing hard disk]\(既存のハード ディスク\)** を選択します。 データ ストア内の VMDK ファイルを選択します。 これにより、OS ディスクが追加されます。 

     ![設定のカスタマイズ -ハード ディスクの追加](./media/data-box-gateway-deploy-provision-vmware/customize-settings-add-hard-disk.png)

    **[New hard disk]\(新しいハード ディスク\)** が表示するまで下にスクロールし、それを展開して設定を表示します。 **[Virtual Device Node]\(仮想デバイス ノード\)** を **[IDE controller 0]\(IDE コントロ―ラー 0\)** に設定します。

     ![設定のカスタマイズ - 新しいハード ディスクの構成](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk.png)

17. (省略可能) *VMware ESXi Server 6.7 を実行している場合にのみ、この手順を実行します*。 **[Customize settings]\(設定のカスタマイズ\)** ページで **[VM options]\(VM のオプション\)** をクリックします。 **[Boot options]\(ブート オプション\) > [Firmware]\(ファームウェア\)** に移動し、 **[BIOS]** に変更します。 既定では、値は [EFI] に設定されます。 **[次へ]** をクリックします。

    ![VMware ESXi Server 6.7 を実行している場合の [Customize settings]\(設定のカスタマイズ\) ページ](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk-esxi.png)

18. **[Ready to Complete]** ページで、新しい仮想マシンに関連するすべての設定を確認します。 CPU に 4、メモリに 8192 MB、ネットワーク インターフェイスに 1、ハード ディスク 2 に IDE コントローラー 0 が設定されていることを確認します。 **[完了]** をクリックします。
   
    ![[Ready to Complete]\(完了の準備完了\) ページ](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![[Ready to Complete]\(完了の準備完了\) ページ 2](./media/data-box-gateway-deploy-provision-vmware/image17.png)

これで仮想マシンがプロビジョニングされました。 その趣旨の通知が表示され、新しい仮想マシンが VM の一覧に追加されます。

![VM の一覧に追加された新しい仮想マシン](./media/data-box-gateway-deploy-provision-vmware/image17.png)

次はこの VM を起動して IP アドレスを取得します。

> [!NOTE]
> (上でプロビジョニングした) 仮想デバイスには VMware ツールをインストールしないことをお勧めします。 VMware ツールをインストールすると、サポートされていない構成になります。

## <a name="start-the-virtual-device-and-get-the-ip"></a>仮想デバイスを起動して IP アドレスを取得する

仮想デバイスを起動して接続するには、次の手順を実行します。

#### <a name="to-start-the-virtual-device"></a>仮想デバイスを起動するには
1. 仮想デバイスを起動します。 右側のウィンドウで、VM の一覧からデバイスを選択し、右クリックしてコンテキスト メニューを表示します。 **[Power]** 、 **[Power on]** の順に選択します。 これで、仮想マシンの電源がオンになります。 Web クライアントの下部ウィンドウで状態を確認できます。

    ![仮想マシンの電源](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. もう一度 VM を選択します。 右クリックして **[Console]\(コンソール\)** を選択します。次に **[Open in a new window]\(新しいウィンドウで開く\)** を選択します。

    ![仮想デバイス コンソールを開く](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. 仮想マシン コンソールが新しいウィンドウで開きます。 

    ![仮想デバイス コンソール](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. デバイスが実行されたら、コンソール ウィンドウの上部中央タブをカーソルでポイントしてクリックします。 **[Guest OS] > [Send keys] > Ctrl+Alt+Delete** を選択します。 これで、VM のロックが解除されます。

   ![仮想デバイスをロック解除する](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. パスワードを入力してマシンにサインインします。 既定のパスワードは *Password1* です。

   ![仮想デバイス パスワードを入力する](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. 手順 6. ～ 8. は、非 DHCP 環境での起動時にのみ適用されます。 DHCP 環境の場合は、手順 6 ～ 8 をスキップし、手順 9 に進みます。 非 DHCP 環境でデバイスを起動した場合は、"**ネットワークを構成するには、Set-HcsIPAddress コマンドレットを使用します**" という趣旨のメッセージが表示されます。 
   
7. ネットワークを構成するには、コマンド プロンプトで `Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効なネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

8. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 例を次に示します。

    `Set-HcsIpAddress –Name Ethernet0 –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. 初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。 デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。 この IP アドレスを使用して、仮想デバイスの Web UI に接続し、ローカル セットアップとアクティブ化を行います。

   ![仮想デバイスのバナー テキストと接続 URL](./media/data-box-gateway-deploy-provision-vmware/image24.png)

デバイスが最小構成要件を満たしていない場合は、バナー テキストにエラーが表示されます (下記参照)。 最小要件を満たすための十分なリソースを確保するようにデバイスの構成を変更する必要があります。 その後、再起動し、デバイスに接続します。 「[ホスト システムが仮想デバイスの最小要件を満たしていることを確認する](#check-the-host-system)」にある最小構成要件を参照してください。

ローカル Web UI を使って初期構成を行っている間に他のエラーが発生した場合は、次のワークフローを参照してください。

- 診断テストを実行して [Web UI のセットアップのトラブルシューティング](data-box-gateway-troubleshoot.md#run-diagnostics)を行う。
- [ログ パッケージを生成してログ ファイルを表示する](data-box-gateway-troubleshoot.md#collect-support-package)。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * ホストがデバイスの最小要件を満たしていることを確認する
> * VMware で仮想デバイスをプロビジョニングする
> * 仮想デバイスを起動し、IP アドレスを取得する

次のチュートリアルに進み、仮想ネットワークの接続、設定、および仮想化を行う方法を学習してください。

* [Data Box Gateway の設定と共有への接続を行う](data-box-gateway-deploy-connect-setup-activate.md)

