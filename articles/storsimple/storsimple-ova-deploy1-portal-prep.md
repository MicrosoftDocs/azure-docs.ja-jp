---
title: "StorSimple Virtual Array 1 のデプロイ - ポータルの準備"
description: "StorSimple Virtual Array をデプロイする最初のチュートリアルでは、ポータルの準備を行います。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 232e765b-9d2c-4907-8b11-aa7e1a17d3df
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 842ea26941a1141cde7db4e6ebff07d52aed9450


---
# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>StorSimple Virtual Array のデプロイ - ポータルの準備
![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>概要
この記事は、2016 年 3 月の一般公開 (GA) リリースを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたは StorSimple 仮想デバイスとも呼ばれます) に適用されます。 仮想アレイをファイル サーバーまたは iSCSI サーバーとして完全にデプロイするために必要なデプロイ チュートリアル シリーズの最初の記事です。 仮想アレイをプロビジョニングする前に、StorSimple Manager サービスを作成して構成するために必要な準備について説明します。 また、デプロイ構成チェックリストや構成の前提条件へのリンクも記載しています。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 開始する前に、デプロイ構成チェックリストを確認することをお勧めします。 ポータルの準備は 10 分ほどで完了します。

この記事に記載されている情報は、Azure クラシック ポータルだけでなく、Microsoft Azure Government Cloud での StorSimple Virtual Arrays のデプロイに適用されます。

### <a name="get-started"></a>作業開始
デプロイのワークフローは、ポータルの準備、仮想化環境内への仮想アレイのプロビジョニング、セットアップ ウィザードの完了という構成になっています。 ファイル サーバーまたは iSCSI サーバーとして StorSimple Virtual Array をデプロイする作業を開始するには、次の表のリソース (記事とビデオ) を参照してください。

#### <a name="deployment-articles"></a>デプロイに関する記事
StorSimple Virtual Array をデプロイするには、次の記事を順番に参照してください。

| **#** | **手順** | **作業内容** | **参照ドキュメント** |
| --- | --- | --- | --- |
| 1. |**Azure クラシック ポータルのセットアップ** |StorSimple 仮想デバイスをプロビジョニングする前に、StorSimple Manager サービスを作成して構成します。 |[ポータルを準備する](storsimple-ova-deploy1-portal-prep.md) |
| 2. |**Virtual Array のプロビジョニング** |Hyper-V の場合: Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行するホスト システムに StorSimple 仮想デバイスをプロビジョニングして接続します。 <br></br> <br></br> VMware の場合: VMware ESXi 5.5 以降を実行するホスト システムに StorSimple オンプレミス仮想デバイスをプロビジョニングして接続します。<br></br> |[Hyper-V で仮想アレイをプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware で Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md) |
| 3. |**Virtual Array のセットアップ** |ファイル サーバーの場合: 初期セットアップを実行し、StorSimple ファイル サーバーを登録して、デバイスのセットアップを完了します。 これで、SMB 共有をプロビジョニングできます。 <br></br> <br></br> iSCSI サーバーの場合: 初期セットアップを実行し、StorSimple iSCSI サーバーを登録して、デバイスのセットアップを完了します。 これで、iSCSI ボリュームをプロビジョニングできます。 |[ファイル サーバーとして Virtual Array をセットアップする](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[iSCSI サーバーとして Virtual Array をセットアップする](storsimple-ova-deploy3-iscsi-setup.md) |

#### <a name="deployment-videos"></a>デプロイに関するビデオ
| **手順** | **ビデオ** |
| --- | --- |
| StorSimple Virtual Array の使用を開始するための詳細な手順 |[StorSimple Virtual Array の使用を開始する](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/) |
| HYPER-V で StorSimple Virtual Array をプロビジョニングするための詳細な手順 |[StorSimple Virtual Array を作成する](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
| StorSimple Virtual Array を構成して登録するための詳細な手順 |[StorSimple Virtual Array を構成する](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/) |
| ファイル サーバーとして構成した StorSimple Virtual Array で共有を作成し、共有をバックアップし、データを復元するための詳細な手順 |[StorSimple Virtual Array を使用する](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/) |
| StorSimple Virtual Array のフェールオーバーと障害復旧のための詳細な手順 |[StorSimple Virtual Array の障害復旧](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/) |

これで、Azure クラシック ポータルをセットアップする準備が整いました。

## <a name="configuration-checklist"></a>構成チェックリスト
構成チェックリストでは、StorSimple デバイスでソフトウェアを構成する前に収集する必要のある情報について説明しています。 事前にこの情報を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。 StorSimple 仮想デバイスをファイル サーバーとしてデプロイする場合と iSCSI サーバーとしてデプロイする場合とではチェックリストの内容が異なります。

* [StorSimple Virtual Array のファイル サーバー構成チェックリスト](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)をダウンロード
* [StorSimple Virtual Array の iSCSI サーバー構成チェックリスト](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)をダウンロード

## <a name="prerequisites"></a>前提条件
ここでは、StorSimple Manager サービス、StorSimple 仮想デバイス、およびデータセンター ネットワークの構成の前提条件について説明します。

### <a name="for-the-storsimple-manager-service"></a>StorSimple Manager サービスの場合
開始する前に次の点を確認します。

* アクセスの資格情報を持つ Microsoft アカウントがあること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。
* Microsoft Azure サブスクリプションで StorSimple Manager サービスが有効になっていること。

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 仮想デバイスの場合
仮想デバイスをデプロイする前に次の点を確認します。

* Windows Server 2008 R2 以降の Hyper-V または VMware (ESXi 5.5 以降) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。
  
  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。
  * ネットワーク インターフェイス&1; つ。
  * システム データ用の 500 GB の仮想ディスク。

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件
開始する前に次の点を確認します。

* StorSimple デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、「 [StorSimple Virtual Array のシステム要件](storsimple-ova-system-requirements.md)」を参照してください。
* StorSimple 仮想デバイスで専用の 5 Mbps インターネット帯域幅 (またはそれ以上) が常に利用できること。 この帯域幅はその他のアプリケーションと共有できません。

## <a name="step-by-step-preparation"></a>準備の手順
次の手順を順番に実行すると、StorSimple Manager サービス用のポータルを準備できます。

## <a name="step-1-create-a-new-service"></a>手順 1. 新しいサービスを作成する
StorSimple Manager サービスの 1 つのインスタンスで複数の StorSimple 1200 デバイスを管理できます。 StorSimple Manager サービスの新しいインスタンスを作成するには、次の手順を実行します。 既存の StorSimple Manager サービスで StorSimple 1200 デバイスを管理する場合は、この手順は省略して「 [手順 2: サービス登録キーを取得する](#step-2-get-the-service-registration-key)」に進みます。

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [!IMPORTANT]
> サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。
> 
> * ストレージ アカウントを自動的に作成していない場合は、「 [サービスの新しいストレージ アカウントを構成する](#optional-step-configure-a-new-storage-account-for-the-service) 」に移動して詳細な手順をご確認ください。
> * ストレージ アカウントの自動作成を有効にしている場合は、「 [手順 2. サービス登録キーを取得する](#step-2-get-the-service-registration-key)」に進みます。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>手順 2. サービス登録キーを取得する
StorSimple Manager サービスが稼働したら、サービス登録キーを取得する必要があります。 このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

[Azure クラシック ポータル](https://manage.windowsazure.com/)で、次の手順を実行します。

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [!NOTE]
> StorSimple Manager サービスに登録する必要があるすべての StorSimple Manager デバイスの登録に、サービス登録キーを使用します。
> 
> 

## <a name="step-3-download-the-virtual-device-image"></a>手順 3: 仮想デバイスのイメージをダウンロードする
サービス登録キーを取得したら、ホスト システムに仮想デバイスをプロビジョニングするために、適切な仮想デバイスのイメージをダウンロードする必要があります。 仮想デバイスのイメージはオペレーティング システムに固有で、Azure クラシック ポータルの [クイック スタート] ページからダウンロードできます。

> [!IMPORTANT]
> StorSimple Virtual Array で実行されているソフトウェアは、Storsimple Manager サービスとの組み合わせでのみ使用できます。
> 
> 

[Azure クラシック ポータル](https://manage.windowsazure.com/)で、次の手順を実行します。

#### <a name="to-get-the-virtual-device-image"></a>仮想デバイスのイメージを取得するには
1. **[StorSimple Manager]** サービス ページで、作成したサービスをクリックします。 この操作により、 **[クイック スタート]** ページが表示されます。 (**[クイック スタート]** ページには、クイック スタート アイコン ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) をクリックすることによっていつでもアクセスできます)。
2. Microsoft ダウンロード センターからダウンロードするイメージに対応するリンクをクリックします。 イメージ ファイルは約 4.8 GB です。
   
   * Windows Server 2012 以降の Hyper-V 用 VHDX
   * Windows Server 2008 R2 以降の Hyper-V 用 VHD
   * VMWare ESXi 5.5 以降用 VMDK
3. ファイルをローカル ドライブにダウンロードして解凍し、解凍したファイルの場所をメモしておきます。

![動画アイコン](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **ビデオ**

StorSimple Virtual Array の使用を開始するための詳細な手順を説明したビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Get-Started-with-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>省略可能な手順: サービスの新しいストレージ アカウントを構成する
これは省略可能な手順で、サービスでストレージ アカウントの自動作成を有効にしていない場合のみ実行する必要があります。

別のリージョンで Azure ストレージ アカウントを作成する必要がある場合、詳しい手順については、[ストレージ アカウントの作成方法](../storage/storage-create-storage-account.md#create-a-storage-account)に関するセクションをご覧ください。

既存の Microsoft Azure ストレージ アカウントを追加するには、 [Azure クラシック ポータル](https://manage.windowsazure.com/) の [StorSimple Manager サービス] ページで次の手順を実行します。

#### <a name="to-add-a-storage-account"></a>ストレージ アカウントを追加するには
1. StorSimple Manager サービスのランディング ページでサービスを選択し、ダブルクリックします。 この操作により、 **[クイック スタート]** ページが表示されます。 **[構成]** ページを選択します。
2. **[ストレージ アカウントの追加/編集]**をクリックします。 **[ストレージ アカウントの追加/編集]** ダイアログ ボックスで、次の手順を実行します。
   
   1. **[新規追加]**をクリックします。
   2. ストレージ アカウントの名前を入力します。
   3. Microsoft Azure ストレージ アカウントのプライマリ **アクセス キー** を入力します。
   4. **[SSL モードを有効にする]** を選択して、デバイスとクラウド間のネットワーク通信用にセキュリティで保護されたチャネルを作成します。 プライベート クラウド内で運用している場合にのみ、 **[SSL モードを有効にする]** チェック ボックスをオフにします。
   5. チェック マーク アイコン ![](./media/storsimple-ova-deploy1-portal-prep/image7.png)をクリックします。 ストレージ アカウントが正常に作成されると通知が表示されます。
      
      ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)
3. 新しく作成されたストレージ アカウントは、**[構成]** ページの **[ストレージ アカウント]** に表示されます。 **[保存]** をクリックして、新しく作成されたストレージ アカウントを保存します。 確認を求められたら、 **[OK]** をクリックします。

## <a name="next-step"></a>次のステップ
次の手順では、StorSimple 仮想デバイス用の仮想マシンをプロビジョニングします。 使用するホスト オペレーティング システムに応じて手順は異なります。

* [Hyper-V で StorSimple Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-hyperv.md)
* [VMware で StorSimple Virtual Array をプロビジョニングする](storsimple-ova-deploy2-provision-vmware.md)




<!--HONumber=Jan17_HO5-->


