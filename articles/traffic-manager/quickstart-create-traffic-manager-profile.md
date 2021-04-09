---
title: 'クイックスタート: アプリケーションの HA のためのプロファイルを作成する - Azure portal - Azure Traffic Manager'
description: このクイックスタート記事では、高可用性 Web アプリケーションを構築するための Traffic Manager プロファイルを、Azure portal を使用して作成する方法について説明します。
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 45489d3500a4a744f2aeb34dc21122d180797133
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101328"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>クイック スタート:Azure portal を使用した Traffic Manager プロファイルの作成

このクイック スタートでは、Web アプリケーションの高可用性を実現する Traffic Manager プロファイルの作成方法について説明します。

このクイック スタートでは、Web アプリケーションの 2 つのインスタンスについて見ていきます。 これらは、それぞれ別の Azure リージョンで実行されています。 皆さんは、[エンドポイントの優先度](traffic-manager-routing-methods.md#priority-traffic-routing-method)に基づいて Traffic Manager プロファイルを作成します。 このプロファイルにより、Web アプリケーションを実行しているプライマリ サイトにユーザー トラフィックを誘導します。 Traffic Manager では、Web アプリケーションが継続的に監視されます。 プライマリ サイトが利用できなくなった場合には、バックアップ サイトへの自動フェールオーバーが実行されます。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、2 つの異なる Azure リージョン ("*米国東部*" と "*西ヨーロッパ*") にデプロイされた、2 つの Web アプリケーション インスタンスが必要になります。 これらインスタンスは、それぞれ Traffic Manager のプライマリとフェールオーバーのエンドポイントとして機能します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 画面の左上で、 **[リソースの作成]** を選択します。 「**Web アプリ**」を検索し、 **[作成]** を選択します。

1. **[Web アプリの作成]** の **[基本]** タブで、次の値を入力または選択します。

    | 設定                 | 値 |
    | ---                     | --- |
    | サブスクリプション            | サブスクリプションを選択します。 |    
    | Resource group          | **[新規作成]** を選択し、テキスト ボックスに「*myResourceGroupTM1*」と入力します。|
    | 名前                    | Web アプリの一意の **名前** を入力します。 この例では *myWebAppEastUS* を使用します。 |
    | 発行                 | **[コード]** を選択します。 |
    | ランタイム スタック           | **[ASP.NET V4.7]** を選択します。 |
    | オペレーティング システム        | **[Windows]** を選択します。 |
    | リージョン                  | **[米国東部]** を選択します。 |
    | Windows プラン            | **[新規作成]** を選択し、テキスト ボックスに「*myAppServicePlanEastUS*」と入力します。 |
    | SKU とサイズ            | **[Standard S1 100 total ACU, 1.75-GB memory]\(Standard S1 合計 100 ACU、1.75 GB メモリ\)** を選択します。 |
   
1. **監視** タブを選択するか、または **Next:Monitoring\(次へ: 監視** を選択します。  **[監視]** の下で **[Application Insights]** > **[Application Insights を有効にする]** を **[いいえ]** に設定します。

1. **[確認と作成]** を選択します。

1. 設定を確認し、 **[作成]** を選択します。  Web アプリが正常にデプロイされると、既定の Web サイトが作成されます。

1. 手順 1. から手順 6. に従って、*myWebAppWestEurope* という名前でもう 1 つ Web アプリを作成します。 **リソース グループ** の名前は *myResourceGroupTM2*、**リージョン** は "*西ヨーロッパ*"、**App Service プラン** の名前は **myAppServicePlanWestEurope** としてください。 それ以外はすべて *myWebAppEastUS* と同じ設定を使用します。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

エンドポイントの優先度に基づいてユーザー トラフィックを誘導する Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]** を選択します。 次に、「**Traffic Manager プロファイル**」を検索し、 **[作成]** を選択します。
1. **[Traffic Manager プロファイルの作成]** ブレードで、次の設定を入力するか、選択します。

    | 設定 | 値 |
    | --------| ----- |
    | 名前 | Traffic Manager プロファイルの一意の名前を入力します。|
    | ルーティング方法 | **[優先順位]** を選択します。|
    | サブスクリプション | Traffic Manager プロファイルの適用先となるサブスクリプションを選択します。 |
    | Resource group | *myResourceGroupTM1* を選択します。|
    | 場所 |この設定は、リソース グループの場所を指定するものです。 グローバルにデプロイされる Traffic Manager プロファイルには影響しません。|

1. **［作成］** を選択します

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

すべてのユーザー トラフィックのルーティング先となるプライマリ エンドポイントとして、"*米国東部*" に Web サイトを追加します。 "*西ヨーロッパ*" に、フェールオーバー エンドポイントとして Web サイトを追加します。 プライマリ エンドポイントが使用できない場合、トラフィックは自動的にフェールオーバー エンドポイントにルーティングされます。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を入力します。
1. 検索結果からプロファイルを選択します。
1. **[Traffic Manager プロファイル]** の **[設定]** セクションで、**[エンドポイント]** を選択し、**[追加]** を選択します。

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Traffic Manager プロファイルのエンドポイントの設定":::

1. 次の設定を入力または選択します。

    | 設定 | 値 |
    | ------- | ------|
    | Type | **[Azure エンドポイント]** を選択します。 |
    | 名前 | 「*myPrimaryEndpoint*」と入力します。 |
    | ターゲット リソースの種類 | **[App Service]** を選択します。 |
    | ターゲット リソース | **[アプリ サービスの選択]**  >  **[米国東部]** を選択します。 |
    | 優先度 | **[1]** を選択します。 正常なトラフィックはすべてこのエンドポイントに送られます。 |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Traffic Manager プロファイルにエンドポイントを追加する画面のスクリーンショット":::
    
1. **[OK]** を選択します。
1. 2 つ目の Azure リージョンのフェールオーバー エンドポイントを作成するには、次の設定を使用して手順 3. と手順 4. を繰り返します。

    | 設定 | 値 |
    | ------- | ------|
    | Type | **[Azure エンドポイント]** を選択します。 |
    | 名前 | 「*myFailoverEndpoint*」と入力します。 |
    | ターゲット リソースの種類 | **[App Service]** を選択します。 |
    | ターゲット リソース | **[アプリ サービスの選択]**  >  **[西ヨーロッパ]** を選択します。 |
    | 優先度 | **[2]** を選択します。 プライマリ エンドポイントが正常でない場合、すべてのトラフィックはこのフェールオーバー エンドポイントに送られます。 |

1. **[OK]** を選択します。

2 つのエンドポイントが追加されると、それらが **Traffic Manager プロファイル** に表示されます。 監視の状態が **オンライン** になったことに注目してください。

## <a name="test-traffic-manager-profile"></a>Traffic Manager プロファイルのテスト

このセクションでは、Traffic Manager プロファイルのドメイン名を確認します。 また、プライマリ エンドポイントを使用できないように構成します。 最後に、Web アプリがまだ使用できることを確認します。 これは、Traffic Manager によってトラフィックがフェールオーバー エンドポイントへと送信されるためです。

### <a name="check-the-dns-name"></a>DNS 名を確認する

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索します。
1. Traffic Manager プロファイルを選択します。 **[概要]** が表示されます。
1. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Traffic Manager の DNS 名の場所を示すスクリーンショット":::

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認

1. Web ブラウザーで、Traffic Manager プロファイルの DNS 名を入力して、Web アプリの既定の Web サイトを確認します。

    > [!NOTE]
    > このクイック スタート シナリオでは、すべての要求がプライマリ エンドポイントにルーティングされます。 これは **優先度 1** に設定されています。

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Traffic Manager プロファイルの可用性を確認する Web ページのスクリーン ショット":::

1. Traffic Manager のフェールオーバー動作を確認するために、次の手順でプライマリ サイトを無効にします。
    1. [Traffic Manager プロファイル] ページで、**[概要]** セクションから **[myPrimaryEndpoint]** を選択します。
    1. *[myPrimaryEndpoint]* で **[無効]** > **[保存]** を選択します。
    1. **[myPrimaryEndpoint]** を閉じます。 状態が **[無効]** になったことを確認してください。
1. 新しい Web ブラウザー セッションで Web サイトを表示するために、Traffic Manager プロファイルの DNS 名を前の手順からコピーします。
1. Web アプリがまだ使用できることを確認します。

プライマリ エンドポイントが使用できないため、フェールオーバー エンドポイントにルーティングされました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完了したら、リソース グループ、Web アプリケーション、およびすべての関連リソースを削除します。 これを行うには、ダッシュ ボードから個々 の項目を選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Traffic Manager プロファイルを作成しました。 これにより、ユーザー トラフィックを高可用性 Web アプリケーション用にルーティングすることができます。 トラフィックのルーティングについて理解を深めるために、引き続き Traffic Manager のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [Traffic Manager のチュートリアル](tutorial-traffic-manager-improve-website-response.md)
