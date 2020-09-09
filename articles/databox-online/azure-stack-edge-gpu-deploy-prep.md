---
title: 'チュートリアル: Azure Stack Edge GPU を配置するために Azure portal、データセンター環境を準備する | Microsoft Docs'
description: Azure Stack Edge GPU の配置に関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c02af8983489651d8b5b83d5d3107a534c51f067
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254680"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-with-gpu"></a>チュートリアル:GPU 搭載 Azure Stack Edge の配置を準備する 

これは、GPU 搭載 Azure Stack Edge を完全に配置するために必要な一連の配置チュートリアルの中で最初のものです。 このチュートリアルでは、Azure Stack Edge リソースを配置するために Azure portal を準備する方法を説明します。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

### <a name="get-started"></a>はじめに

Azure Stack Edge の配置では、最初に環境を準備する必要があります。 環境を準備したら、必要な手順に従い、必要に応じて、デバイスを完全に配置するためのオプションのステップと手順を実行します。 配置の詳細な手順では、どのようなときにこれらの必須手順と省略可能な手順を行う必要があるかが示されています。

| 手順 | 説明 |
| --- | --- |
| **準備** |後で行う配置のための準備として、これらの手順を完了する必要があります。 |
| **[配置の構成のチェック リスト](#deployment-configuration-checklist)** |このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。 |
| **[デプロイの前提条件](#prerequisites)** |これらの前提条件を使用して、デプロイに対する環境の準備が完了していることを確認します。 |
|  | |
|**配置のチュートリアル** |これらのチュートリアルは、Azure Stack Edge デバイスを運用環境に配置するために必要です。 |
|**[1.Azure Stack Edge のために Azure portal を準備する](azure-stack-edge-gpu-deploy-prep.md)** |Azure Stack Box Edge 物理デバイスを設置する前に、Azure Stack Edge リソースを作成して構成します。 |
|**[2.Azure Stack Edge を設置する](azure-stack-edge-gpu-deploy-install.md)**|Azure Stack Edge 物理デバイスの開梱、ラックへの取り付け、ケーブル接続を行います。  |
|**[3.Azure Stack Edge に接続する](azure-stack-edge-gpu-deploy-connect.md)** |デバイスを設置したら、デバイスのローカル Web UI に接続します。  |
|**[4.Azure Stack Edge 用にネットワークの設定を構成する](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |デバイスに対するコンピューティング ネットワークや Web プロキシの設定など、ネットワークを構成します。   |
|**[5.Azure Stack Edge 用にデバイスの設定を構成する](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |デバイス名と DNS ドメインを割り当てて、更新サーバーとデバイスの時刻を構成します。 |
|**[6.Azure Stack Edge 用にセキュリティの設定を構成する](azure-stack-edge-gpu-deploy-configure-certificates.md)** |デバイス用に証明書を構成します。 デバイスで生成された証明書を使用するか、独自の証明書を使用します。   |
|**[7.Azure Stack Edge をアクティブにする](azure-stack-edge-gpu-deploy-activate.md)** |サービスからのアクティブ化キーを使用して、デバイスをアクティブ化します。 デバイスは、SMB または NFS 共有を設定するか、REST 経由で接続できる状態になります。 |
|**[8.コンピューティングを構成する](azure-stack-edge-gpu-deploy-configure-compute.md)** |デバイスでコンピューティング ロールを構成します。 Kubernetes クラスターも作成します。 |
|**[9A. Edge 共有でデータを転送する](azure-stack-edge-j-series-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |
|**[9B. Edge ストレージ アカウントでデータを転送する](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |ストレージ アカウントを追加し、REST API を使用して Blob Storage に接続します。 |


Azure Stack Edge デバイスのソフトウェア構成に関する情報の収集を開始できるようになります。

## <a name="deployment-configuration-checklist"></a>デプロイの構成チェック リスト

デバイスを配置する前に、Azure Stack Edge デバイスにソフトウェアを構成するための情報を収集する必要があります。 事前にこの情報の一部を準備することで、環境内にデバイスを配置するプロセスを効率化できます。 [Azure Stack Edge 配置構成チェック リスト](azure-stack-edge-gpu-deploy-checklist.md)を使用して、デバイスをデプロイするときの構成の詳細を記録します。


## <a name="prerequisites"></a>前提条件

次に示すのは、Azure Stack Edge リソース、Azure Stack Edge デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

開始する前に次の点を確認します。

- ご利用の Microsoft Azure サブスクリプションで Azure Stack Edge リソースが有効になっていること。 [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。 従量課金制のサブスクリプションには対応していません。
- Azure Stack Edge と Data Box Gateway、IoT Hub、および Azure Storage のリソースに対してリソース グループ レベルで所有者または共同作成者のアクセス許可を持っていること。

    - Azure Stack Edge/Data Box Gateway のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 また、`Microsoft.DataBoxEdge` プロバイダーが登録されていることも確認する必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - IoT Hub リソースを作成するには、Microsoft.Devices プロバイダーが登録されていることを確認します。 登録方法の詳細については、「[リソース プロバイダーの登録](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
    - Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
- Azure Active Directory Graph API に対して管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。
- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-azure-stack-edge-device"></a>Azure Stack Edge デバイスの前提条件

物理デバイスを配置する前に次の点を確認します。

- 配送パッケージに同梱されている安全性情報を確認済みであること。
- デバイスをラック マウントするため、データセンターの標準 19 インチ ラックに空いている 1U スロットが 1 つあること。
- デバイスを安全に置くことができる、平らで安定した水平の作業面が用意されていること。
- デバイスをセットアップする予定の場所に、独立した電源からの標準 AC 電源、または無停電電源装置 (UPS) を備えたラック配電ユニット (PDU) があること。
- 物理デバイスにアクセスできること。


### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

- Azure Stack Edge デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳しくは、「[Azure Stack Edge のシステム要件](azure-stack-edge-system-requirements.md)」を参照してください。

- Azure Stack Edge の通常の動作条件下における帯域幅は次のとおりです。

    - デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
    - ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-a-new-resource"></a>新しいリソースを作成

物理デバイスを管理する既存の Azure Stack Edge リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Azure Stack Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用し、Azure portal にサインインします。URL は [https://portal.azure.com](https://portal.azure.com) です。

2. 左側のウィンドウで、 **[+ リソースの作成]** を選択します。 **[Azure Stack Edge/Data Box Gateway]** を検索して選択します。 **［作成］** を選択します 問題が発生する場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

3. Azure Stack Edge デバイスに使用するサブスクリプションを選択します。 この物理デバイスを発送する先の国を選択します。 **[デバイスの表示]** を選択します。

    ![リソースを作成する 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. デバイスの種類を選択します。 **[Azure Stack Edge Commercial]** で、 **[GPU 搭載の Azure Stack Edge]** を選択し、 **[サインアップ]** を選択します。 

    ![リソースを作成する 2](media/azure-stack-edge-gpu-deploy-prep/create-resource-2.png)

5. 短いフォームが表示されます。 フォームに入力して、 **[送信]** を選択します。 Microsoft によってサブスクリプションが有効にされます。

    ![リソースを作成する 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. サブスクリプションを有効にした後、リソースの作成を続けることができます。 **[デバイスの種類の選択]** ブレードで、 **[選択]** を選択します。

    ![リソースを作成する 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)


6. **[基本]** タブで、次の**プロジェクト情報**を入力または選択します。
    
    |設定  |値  |
    |---------|---------|
    |サブスクリプション    |事前に選択した内容に応じて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
    |Resource group  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。     |

4. 次の**インスタンス情報**を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |名前   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 - 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |リージョン     |Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。<br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|

    ![リソースを作成する 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


5. **配送先住所** を選択します。

    - 既にデバイスがある場合は、 **[I have a Azure Stack Edge device]\(Azure Stack Edge デバイスを持っています\)** のコンボ ボックスを選択します。

        ![リソースを作成する 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - 新しいデバイスを注文する場合は、連絡先名、会社、デバイスの配送先住所、連絡先情報を入力します。

        ![リソースを作成する 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

6. **確認と作成** をクリックします。

7. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **[I have reviewed the privacy terms]\(プライバシー条件を確認しました\)** のコンボ ボックスを選択します。

    ![リソースを作成する 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

8. **［作成］** を選択します

リソースの作成には数分かかります。 リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

![Azure Stack Edge リソースに移動する](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Microsoft は受け取った注文を確認し、発送の詳細と共にお客様に (メールで) 連絡します。

![Azure Stack Edge の注文確認通知](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

注文処理の間に問題が発生した場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Azure Stack Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Azure Stack Edge デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースを選択します。 **[概要]** を選択し、 **[デバイスの設定]** を選択します。

    ![[デバイスの設定] を選択する](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. **[Activate]\(アクティブ化\)** タイルの **[キーの生成]** を選択してアクティブ化キーを作成します。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

次のチュートリアルに進み、Azure Stack Edge を設置する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge を設置する](./azure-stack-edge-gpu-deploy-install.md)



