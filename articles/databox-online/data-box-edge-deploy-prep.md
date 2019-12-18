---
title: 'チュートリアル: Azure Data Box Edge をデプロイするために Azure portal、データセンター環境を準備する | Microsoft Docs'
description: Edge のデプロイに関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 364ceabc3c9e7a577bd126b81bcd5256d947bbe3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476781"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>チュートリアル:Edge のデプロイを準備する  

これは、Edge を完全にデプロイするために必要な一連のデプロイ チュートリアルの中で最初のものです。 このチュートリアルでは、Edge リソースをデプロイするために Azure portal を準備する方法を説明します。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="get-started"></a>作業開始

Edge をデプロイするには、以下の順序どおりにチュートリアルを参照してください。

| **#** | **手順** | **概要** |
| --- | --- | --- | 
| 1. |**[Edge のために Azure portal を準備する](data-box-edge-deploy-prep.md)** |Edge 物理デバイスをインストールする前に、Edge リソースを作成して構成します。 |
| 2. |**[Edge をインストールする](data-box-edge-deploy-install.md)**|Edge 物理デバイスの開梱、ラックへの取り付け、ケーブル接続を行います。  |
| 3. |**[Edge を接続、設定、アクティブ化する](data-box-edge-deploy-connect-setup-activate.md)** |ローカル Web UI に接続して、デバイス設定を完了し、デバイスをアクティブ化します。 このデバイスは、SMB または NFS 共有を設定する準備ができています。  |
| 4. |**[Edge を使用してデータを転送する](data-box-edge-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |
| 5. |**[Edge を使用してデータを変換する](data-box-edge-deploy-configure-compute.md)** |データを Azure に移動するときにそれを変換するように、デバイス上のコンピューティング モジュールを構成します。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="prerequisites"></a>前提条件

次に示すのは、Edge リソース、Edge デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-data-box-edge-resource"></a>Edge リソースに対して

開始する前に次の点を確認します。

- Microsoft Azure サブスクリプションで Edge リソースが有効になっていること。 従量課金制のサブスクリプションには対応していません。
- Data Box Edge/Data Box Gateway、IoT Hub、および Azure Storage のリソースに対してリソース グループ レベルの所有者または共同作成者のアクセス許可を持っていること。

    - Data Box Edge/Data Box Gateway のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 また、`Microsoft.DataBoxEdge` プロバイダーが登録されていることも確認する必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - IoT Hub リソースを作成するには、Microsoft.Devices プロバイダーが登録されていることを確認します。 登録方法の詳細については、「[リソース プロバイダーの登録](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
- Azure Active Directory Graph API に対して管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。
- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-data-box-edge-device"></a>Edge デバイスに対して

物理デバイスを配置する前に次の点を確認します。

- 配送パッケージに同梱されている安全性情報を確認済みであること。
- デバイスをラック マウントするため、データセンターの標準 19 インチ ラックに空いている 1U スロットが 1 つあること。
- デバイスを安全に置くことができる、平らで安定した水平の作業面が用意されていること。
- デバイスをセットアップする予定の場所に、独立した電源からの標準 AC 電源、または無停電電源装置 (UPS) を備えたラック配電ユニット (PDU) があること。
- 物理デバイスにアクセスできること。


### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

- Edge デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳しくは、[Edge のシステム要件](data-box-edge-system-requirements.md)に関する記事をご覧ください。

- Data Box Edge の通常の動作条件下における帯域幅は次のとおりです。

    - デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
    - ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-a-new-resource"></a>新しいリソースを作成

物理デバイスを管理する既存の Edge リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure の資格情報を使用して、次のサイトにサインインします。 
    
    - Azure portal (URL: [https://portal.azure.com](https://portal.azure.com))。
    - または、Azure Government ポータル (URL: [https://portal.azure.us](https://portal.azure.us))。 詳細については、[ポータルを使用して Azure Government に接続する](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)方法に関するページを参照してください。

2. 左側のウィンドウで、 **[+ リソースの作成]** を選択します。 **[Data Box Edge / Data Box Gateway]** を検索します。 **[Data Box Edge / Data Box Gateway]** を選択します。 **作成** を選択します。
3. Data Box Edge に使用するサブスクリプションを選択します。 Data Box Edge リソースをデプロイするリージョンを選択します。 このリリースでは、米国東部、東南アジア、西ヨーロッパを選択できます。 

    デバイスをデプロイする地理的リージョンに最も近い場所を選択します。 このリージョンには、デバイス管理用のメタデータのみが格納されます。 実際のデータは、任意のストレージ アカウントに格納できます。 
    
    **Edge** オプションで、 **[作成]** を選択します。

    ![Edge サービスを検索する](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. **[基本]** タブで、次の**プロジェクト情報**を入力または選択します。
    
    |Setting  |値  |
    |---------|---------|
    |サブスクリプション    |事前に選択した内容に応じて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
    |リソース グループ  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。     |

4. 次の**インスタンス情報**を入力または選択します。

    |Setting  |値  |
    |---------|---------|
    |Name   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 - 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |リージョン     |このリリースでは、リソースのデプロイ先として、米国東部、東南アジア、西ヨーロッパを選択できます。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。<br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|

    ![プロジェクトとインスタンスの詳細](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. **[次へ:配送先住所]** を選択します。

    - 既にデバイスがある場合は、 **[I have a Data Box Edge device]\(Data Box Edge デバイスを持っています\)** のコンボ ボックスを選択します。
    - 新しいデバイスを注文する場合は、連絡先名、会社、デバイスの配送先住所、連絡先情報を入力します。

    ![新しいデバイスの配送先住所](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. **[次へ:確認と作成]** をクリックします。

7. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **[I have reviewed the privacy terms]\(プライバシー条件を確認しました\)** のコンボ ボックスを選択します。

    ![Data Box Edge リソースの詳細とプライバシー条件を確認する](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. **作成** を選択します。

リソースの作成には数分かかります。 リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

![Data Box Edge リソースに移動する](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Microsoft は受け取った注文を確認し、発送の詳細と共にお客様に (メールで) 連絡します。

![Data Box Edge の注文確認通知](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Edge デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースを選択します。 **[概要]** を選択し、 **[デバイスの設定]** を選択します。

    ![[デバイスの設定] を選択する](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. **[Activate]\(アクティブ化\)** タイルの **[キーの生成]** を選択してアクティブ化キーを作成します。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Edge に関する以下のトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

次のチュートリアルに進み、Edge をインストールする方法を学習してください。

> [!div class="nextstepaction"]
> [Edge をインストールする](./data-box-edge-deploy-install.md)



