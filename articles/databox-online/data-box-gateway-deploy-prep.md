---
title: 'チュートリアル: Data Box Gateway をデプロイするための Azure portal の準備 | Microsoft Docs'
description: Azure Data Box Gateway をデプロイする最初のチュートリアルでは、Azure portal の準備を行います。
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 428b42e62f44d182de109740359544135561e54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441489"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>チュートリアル:Azure Data Box Gateway のデプロイを準備する


これは、Azure Data Box Gateway を完全にデプロイするために必要な一連のチュートリアルのうち最初のものです。 このチュートリアルでは、Data Box Gateway リソースをデプロイするために Azure portal を準備する方法を説明します。 

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

## <a name="get-started"></a>作業開始

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

- Microsoft Azure サブスクリプションで Data Box Gateway リソースがサポートされていること。 従量課金制のサブスクリプションもサポートされます。
- Data Box Edge/Data Box Gateway、IoT Hub、および Azure Storage のリソースに対してリソース グループ レベルの所有者または共同作成者のアクセス許可を持っていること。

    - Data Box Edge/Data Box Gateway のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 また、`Microsoft.DataBoxEdge` プロバイダーが登録されていることも確認する必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
- Azure Active Directory Graph API に対して管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。
- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway デバイスの場合

仮想デバイスをデプロイする前に次の点を確認します。

- Windows Server 2012 R2 以降の上に配置された Hyper-V、または VMware (ESXi 6.0、6.5、または 6.7) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
- ホスト システムで、次のリソースを Data Box 仮想デバイスのプロビジョニング専用に使用できること。
  
  - 少なくとも 4 つの仮想プロセッサ。
  - 少なくとも 8 GB の RAM。
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

1. Microsoft Azure の資格情報を使用して、次のサイトにサインインします。

    - Azure portal (URL: [https://portal.azure.com](https://portal.azure.com))。
    - または、Azure Government ポータル (URL: [https://portal.azure.us](https://portal.azure.us))。 詳細については、[ポータルを使用して Azure Government に接続する](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)方法に関するページを参照してください。

2. 左側のウィンドウで、 **[+ リソースの作成]** を選択します。 **[Data Box Edge / Data Box Gateway]** を検索します。 [Data Box Edge / Data Box Gateway] を選択します。 **作成** を選択します。
3. Data Box Gateway デバイスに使用するサブスクリプションを選択します。 Data Box Gateway リソースをデプロイするリージョンを選択します。 このリリースでは、米国東部、東南アジア、西ヨーロッパを選択できます。 デバイスをデプロイする地理的リージョンに最も近い場所を選択します。 **[Data Box Gateway]** オプションで **[作成]** を選択します。

    ![Data Box Gateway サービスの検索](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. **[基本]** タブで、次の**プロジェクト情報**を入力または選択します。
    
    |Setting  |値  |
    |---------|---------|
    |サブスクリプション    |事前に選択した内容に応じて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
    |リソース グループ  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。     |

5. 次の**インスタンス情報**を入力または選択します。

    |Setting  |値  |
    |---------|---------|
    |Name   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 - 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |   
    |リージョン     |このリリースでは、リソースのデプロイ先として、米国東部、東南アジア、西ヨーロッパを選択できます。 Azure Government の場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。 <br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|
    
    ![Data Box Gateway リソースの作成](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. **[Review + create]\(レビュー + 作成\)** を選択します。
 
7. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **作成** を選択します。

    ![Data Box Gateway リソースの詳細を確認する](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

リソースの作成には数分かかります。 リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

![Data Box Gateway リソースの詳細を確認する](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

次のチュートリアルに進んで、Data Box Gateway の仮想マシンをプロビジョニングする方法を学びます。 使用するホスト オペレーティング システムに応じて手順は異なります。

> [!div class="nextstepaction"]
> [Hyper-V で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-hyperv.md)

または

> [!div class="nextstepaction"]
> [VMware で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-vmware.md)


