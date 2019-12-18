---
title: StorSimple Virtual Array のポータルの準備 | Microsoft Docs
description: StorSimple Virtual Array をデプロイする最初のチュートリアルでは、Azure Portal の準備を行います。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccdd234f951327aeb5baa6aabcac4314b9de7b34
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517099"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple Virtual Array のデプロイ - Azure Portal の準備

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>概要

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

これは、Resource Manager モデルを使用して、仮想アレイを、ファイル サーバーまたは iSCSI サーバーとして完全にデプロイするためのチュートリアル シリーズの最初の記事で、 仮想アレイをプロビジョニングする前に、StorSimple デバイス マネージャー サービスを作成して構成するために必要な準備について説明します。 デプロイ構成チェックリストや構成の前提条件へのリンクも記載しています。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 開始する前に、デプロイ構成チェックリストを確認することをお勧めします。 ポータルの準備は 10 分ほどで完了します。

この記事に記載されている情報は、Azure Portal および Microsoft Azure Government Cloud での StorSimple Virtual Array のデプロイに適用されます。

### <a name="get-started"></a>作業開始
デプロイのワークフローは、ポータルの準備、仮想化環境内への仮想アレイのプロビジョニング、セットアップ ウィザードの完了という構成になっています。 ファイル サーバーまたは iSCSI サーバーとして StorSimple Virtual Array をデプロイする作業を開始するには、次の表のリソースを参照してください。

#### <a name="deployment-articles"></a>デプロイに関する記事

StorSimple Virtual Array をデプロイするには、次の記事を順番に参照してください。

| **#** | **手順** | **説明** | **参照ドキュメント** |
| --- | --- | --- | --- |
| 1. |**Azure Portal のセットアップ** |StorSimple Virtual Array をプロビジョニングする前に、StorSimple デバイス マネージャー サービスを作成して構成します。 |[ポータルを準備する](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Virtual Array のプロビジョニング** |Hyper-V の場合、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行しているホスト システムで、StorSimple Virtual Array をプロビジョニングし、そのアレイに接続します。 <br></br> <br></br> VMware の場合、VMware ESXi 5.0、5.5、6.0、または 6.5 を実行しているホスト システムで、StorSimple Virtual Array をプロビジョニングし、そのアレイに接続します。<br></br> |[Hyper-V で Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware で Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Virtual Array のセットアップ** |ファイル サーバーの場合: 初期セットアップを実行し、StorSimple ファイル サーバーを登録して、デバイスのセットアップを完了します。 これで、SMB 共有をプロビジョニングできます。 <br></br> <br></br> iSCSI サーバーの場合: 初期セットアップを実行し、StorSimple iSCSI サーバーを登録して、デバイスのセットアップを完了します。 これで、iSCSI ボリュームをプロビジョニングできます。 |[ファイル サーバーとして Virtual Array をセットアップする](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[iSCSI サーバーとして Virtual Array をセットアップする](storsimple-virtual-array-deploy3-iscsi-setup.md) |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="configuration-checklist"></a>構成チェックリスト

構成チェックリストでは、StorSimple Virtual Array でソフトウェアを構成する前に収集する必要のある情報について説明します。 事前にこの情報を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。 StorSimple Virtual Array を、ファイル サーバーとしてデプロイする場合と iSCSI サーバーとしてデプロイする場合とでは、チェックリストの内容が異なります。

* [StorSimple Virtual Array のファイル サーバー構成チェックリスト](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)をダウンロード
* [StorSimple Virtual Array の iSCSI サーバー構成チェックリスト](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)をダウンロード

## <a name="prerequisites"></a>前提条件

ここでは、StorSimple デバイス マネージャー サービス、StorSimple Virtual Array、およびデータセンター ネットワークの構成の前提条件について説明します。

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの場合

開始する前に次の点を確認します。

* アクセスの資格情報を持つ Microsoft アカウントがあること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。
* Microsoft Azure サブスクリプションが StorSimple デバイス マネージャー サービスに対して有効であること。

### <a name="for-the-storsimple-virtual-array"></a>StorSimple Virtual Array の場合

仮想アレイをデプロイする前に次の点を確認します。

* Windows Server 2008 R2 以降の上に配置された Hyper-V、または VMware (ESXi 5.0、5.5、6.0、または 6.5) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを仮想アレイのプロビジョニング専用に使用できること。
  
  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 ファイル サーバーとして仮想アレイを構成する予定がある場合、8 GB でサポートできるのは 2 百万個のファイルです。 2 百万から 4 百万個のファイルをサポートするには 16 GB の RAM が必要です。
  * ネットワーク インターフェイス 1 つ。
  * システム データ用の 500 GB の仮想ディスク。

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

* StorSimple デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、「 [StorSimple Virtual Array のシステム要件](storsimple-ova-system-requirements.md)」を参照してください。
* StorSimple Virtual Array で専用の 5 Mbps インターネット帯域幅 (またはそれ以上) が常に利用できること。 この帯域幅はその他のアプリケーションと共有できません。

## <a name="step-by-step-preparation"></a>準備の手順

次の手順を順番に実行すると、StorSimple デバイス マネージャー サービス用のポータルを準備できます。

## <a name="step-1-create-a-new-service"></a>手順 1:新しいサービスの作成

StorSimple デバイス マネージャー サービスの 1 つのインスタンスで、複数の StorSimple Virtual Array を管理できます。 StorSimple デバイス マネージャー サービスのインスタンスを作成するには、次の手順を実行します。 既存の StorSimple デバイス マネージャー サービスで仮想アレイを管理する場合、この手順は省略して、「[手順 2:サービス登録キーを取得する](#step-2-get-the-service-registration-key)」をご覧ください。

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。
> 
> * ストレージ アカウントを自動的に作成していない場合は、「 [サービスの新しいストレージ アカウントを構成する](#optional-step-configure-a-new-storage-account-for-the-service) 」に移動して詳細な手順をご確認ください。
> * ストレージ アカウントの自動作成を有効にしている場合は、「 [手順 2:サービス登録キーを取得する](#step-2-get-the-service-registration-key)」をご覧ください。
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>手順 2:サービス登録キーを取得する

StorSimple デバイス マネージャー サービスが稼働したら、サービス登録キーを取得する必要があります。 このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

[Azure Portal](https://portal.azure.com/) で、次の手順を実行します。

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> サービス登録キーは、StorSimple デバイス マネージャー サービスに登録する必要がある、すべての StorSimple デバイス マネージャー デバイスの登録に使用します。
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>手順 3:仮想アレイのイメージをダウンロードする

サービス登録キーを取得したら、ホスト システムで仮想アレイをプロビジョニングするために、適切な仮想アレイのイメージをダウンロードする必要があります。 仮想アレイのイメージはオペレーティング システムに固有で、Azure Portal の [クイック スタート] ページからダウンロードできます。

> [!IMPORTANT]
> StorSimple Virtual Array で実行されているソフトウェアは、StorSimple デバイス マネージャー サービスでのみ使用できます。
> 
> 

[Azure Portal](https://portal.azure.com/) で、次の手順を実行します。

#### <a name="to-get-the-virtual-array-image"></a>仮想アレイのイメージを取得するには

1. [Azure Portal](https://portal.azure.com/)にサインインします。 
2. Azure Portal で、 **[参照] > [StorSimple デバイス マネージャー]** の順にクリックします。
3. 既存の StorSimple デバイス マネージャー サービスを選択します。 **[StorSimple デバイス マネージャー]** ブレードで **[クイック スタート]** をクリックします。 
4. Microsoft ダウンロード センターからダウンロードするイメージに対応するリンクをクリックします。 イメージ ファイルは約 4.8 GB です。
   
   * Windows Server 2012 以降の Hyper-V 用 VHDX
   * Windows Server 2008 R2 以降の Hyper-V 用 VHD
   * VMWare ESXi 5.0、5.5、6.0、または 6.5 用 VMDK
5. ファイルをローカル ドライブにダウンロードして解凍し、解凍したファイルの場所をメモしておきます。

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>省略可能な手順:サービスの新しいストレージ アカウントを構成する

この手順は省略可能で、サービスでストレージ アカウントの自動作成を有効にしていない場合にのみ実行する必要があります。

別のリージョンで Azure ストレージ アカウントを作成する必要がある場合、詳しい手順については、[ストレージ アカウントの作成方法](../storage/common/storage-quickstart-create-account.md)に関するセクションをご覧ください。

既存の Microsoft Azure ストレージ アカウントを追加するには、[Azure Portal](https://ms.portal.azure.com/) の [StorSimple Device Manager service (StorSimple デバイス マネージャー サービス)] ページで、次の手順を実行します。

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>デバイス マネージャー サービスと同じ Azure サブスクリプションを持つ、ストレージ アカウントの資格情報を追加するには

1. デバイス マネージャー サービスに移動し、選択してダブルクリックします。 **[概要]** ブレードが開きます。
2. **[構成]** セクションで **[ストレージ アカウントの資格情報]** を選択します。
3. **[追加]** をクリックします。
4. **[ストレージ アカウントの追加]** ブレードで、次の操作を行います。
   
   1. **[サブスクリプション]** で **[現在]** を選択します。
   
   2. Azure ストレージ アカウントの名前を指定します。
   
   3. **[有効にする]** を選択し、StorSimple デバイスとクラウドの間のネットワーク通信用に、セキュリティで保護されたチャネルを作成します。 **[無効にする]** は、プライベート クラウド内で動作している場合にのみ選択します。
   
   4. **[追加]** をクリックします。 ストレージ アカウントが正常に作成されると、その旨が通知が表示されます。<br></br>
   
      ![既存のストレージ アカウントの資格情報の追加](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>次のステップ

次の手順では、StorSimple Virtual Array 用の仮想マシンをプロビジョニングします。 使用するホスト オペレーティング システムに応じて手順は異なります。

* [Hyper-V で StorSimple Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [VMware で StorSimple Virtual Array をプロビジョニングする](storsimple-virtual-array-deploy2-provision-vmware.md)

