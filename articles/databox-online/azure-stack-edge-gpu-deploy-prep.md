---
title: 'チュートリアル: Azure Stack Edge Pro GPU を配置するために Azure portal、データセンター環境を準備する | Microsoft Docs'
description: Azure Stack Edge Pro GPU の配置に関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: b108e757ed9fe9ab7038cae4240f0f749ac19675
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036046"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>チュートリアル:GPU 搭載の Azure Stack Edge Pro の配置を準備する 

これのチュートリアルは、GPU 搭載の Azure Stack Edge Pro を完全に配置するために必要な一連のデプロイ チュートリアルの第 1 部です。 このチュートリアルでは、Azure Stack Edge リソースを配置するために Azure portal を準備する方法を説明します。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

### <a name="get-started"></a>はじめに

Azure Stack Edge Pro の配置では、最初に環境を準備する必要があります。 環境を準備したら、必要な手順に従い、必要に応じて、デバイスを完全に配置するためのオプションのステップと手順を実行します。 配置の詳細な手順では、どのようなときにこれらの必須手順と省略可能な手順を行う必要があるかが示されています。

| 手順 | 説明 |
| --- | --- |
| **準備** |後で行う配置のための準備として、これらの手順を完了する必要があります。 |
| **[配置の構成のチェック リスト](#deployment-configuration-checklist)** |このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。 |
| **[デプロイの前提条件](#prerequisites)** |これらの前提条件を使用して、配置に対する環境の準備が完了していることを確認します。 |
|  | |
|**配置のチュートリアル** |これらのチュートリアルは、Azure Stack Edge Pro デバイスを運用環境に配置するために必要です。 |
|**[1.Azure Stack Edge Pro のために Azure portal を準備する](azure-stack-edge-gpu-deploy-prep.md)** |Azure Stack Box Edge 物理デバイスを設置する前に、Azure Stack Edge リソースを作成して構成します。 |
|**[2.](azure-stack-edge-gpu-deploy-install.md)Azure Stack Edge Pro を設置する**|Azure Stack Edge Pro 物理デバイスの開梱、ラックへの取り付け、およびケーブル接続を行います。  |
|**[3.Azure Stack Edge Pro に接続する](azure-stack-edge-gpu-deploy-connect.md)** |デバイスを設置したら、デバイスのローカル Web UI に接続します。  |
|**[4.Azure Stack Edge Pro のネットワーク設定を構成する](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |デバイスに対するコンピューティング ネットワークや Web プロキシの設定など、ネットワークを構成します。   |
|**[5.Azure Stack Edge Pro のデバイス設定を構成する](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |デバイス名と DNS ドメインを割り当てて、更新サーバーとデバイスの時刻を構成します。 |
|**[6.Azure Stack Edge Pro のセキュリティ設定を構成する](azure-stack-edge-gpu-deploy-configure-certificates.md)** |デバイス用に証明書を構成します。 デバイスで生成された証明書を使用するか、独自の証明書を使用します。   |
|**[7.Azure Stack Edge Pro をアクティブにする](azure-stack-edge-gpu-deploy-activate.md)** |サービスからのアクティブ化キーを使用して、デバイスをアクティブ化します。 デバイスは、SMB または NFS 共有を設定するか、REST 経由で接続できる状態になります。 |
|**[8.コンピューティングを構成する](azure-stack-edge-gpu-deploy-configure-compute.md)** |デバイスでコンピューティング ロールを構成します。 Kubernetes クラスターも作成されます。 |
|**[9A. Edge 共有でデータを転送する](azure-stack-edge-j-series-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |
|**[9B. Edge ストレージ アカウントでデータを転送する](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |ストレージ アカウントを追加し、REST API を使用して Blob Storage に接続します。 |


これで、Azure Stack Edge Pro デバイスのソフトウェア構成に関する情報の収集を開始できます。

## <a name="deployment-configuration-checklist"></a>デプロイの構成チェック リスト

デバイスを配置する前に、Azure Stack Edge Pro デバイスにソフトウェアを構成するための情報を収集する必要があります。 事前にこの情報の一部を準備することで、環境内にデバイスを配置するプロセスを効率化できます。 [Azure Stack Edge Pro 配置構成チェック リスト](azure-stack-edge-gpu-deploy-checklist.md)を使用して、デバイスを配置するときの構成の詳細をメモします。


## <a name="prerequisites"></a>前提条件

次に示すのは、Azure Stack Edge リソース、Azure Stack Edge Pro デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

開始する前に次の点を確認します。

- ご利用の Microsoft Azure サブスクリプションで Azure Stack Edge リソースが有効になっていること。 [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。 従量課金制サブスクリプションはサポートされていません。 所有している Azure サブスクリプションの種類を特定するには、「[Azure プランとは](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer)」を参照してください。
- Azure Stack Edge Pro/Data Box Gateway、IoT Hub、および Azure Storage の各リソースに対して、リソース グループ レベルで所有者または共同作成者のアクセス許可を持っていること。

    - Azure Stack Edge/Data Box Gateway のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 
    - また、`Microsoft.DataBoxEdge` および `MicrosoftKeyVault` リソース プロバイダーが登録されていることを確認する必要もあります。 どのような IoT Hub リソースを作成する場合でも、`Microsoft.Devices` プロバイダーを登録する必要があります。 
        - リソース プロバイダーを登録するには、Azure portal で、 **[ホーム] > [サブスクリプション] > お使いのサブスクリプション > [リソース プロバイダー]** と移動します。 
        - 特定のリソース プロバイダー (`Microsoft.DataBoxEdge` など) を検索し、そのリソース プロバイダーを登録します。 
    - Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
- アクティブ化キーの生成や、ストレージ アカウントを使用する共有の作成などの資格情報の操作のために、Azure Active Directory Graph API に対する管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。


### <a name="for-the-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスの前提条件

物理デバイスを配置する前に次の点を確認します。

- 配送パッケージに同梱されている安全性情報を確認済みであること。
- デバイスをラック マウントするため、データセンターの標準 19 インチ ラックに空いている 1U スロットが 1 つあること。
- デバイスを安全に置くことができる、平らで安定した水平の作業面が用意されていること。
- デバイスをセットアップする予定の場所に、独立した電源からの標準 AC 電源、または無停電電源装置 (UPS) を備えたラック配電ユニット (PDU) があること。
- 物理デバイスにアクセスできること。


### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

- Azure Stack Edge Pro デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、「[Azure Stack Edge Pro のシステム要件](azure-stack-edge-system-requirements.md)」を参照してください。

- Azure Stack Edge Pro の通常の動作条件下で、以下が存在すること。

    - デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
    - ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-a-new-resource"></a>新しいリソースを作成

物理デバイスを管理する既存の Azure Stack Edge リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure Stack Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用し、Azure portal にサインインします。URL は [https://portal.azure.com](https://portal.azure.com) です。

2. 左側のウィンドウで、 **[+ リソースの作成]** を選択します。 **[Azure Stack Edge/Data Box Gateway]** を検索して選択します。 **［作成］** を選択します 

3. Azure Stack Edge Pro デバイスに使用するサブスクリプションを選択します。 この物理デバイスを発送する先の国を選択します。 **[デバイスの表示]** を選択します。

    ![リソースを作成する 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. デバイスの種類を選択します。 **[Azure Stack Edge Pro]** で、 **[GPU 搭載の Azure Stack Edge Pro]** を選択し、 **[選択]** を選択します。 問題が発生した場合、またはデバイスの種類を選択できない場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

    ![リソースを作成する 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. ビジネス ニーズに基づいて、Nvidia 製の 1 個または 2 個のグラフィカル プロセシング ユニット (GPU) を使用する Azure Stack Edge Pro を選択できます。 

    ![リソースを作成する 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. **[基本]** タブで、次の **プロジェクト情報** を入力または選択します。
    
    |設定  |値  |
    |---------|---------|
    |サブスクリプション    |サブスクリプションは、前の選択に基づいて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
    |Resource group  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。     |

7. 次の **インスタンス情報** を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |名前   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 から 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |リージョン     |Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。<br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|

    ![リソースを作成する 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. **配送先住所** を選択します。

    - 既にデバイスがある場合は、 **[I already have a device]\(既にデバイスを持っています\)** のコンボ ボックスを選択します。

        ![リソースを作成する 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - 新しいデバイスを注文する場合は、連絡先名、会社、デバイスの配送先住所、連絡先情報を入力します。

        ![リソースを作成する 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. **タグ** を選択します。 必要に応じて、リソースを分類し、請求を統合するためのタグを指定します。 **確認と作成** をクリックします。

10. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **[I have reviewed the privacy terms]\(プライバシー条件を確認しました\)** のコンボ ボックスを選択します。

    ![リソースを作成する 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    また、リソースの作成時に、クラウド サービスに対して認証できるようにするマネージド サービス ID (MSI) が有効になっていることも通知されます。 この ID は、リソースが存在する限り存在します。

11. **［作成］** を選択します

    リソースの作成には数分かかります。 また、Azure Stack Edge デバイスが Azure のリソースプロバイダーと通信できるようにする MSI も作成されます。

    リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

    ![Azure Stack Edge Pro リソースに移動する](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Microsoft は受け取った注文を確認し、発送の詳細と共にお客様に (メールで) 連絡します。

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> 一度に複数の注文を作成したい場合や既存の注文を複製したい場合は、[「Azure サンプル」のスクリプト](https://github.com/Azure-Samples/azure-stack-edge-order)をご利用ください。 詳細については、Readme ファイルを参照してください。

注文処理の間に問題が発生した場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

必要に応じて、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure Stack Edge リソースを作成するには、Azure CLI で次のコマンドを実行します。

1. [az group create](/cli/azure/group#az_group_create) コマンドを使用してリソース グループを作成するか、既存のリソース グループを使用します。

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. デバイスを作成するには、[az databoxedge device create](/cli/azure/databoxedge/device#az_databoxedge_device_create) コマンドを使用します。

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   デバイスをデプロイする地理的リージョンに最も近い場所を選択します。 このリージョンには、デバイス管理用のメタデータのみが格納されます。 実際のデータは、任意のストレージ アカウントに格納できます。

   Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。

1. 注文を作成するには、[az databoxedge order create](/cli/azure/databoxedge/order#az_databoxedge_order_create) コマンドを実行します。

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

リソースの作成には数分かかります。 [Az databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) コマンドを実行して、注文を確認します。

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

注文が行われたら、Microsoft は注文を確認し、発送の詳細が記載されたメールをお客様に送信します。

---

## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Azure Stack Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Azure Stack Edge Pro デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 自分が作成したリソースを選択し、 **[概要]** を選択します。

2. 右ペインで、Azure キー コンテナーの名前を入力するか、既定の名前をそのまま使用します。 キー コンテナーの名前は 3 から 24 文字で指定できます。

   デバイスでアクティブ化される Azure Stack Edge リソースごとに、キー コンテナーが作成されます。 キー コンテナーを使用すると、シークレットを格納してアクセスできます。たとえば、サービスのチャネル整合性キー (CIK) をキー コンテナーに格納できます。 

   キー コンテナーの名前を指定したら、 **[キーの生成]** を選択して、アクティブ化キーを作成します。 

   ![アクティブ化キーの取得](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   キー コンテナーとアクティブ化キーが作成されるまで数分待ちます。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge Pro に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

次のチュートリアルに進んで、Azure Stack Edge Pro を設置する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro を設置する](./azure-stack-edge-gpu-deploy-install.md)