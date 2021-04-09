---
title: 'チュートリアル: Data Box Gateway をデプロイするための Azure portal の準備 | Microsoft Docs'
description: Azure Data Box Gateway をデプロイする最初のチュートリアルでは、Azure portal の準備を行います。
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101706097"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>チュートリアル:Azure Data Box Gateway のデプロイを準備する

これは、Azure Data Box Gateway を完全にデプロイするために必要な一連のチュートリアルのうち最初のものです。 このチュートリアルでは、Data Box Gateway リソースをデプロイするために Azure portal を準備する方法を説明します。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

## <a name="get-started"></a>はじめに

Data Box Gateway をデプロイするには、以下の順序どおりにチュートリアルを参照してください。

| **#** | **手順** | **概要** |
| --- | --- | --- | 
| 1. |**[Data Box Gateway のために Azure portal を準備する](data-box-gateway-deploy-prep.md)** |Data Box Gateway リソースを作成して構成した後で、Data Box Gateway 仮想デバイスをプロビジョニングします。 |
| 2. |**[Hyper-V で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[VMware で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V の場合、Windows Server 2016 または Windows Server 2012 R2 上で Hyper-V を実行するホスト システムに Data Box Gateway 仮想デバイスをプロビジョニングして接続します。 <br><br><br> VMware の場合、VMware ESXi 6.0、6.5、または 6.7 を実行するホスト システムに Data Box Gateway 仮想デバイスをプロビジョニングして接続します。<br></br> |
| 3. |**[Data Box Gateway を接続、設定、アクティブ化する](data-box-gateway-deploy-connect-setup-activate.md)** |ローカル Web UI に接続して、デバイス設定を完了し、デバイスをアクティブ化します。 これで、SMB 共有をプロビジョニングできます。  |
| 4. |**[Data Box Gateway でデータを転送する](data-box-gateway-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="prerequisites"></a>前提条件

ここでは、Data Box Gateway リソース、Data Box Gateway デバイス、データセンター ネットワークについて構成の前提条件を示します。

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway リソースの場合

開始する前に次の点を確認します。

* ご利用の Microsoft Azure サブスクリプションで Azure Stack Edge リソースが有効になっていること。 [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。
* Azure Stack Edge/Data Box Gateway、IoT Hub、および Azure Storage のリソースに対してリソース グループ レベルで所有者または共同作成者のアクセス許可を持っていること。
    - Azure Stack Edge/Data Box Gateway のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 また、`Microsoft.DataBoxEdge` プロバイダーが登録されていることも確認する必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
- Microsoft Graph API に対する管理者またはユーザーのアクセス権を持っていること。 詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」を参照してください。
- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway デバイスの場合

仮想デバイスをデプロイする前に次の点を確認します。

- Windows Server 2012 R2 以降の上に配置された Hyper-V、または VMware (ESXi 6.0、6.5、または 6.7) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
- ホスト システムで、次のリソースを Data Box 仮想デバイスのプロビジョニング専用に使用できること。
  
  - 少なくとも 4 つの仮想プロセッサ。
  - 少なくとも 8 GB の RAM。 RAM を 16 GB 以上にすることを強くお勧めします。
  - ネットワーク インターフェイス 1 つ。
  - 250 GB の OS ディスク。
  - システム データ用の 2 TB の仮想ディスク。

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

- Data Box Gateway デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)に関する記事を参照してください。

- Data Box Gateway の通常の動作条件下における帯域幅は次のとおりです。

    - デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
    - ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-a-new-resource"></a>新しいリソースを作成

仮想マシンを管理するために既存の Data Box Gateway リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Data Box Gateway リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure の資格情報を使用して、次のいずれかのポータルにサインインします。

    - Azure portal (URL: [https://portal.azure.com](https://portal.azure.com))。
    - Azure Government ポータル (URL: [https://portal.azure.us](https://portal.azure.us))。 詳細については、[ポータルを使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-portal.md)に関する記事をご確認ください。
    
2. **[+ リソースの作成]** を選択します。

    ![Azure Data Box Gateway の [リソースの作成] ボタン](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. 検索ボックスに「**Data Box Gateway**」と入力し、Enter キーを押します。

    ![Data Box Gateway サービスを検索する](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    **[Azure Data Box Gateway]** を選択します。

    ![Data Box Gateway SKU を選択する](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. **［作成］** を選択します

    ![[作成] をクリックして Data Box Gateway リソースを作成する](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. **[基本]** タブで、次の操作を行います。

    次の **プロジェクトの詳細** を入力または選択します。
    
    |設定  |値  |
    |---------|---------|
    |サブスクリプション    |Data Box Gateway デバイスに使用するサブスクリプションを選択します。 サブスクリプションは、課金アカウントにリンクされます。|
    |Resource group  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。|

   次の **インスタンス情報** を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |名前   |リソースを識別するわかりやすい名前を入力します。<br>名前は 2 から 50 文字で、英字、数字、ハイフンを使用します。 <br> 名前の最初と最後は、文字か数字とする必要があります。 |
    |リージョン  |リソースをデプロイするリージョンを選択します。 デバイスをデプロイする地理的リージョンに近い場所を選択します。| <br> Data Base Gateway および Azure Stack Edge リソースを利用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 <br> Azure Government の場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。|

   **[確認および作成]** を選択して、注文内容を確認します。

   ![Data Box Gateway の注文のプロジェクトとインスタンスの詳細エントリ](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **［作成］** を選択します

    ![確認用に表示された Data Box Gateway リソースの詳細](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

リソースの作成には数分かかります。 リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

![Data Box Gateway の注文の完了](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>仮想デバイスのイメージのダウンロード

Data Box Gateway リソースを作成したら、ホスト システムに仮想デバイスをプロビジョニングするために、適切な仮想デバイスのイメージをダウンロードする必要があります。 仮想デバイスのイメージは、オペレーティング システムごとに存在します。

> [!IMPORTANT]
> Data Box Gateway 上で実行されるソフトウェアは、Data Box Gateway リソースに対してのみ使用できます。

[Azure portal](https://portal.azure.com/) から次の手順に従って、仮想デバイスのイメージをダウンロードしてください。

1. 作成したリソースで、 **[概要]** を選択します。 既存の Azure Data Box Gateway リソースがある場合は、そのリソースを選択してから、 **[概要]** に進みます。 **[デバイスの設定]** を選択します。

    ![新しい Data Box Gateway リソース](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. **[イメージのダウンロード]** タイルで、VM のプロビジョニングに使用するホスト サーバーから、オペレーティング システムに対応する仮想デバイスのイメージを選択します。 イメージ ファイルは約 5.6 GB です。
   
   * [Windows Server 2012 R2 以降の Hyper-V 用 VHDX](https://aka.ms/dbe-vhdx-2012)
   * [VMware ESXi 6.0、6.5、または 6.7 用 VMDK](https://aka.ms/dbe-vmdk)。

    ![Data Box Gateway の仮想デバイスのイメージをダウンロードする](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. ファイルをローカル ドライブにダウンロードして解凍し、解凍したファイルの場所をメモしておきます。


## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Data Box Gateway リソースが起動して稼働中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Data Box Gateway デバイスをアクティブ化し、リソースに接続します。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースを選択してから、 **[概要]** を選択します。 **[デバイスの設定]** の **[Configure and activate]\(構成とアクティブ化\)** タイルに移動します。

    ![[Configure and activate]\(構成とアクティブ化\) タイル](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. **[キーの生成]** を選択して、アクティブ化キーを作成します。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

次のチュートリアルに進んで、Data Box Gateway の仮想マシンをプロビジョニングする方法を学びます。 使用するホスト オペレーティング システムに応じて手順は異なります。

> [!div class="nextstepaction"]
> [Hyper-V で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [VMware で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-vmware.md)