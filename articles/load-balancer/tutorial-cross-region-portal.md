---
title: チュートリアル:Azure portal を使用してリージョン間ロード バランサーを作成する
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure portal を使用してリージョン間 Azure ロード バランサーのデプロイを開始します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: c16123fae63b89eff57b5c91864d9a947e01b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576919"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>チュートリアル:Azure portal を使用してリージョン間 Azure ロード バランサーを作成する

リージョン間ロード バランサーを使うと、サービスが複数の Azure リージョンにわたってグローバルに利用可能になります。 1 つのリージョンで障害が発生した場合、トラフィックは次の最も近い正常なリージョンのロード バランサーにルーティングされます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リージョン間ロード バランサーを作成します。
> * 2 つのリージョン ロード バランサーを含むバックエンド プールを作成します。
> * ロード バランサー規則を作成します。
> * ロード バランサーをテストします。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

> [!IMPORTANT]
> リージョンをまたがる Azure Load Balancer は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- 2 つの異なる Azure リージョンにデプロイされたバックエンド プールを備えた 2 つの **Standard** SKU Azure ロード バランサー。
    - バックエンド プール用にリージョンの Standard ロード バランサーと仮想マシンを作成する方法については、「[クイックスタート: Azure portal を使用して、VM の負荷分散を行うパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-portal.md)」を参照してください。
        - 各リージョンのロード バランサー、仮想マシン、その他のリソースの名前の末尾に、**R1** と **-R2** を追加します。 

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure portal に[サインイン](https://portal.azure.com)します。

## <a name="create-cross-region-load-balancer"></a>リージョン間ロード バランサーを作成する

このセクションでは、リージョン間ロード バランサーとパブリック IP アドレスを作成します。

1. **[リソースの作成]** を選択します。 
2. 検索ボックスに「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。
3. **[ロード バランサー]** ページで、 **[作成]** を選択します。
4. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。 

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | サブスクリプション               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択し、テキスト ボックスに「**CreateCRLBTutorial-rg**」と入力します。|
    | Name                   | 「**myLoadBalancer-CR**」と入力します                                   |
    | リージョン         | **[(米国) 米国西部]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | 規定値である **[標準]** のままにします。 |
    | レベル           | **[グローバル]** を選択します |
    | パブリック IP アドレス | **[新規作成]** を選択します。|
    | パブリック IP アドレス名 | テキスト ボックスに「**myPublicIP-CR**」と入力します。|
    | ルーティング設定| **[Microsoft ネットワーク]** を選択します。 </br> ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](../virtual-network/routing-preference-overview.md)」を参照してください。 |

    > [!NOTE]
    > リージョン間ロード バランサーは、次のホーム リージョンにのみデプロイできます。**米国東部 2、米国西部、西ヨーロッパ、東南アジア、米国中部、北ヨーロッパ、東アジア**。 詳細については、**https://aka.ms/homeregionforglb** をご覧ください。


3. 残りの設定は既定値をそのまま使用し、 **[確認と作成]** を選択します。

4. **[確認および作成]** タブで、 **[作成]** を選択します。   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="リージョン間ロード バランサーを作成する" border="true":::

## <a name="create-backend-pool"></a>バックエンド プールの作成

このセクションでは、リージョン間ロード バランサーのバックエンド プールに 2 つのリージョン Standard ロード バランサーを追加します。

> [!IMPORTANT]
> これらの手順を完了するには、2 つのリージョン ロード バランサーとバックエンド プールを自分のサブスクリプションにデプロイしておく必要があります。  詳細については、「 **[クイックスタート: Azure portal を使用して、VM の負荷分散を行うパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-portal.md)** 」を参照してください。

バックエンド アドレス プール **myBackendPool-CR** を作成し、リージョン Standard ロード バランサーを含めます。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer-CR]** を選択します。

2. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順に選択します。

3. **[バックエンド プールの追加]** ページで、名前として「**myBackendPool-CR**」と入力します。

4. **[追加]** を選択します。

4. **[myBackendPool-CR]** を選択します。

5. **[ロード バランサー]** で、 **[ロード バランサー]** の下にあるプルダウン ボックスを選択します。

5. **[myLoadBalancer-R1]** を選択するか、リージョン 1 のロード バランサーの名前を選択します。

6. **[フロントエンド IP 構成]** の下のプルダウン ボックスを選択します。 **[LoadBalancerFrontEnd]** を選択します。

7. 手順 4. から 6. を繰り返して、**myLoadBalancer-R2** を追加します。

8. **[追加]** を選択します。

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="バックエンド プールにリージョン ロード バランサーを追加する" border="true":::

## <a name="create-a-health-probe"></a>正常性プローブの作成

このセクションでは、負荷分散規則を作成するために正常性プローブを作成します。

* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* 間隔は **5** 秒にします。
* 異常のしきい値は **2** つのエラーにします。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer-CR]** を選択します。

2. **[設定]** の下で **[正常性プローブ]** を選択します。

3. これらの値を使用して、正常性プローブを構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「**myHealthProbe**」と入力します。 |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「**80**」と入力します。 |
    | Interval | 「**5**」と入力します。 |
    | 異常のしきい値 | 「**2**」と入力します。 |

4. **[OK]** を選択します。

    > [!NOTE]
    > リージョン間ロード バランサーには、組み込みの正常性プローブがあります。 このプローブは、負荷分散規則の作成を機能させるためのプレースホルダーです。  詳細については、 **[リージョン間ロード バランサーの制限](cross-region-overview.md#limitations)** に関するセクションを参照してください。

## <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

このセクションでは、ロード バランサー規則を作成します。

* 「**myHTTPRule**」という名前を付けます。
* フロントエンドには、「**LoadBalancerFrontEnd**」という名前を付けます。
* **ポート 80** でリッスンします。
* 負荷分散されたトラフィックが **myBackendPool-CR** という名前のバックエンドの **ポート 80** に送られるようにします。

    > [!NOTE]
    > フロントエンド ポートは、バックエンド ポートと、バックエンド プール内のリージョン ロード バランサーのフロントエンド ポートと一致している必要があります。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer-CR]** を選択します。

2. **[設定]** で、 **[負荷分散規則]** 、 **[追加]** の順に選択します。

3. 負荷分散規則の構成には、以下の値を使用します。
    
    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「**myHTTPRule**」と入力します。 |
    | IP バージョン | **[IPv4]** を選択します |
    | フロントエンド IP アドレス | **[LoadBalancerFrontEnd]** を選択します |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「**80**」と入力します。|
    | バックエンド ポート | 「**80**」と入力します。 |
    | バックエンド プール | **[myBackendPool]** を選択します。|
    | 正常性プローブ | **[myHealthProbe]** を選択します。 |
    | アイドル タイムアウト (分) | スライダーを **15** に移動します。 |
    | TCP リセット | **[Enabled]** を選択します。 |

4. 残りの部分は既定値のままにし、次に **[OK]** を選択します。

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="ロード バランサー規則の作成" border="true":::

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

このセクションでは、リージョン間ロード バランサーをテストします。 Web ブラウザーで、パブリック IP アドレスに接続します。  いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止し、フェールオーバーを観察します。

1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、 **[myPublicIP-CR]** を選択します。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="ロード バランサーをテストする" border="true":::

3. いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止します。

4. Web ブラウザーを最新の状態に更新し、別のリージョン ロード バランサーへの接続のフェールオーバーを観察します。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="フェールオーバー後のロード バランサーをテストする" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 

これを行うには、これらのリソースを含むリソース グループ (**CreateCRLBTutorial-rg**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

* リージョン間ロード バランサーを作成しました。
* リージョン間ロード バランサーのバックエンド プールにリージョン ロード バランサーを追加しました。
* 負荷分散規則を作成しました。
* ロード バランサーをテストしました。

リージョン間ロード バランサーの詳細については、次を参照してください。
> [!div class="nextstepaction"]
> [リージョン間ロード バランサー (プレビュー)](cross-region-overview.md)
