---
title: Hyper-V での Azure Data Box Gateway のプロビジョニングに関するチュートリアル | Microsoft Docs
description: Azure Data Box Gateway をデプロイするための 2 番目のチュートリアルには、Hyper-V での仮想デバイスのプロビジョニングが含まれます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727687"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>チュートリアル:Hyper-V で Azure Data Box Gateway をプロビジョニングする

## <a name="overview"></a>概要

このチュートリアルでは、Windows Server 2016、Windows Server 2012 R2、または Windows Server 2012 の Hyper-V を実行しているホスト システムで、Data Box Gateway をプロビジョニングする方法について説明します。

仮想デバイスをプロビジョニングして構成するには、管理者特権が必要です。 プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * ホストがデバイスの最小要件を満たしていることを確認する
> * ハイパーバイザーで仮想デバイスをプロビジョニングする
> * 仮想デバイスを起動し、IP アドレスを取得する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Windows Server 2016 または Windows Server 2012 R2 の Hyper-V を実行するホスト システムに仮想デバイスをプロビジョニングする際の前提条件は以下のとおりです。

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway リソースの場合

開始する前に次の点を確認します。

* [Data Box Gateway 用のポータルの準備](data-box-gateway-deploy-prep.md)に関するページの手順がすべて完了していること。
* [Data Box Gateway 用のポータルの準備](data-box-gateway-deploy-prep.md)に関するページの説明に従って、Hyper-V 用の仮想デバイス イメージを Azure portal からダウンロードしていること。

  > [!IMPORTANT]
  > Data Box Gateway 上で実行されるソフトウェアは、Data Box Gateway リソースに対してのみ使用できます。

### <a name="for-the-data-box-gateway-virtual-device"></a>Data Box Gateway 仮想デバイスの場合

デバイスをデプロイする前に次の点を確認します。

* Windows Server 2012 R2 以降の Hyper-V を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。

  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 RAM を 16 GB 以上にすることを強くお勧めします。
  * ネットワーク インターフェイス 1 つ。
  * 250 GB の OS ディスク。
  * データ用の 2 TB の仮想ディスク。

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合

作業を開始する前に、次のことを行います。

* Data Box Gateway をデプロイするためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成します。 詳細については、[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md#networking-port-requirements)に関する記事を参照してください。
* インターネットの最小帯域幅が、デバイスの最適動作を可能にする 20 Mbps であることを確認します。

## <a name="check-the-host-system"></a>ホスト システムをチェックする

仮想デバイスを作成するには、以下が必要です。

* Windows Server 2016、Windows Server 2012 R2、または Windows Server 2012 にインストールされた Hyper-V ロール。
* ホストに接続されている Microsoft Windows クライアント上の Microsoft Hyper-V マネージャー。
* 仮想デバイスを作成している基盤となるハードウェア (ホスト システム) で、次のリソースを仮想デバイス専用に使用できることを確認します。

  * 少なくとも 4 つの仮想プロセッサ。
  * 少なくとも 8 GB の RAM。
  * トラフィックをインターネットにルーティングできるネットワークに接続している 1 つのネットワーク インターフェイス。
  * 250 GB の OS ディスク。
  * システム データ用の 2 TB の仮想ディスク。

## <a name="bitlocker-considerations"></a>BitLocker の考慮事項

* Data Box Gateway 仮想マシンの BitLocker を有効にすることをお勧めします。 既定では、BitLocker は有効になっていません。 詳細については、次を参照してください。
  * [Hyper-V マネージャーでの暗号化サポート設定](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [仮想マシンでの BitLocker のサポート](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>ハイパーバイザーで仮想デバイスをプロビジョニングする

ハイパーバイザーでデバイスをプロビジョニングするには、次の手順を実行します。

1. Windows Server ホストで、仮想デバイスのイメージをローカル ドライブにコピーします。 この VHDX イメージは、Azure portal からダウンロードしました。 このイメージは後で使用するため、コピー先はメモしておいてください。

2. **サーバー マネージャー** を開きます。 右上隅の **[ツール]** をクリックし、 **[Hyper-V マネージャー]** を選択します。

    ![サーバー マネージャーで Hyper-V マネージャーを選択する](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. **Hyper-V マネージャー** のスコープ ウィンドウで、システム ノードを右クリックしてコンテキスト メニューを開き、 **[新規]**  >  **[仮想マシン]** の順にクリックします。

   ![Hyper-V マネージャーで新しい仮想マシンを作成する](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. 仮想マシンの新規作成ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。
5. **[名前と場所を指定]** ページで、仮想デバイスの **名前** を入力します。 **[次へ]** をクリックします。

   ![[名前と場所の指定] ページ](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. **[世代の指定]** ページで、.vhdx デバイスのイメージの種類に **[第 2 世代]** を選択し、 **[次へ]** をクリックします。    

   ![[世代の指定] ページ](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. **[メモリの割り当て]** ページで、**8192 MB** 以上の **起動メモリ** を指定します。動的メモリは有効にしないでください。 **[次へ]** をクリックします。

   ![[メモリの割り当て] ページ](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. **[ネットワークの構成]** ページで、インターネットに接続されている仮想スイッチを指定し、 **[次へ]** をクリックします。

   ![[ネットワークの構成] ページ](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. **[仮想ハード ディスクの接続]** ページで、 **[既存の仮想ハード ディスクを使用する]** を選択し、仮想デバイスのイメージの場所を指定して、 **[次へ]** をクリックします。

   ![[仮想ハード ディスクの接続] ページ](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. **[概要]** を確認し、 **[完了]** をクリックして仮想マシンを作成します。

    ![[仮想マシンの新規作成ウィザードの完了] ページ](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. 最小要件を満たすには、4 個の仮想プロセッサが必要です。 4 個の仮想プロセッサを追加するには、 **[Hyper-V マネージャー]** ウィンドウで対象のホスト システムを選択します。 右側のウィンドウの **[仮想マシン]** の一覧で、先ほど作成した仮想マシンを見つけます。 マシン名を選択して右クリックし、 **[設定]** を選択します。

    ![仮想マシンの設定](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. **[設定]** ページの左側のウィンドウで **[プロセッサ]** をクリックします。 右側のウィンドウで、 **[仮想プロセッサの数]** を 4 (またはそれ以上) に設定します。 **[Apply]** をクリックします。

    ![[設定] ページで仮想プロセッサ数を設定する](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. 最小要件を満たすには、2 TB の仮想データ ディスクも追加する必要があります。 **[設定]** ページで次の操作を行います。

    1. 左側のウィンドウで **[SCSI コントローラー]** を選択します。
    2. 右側のウィンドウで **[ハード ドライブ]** を選択し、 **[追加]** をクリックします。

    ![[設定] ページでハード ドライブを追加する](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. **[ハード ドライブ]** ページで **[仮想ハード ディスク]** を選択し、 **[新規]** をクリックします。 **仮想ハード ディスクの新規作成ウィザード** が開始されます。

    ![仮想ハード ディスクの新規作成ウィザード](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. 仮想ハード ディスクの新規作成ウィザードの **[開始する前に]** ページで、 **[次へ]** をクリックします。
16. **[ディスク フォーマットの選択]** ページで、既定のオプションの **[VHDX]** 形式をそのまま使用します。 **[次へ]** をクリックします。
17. **[ディスクの種類の選択]** ページで、仮想ハード ディスクの種類を **[容量可変]** に設定します (推奨)。 **固定サイズ** のディスクでも動作しますが、待機時間が長くなる可能性があります。 **[差分]** は使用しないことをお勧めします。 **[次へ]** をクリックします。

    ![[ディスクの種類の選択] ページ](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. **[名前と場所の指定]** ページで、データ ディスクの **名前** と **場所** を入力します (場所は参照することもできます)。 **[次へ]** をクリックします。

    ![[名前と場所の指定] ページ](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. **[ディスクの構成]** ページで、 **[新しい空の仮想ハード ディスクを作成する]** オプションを選択し、サイズを **2 TB** (またはそれ以上) に指定します。

    2 TB は最小要件ですが、より容量の大きいディスクを常にプロビジョニングできます。 一度ディスクをプロビジョニングすると、圧縮できなくなることに注意してください。 ディスクを縮小しようとすると、デバイスのローカル データすべてが失われます。 データ ディスクの拡張はサポートされていません。 **[次へ]** をクリックします。

    ![[ディスクの構成] ページ](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. **[概要]** ページで仮想データ ディスクの詳細を確認し、問題がなければ **[完了]** をクリックしてディスクを作成します。 ウィザードが終了し、仮想ハード ディスクがコンピューターに追加されます。

    ![[仮想ハード ディスクの新規作成ウィザードの完了] ページ](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. **[設定]** ページに戻ります。 **[OK]** をクリックして **[設定]** ページを閉じ、[Hyper-V マネージャー] ウィンドウに戻ります。

    ![[設定] ページ](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

これで、仮想マシンが完全に構成されました。

> [!NOTE]
> 構成した VHD をコピーして新しい Data Box Gateway をプロビジョニングすることはできません。 新しい Data Box Gateway 仮想デバイスは、Azure portal からダウンロードした Hyper-V の仮想デバイス イメージからそれぞれプロビジョニングする必要があります。

## <a name="start-the-virtual-device-and-get-the-ip"></a>仮想デバイスを起動して IP アドレスを取得する

仮想デバイスを起動して接続するには、次の手順を実行します。

#### <a name="to-start-the-virtual-device"></a>仮想デバイスを起動するには

1. 仮想デバイスを起動します。

   ![仮想デバイスを起動する](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. デバイスが起動したら、デバイスを選択して右クリックし、 **[接続]** を選択します。

3. デバイスの準備ができるまでに 10 から 15 分かかる場合があります。 進行状況を示すステータス メッセージがコンソールに表示されます。 デバイスの準備ができたら **[アクション]** に移動します。 `Ctrl + Alt + Delete` キーを押して、仮想デバイスにサインインします。 既定のユーザーは *EdgeUser* で、既定のパスワードは *Password1* です。

   ![仮想マシンにサインインする](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. 手順 5 ～ 7 は、非 DHCP 環境での起動時にのみ適用されます。 DHCP 環境の場合は、これらの手順をスキップします。 非 DHCP 環境でデバイスを起動した場合は、結果にメッセージが表示されます。

5. ネットワークを構成するには、`Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効なネットワーク インターフェイスの一覧を表示します。 デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet`です。

6. `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。 次の例を参照してください。

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. 初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。 デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。 この IP アドレスを使用して、仮想デバイスの Web UI に接続し、ローカル セットアップとアクティブ化を行います。

   ![IP アドレスと接続 URL が含まれている仮想デバイスのバナー](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

デバイスが最小構成要件を満たしていない場合は、バナー テキストにエラーが表示されます。 最小要件を満たすだけのリソースにコンピューターが対応できるように、デバイスの構成を変更します。 その後、再起動し、デバイスに接続します。 「[ホスト システムが仮想デバイスの最小要件を満たしていることを確認する](#check-the-host-system)」にある最小構成要件を参照してください。

ローカル Web UI を使って初期構成を行っている間に他のエラーが発生した場合は、次のワークフローを参照してください。

* 診断テストを実行して [Web UI のセットアップのトラブルシューティング](data-box-gateway-troubleshoot.md#run-diagnostics)を行う。
* [ログ パッケージを生成してログ ファイルを表示する](data-box-gateway-troubleshoot.md#collect-support-package)。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * ホストがデバイスの最小要件を満たしていることを確認する
> * ハイパーバイザーで仮想デバイスをプロビジョニングする
> * 仮想デバイスを起動し、IP アドレスを取得する

次のチュートリアルに進み、仮想ネットワークの接続、設定、および仮想化を行う方法を学習してください。

> [!div class="nextstepaction"]
> [Data Box Gateway の接続と設定](./data-box-gateway-deploy-connect-setup-activate.md)
