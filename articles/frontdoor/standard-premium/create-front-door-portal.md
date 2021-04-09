---
title: 'クイックスタート : Azure Front Door Standard/Premium プロファイルを作成する - Azure portal'
description: このクイックスタートでは、Azure portal で Azure Front Door Standard/Premium Service を使用してグローバル Web アプリケーションの高可用性とハイパフォーマンスを実現する方法について示します。
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 18956948a32a79c1435bf4fc1554b09480c9010c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100719"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>クイックスタート : Azure Front Door Standard/Premium プロファイルを作成する - Azure portal

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [Azure Front Door ドキュメント](../front-door-overview.md)を参照してください。

このクイックスタートでは、Azure portal を使用して Azure Front Door Standard/Premium プロファイルを作成する方法を説明します。 Azure Front Door Standard/Premium プロファイルは、基本構成による "*簡易作成*" を使用して作成することも、より詳細な構成による "*カスタム作成*" を使用して作成することもできます。 "*カスタム作成*" では、2 つの Web アプリをデプロイします。 次に、この 2 つの Web アプリを配信元として使用して、Azure Front Door Standard/Premium プロファイルを作成します。 次に、Azure Front Door Standard/Premium フロントエンド ホスト名を使用して、Web アプリへの接続を確認します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-front-door-profile---quick-create"></a>Front Door プロファイルを作成する - 簡易作成

1. [Azure portal](https://portal.azure.com) にサインインします。

1. ホーム ページまたは Azure メニューから  **[+ リソースの作成]** を選択します。 " *Azure Front Door Standard/Premium (プレビュー)* " を検索します。 **[作成]** を選択します。

1. **[Compare offerings]\(オファリングの比較\)** ページで、 **[簡易作成]** を選択します。 次に、 **[Continue to create a Front Door]\(フロント ドアの作成を続行する\)** を選択します。

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="オファリングの比較のスクリーンショット。":::

1. **[Create a front door profile]\(Front Door プロファイルの作成\)** ページで、次の設定を入力または選択します。

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Front Door の簡易作成ページのスクリーンショット。":::    

    | 設定 | 値 |
    | --- | --- |
    | **サブスクリプション**  | サブスクリプションを選択します。 |
    | **リソース グループ**  | **[新規作成]** を選択し、テキスト ボックスに「*contoso-appservice*」と入力します。|
    | **名前** | プロファイルに名前を付けます。 この例では、「**contoso-afd-quickcreate**」を使用します。 |
    | **レベル** | Standard と Premium のいずれかの SKU を選択します。 Standard SKU は、コンテンツ配信に最適化されています。 Premium SKU は、Standard SKU をベースに、セキュリティにも重点が置かれています。 [レベルの比較](tier-comparison.md)に関する記事をご覧ください。  |
    | **[エンドポイント名]** | グローバルに一意のエンドポイント名を入力します。 |
    | **配信元の種類** | 配信元のリソースの種類を選択します。 この例では、Private Link が有効になっている配信元として、App Service を選択します。 |
    | **配信元のホスト名** | 配信元のホスト名を入力します。 |
    | **Private Link を有効にする** | Azure Front Door と配信元の間でプライベート接続を使用する場合に選択します。 詳細については、[プライベート リンクのガイダンス](concept-private-link.md)と[プライベート リンクの有効化](how-to-enable-private-link.md)に関するページをご覧ください。
    | **キャッシュ** | Azure Front Door のエッジ POP と Microsoft ネットワークを使用して、コンテンツをユーザーにより近い場所でグローバルにキャッシュする場合は、このチェック ボックスをオンにします。 |
    | **WAF ポリシー** | この機能を有効にする場合は、 **[新規作成]** を選択するか、ドロップダウンから既存の WAF ポリシーを選択します。 |

    > [!NOTE]
    > Azure Front Door Standard/Premium プロファイルを作成する際は、フロント ドアを作成しているのと同じサブスクリプションから配信元を選択する必要があります。

1. **[レビューと作成]** を選択し、Front Door プロファイルを起動して実行します。

   > [!NOTE]
   > 構成がすべてのエッジ POP に反映されるには、数分を要する場合があります。

1. 次に、 **[作成]** をクリックし、Front Door プロファイルをデプロイして実行します。

1. Private Link を有効にした場合は、配信元 (この例では App Service) にアクセスします。 **[ネットワーク]**  >  **[Configure Private Link]\(プライベート リンクの構成\)** を選択します。 次に、Azure Front Door からの保留中の要求を選択し、[承認] をクリックします。 数秒後に、Azure Front Door を介してアプリケーションに安全にアクセスできるようになります。

## <a name="create-front-door-profile---custom-create"></a>Front Door プロファイルを作成する - カスタム作成

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>配信元として 2 つのインスタンスがある Web アプリを作成する

配信元または配信元グループを既に構成している場合は、「アプリケーションに Front Door Standard/Premium (プレビュー) を作成する」に進みます。

この例では、異なる Azure リージョンで実行される 2 つのインスタンスがある Web アプリケーションを作成します。 どちらの Web アプリケーション インスタンスも "*アクティブ/アクティブ* " モードで実行されるため、トラフィックをどちらでも受け入れることができます。 この構成は、一方がフェールオーバーとして機能する "*アクティブ/スタンバイ*" 構成とは異なります。

まだ Web アプリがない場合は、次の手順に従ってサンプル Web アプリを設定してください。

1. Azure Portal ( https://portal.azure.com ) にサインインします。

1. 画面の左上で、 **[リソースの作成]**  >  **[WebApp]** の順に選択します。

1. **[Web アプリの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | **サブスクリプション**               | サブスクリプションを選択します。 |
    | **リソース グループ**       | **[新規作成]** を選択し、テキスト ボックスに 「*FrontDoorQS_rg1*」 と入力します。|
    | **名前**                   | Web アプリの一意の **名前** を入力します。 この例では「*WebAppContoso-001*」を使用します。 |
    | **発行** | **[コード]** を選択します。 |
    | **ランタイム スタック**         | **[.NET Core 2.1 (LTS)]** を選択します。 |
    | **オペレーティング システム**          | **[Windows]** を選択します。 |
    | **[リージョン]**           | **[米国中部]** を選択します。 |
    | **Windows プラン** | **[新規作成]** を選択し、テキスト ボックスに「*myAppServicePlanCentralUS*」と入力します。 |
    | **SKU とサイズ** | **[Standard S1 100 total ACU, 1.75-GB memory]\(Standard S1 合計 100 ACU、1.75 GB メモリ\)** を選択します。 |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Azure portal での Front Door Premium SKU の簡易作成":::

1. **[レビューと作成]** を選択し、[概要] を確認して、 **[作成]** を選択します。 デプロイには数分かかる場合があります。

デプロイが完了したら、もう 1 つ Web アプリを作成します。 次の設定を除き、上記と同じ設定を使用します。

| 設定          | 値     |
| ---              | ---  |
| **リソース グループ**   | **[新規作成]** を選択し、「*FrontDoorQS_rg2*」と入力します。 |
| **名前**             | Web アプリの一意の名前を入力します (この例では「*WebAppContoso-002*」)。  |
| **[リージョン]**           | 異なるリージョンを選択します (この例では *[米国中南部]* )。 |
| **App Service プラン** > **Windows プラン**         | **[新規]** を選択し、「*myAppServicePlanSouthCentralUS*」と入力して、 **[OK]** を選択します。 |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>アプリケーション用の Front Door Standard/Premium (プレビュー) を作成する

2 つの Web アプリ サーバーのうち低遅延の方にユーザー トラフィックを誘導するように Azure Front Door Standard/Premium (プレビュー) を構成します。 また、Web アプリケーション ファイアウォールを使用してフロント ドアをセキュリティで保護します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
 
1. ホーム ページまたは Azure メニューから  **[+ リソースの作成]** を選択します。 " *Azure Front Door Standard/Premium (プレビュー)* " を検索します。 **[作成]** を選択します。

1. **[Compare offerings]\(オファリングの比較\)** ページで、 **[カスタム作成]** を選択します。 次に、 **[Continue to create a Front Door]\(フロント ドアの作成を続行する\)** を選択します。

1.  **[基本]**  タブで、次の情報を入力または選択し、 **[Next: Secret]\(次へ: シークレット\)** を選択します。 

    | 設定 | 値 |
    | --- | --- |
    | **サブスクリプション** | サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択し、テキスト ボックスに「*FrontDoorQS_rg0*」 と入力します。 |
    | **リソース グループの場所** | **[米国東部]** を選択します。 |
    | **[プロファイル名]** | このサブスクリプションで一意の名前である「**Webapp-Contoso-AFD**」と入力します |
    | **レベル** | **[Premium]** を選択します。 |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Front Door プロファイルを作成する":::

1. "*省略可能*": **[シークレット]** 。 マネージド証明書を使用する予定がある場合、この手順は省略できます。 カスタム ドメイン用に独自の証明書を持ち込むために使用する予定のキー コンテナーが Azure にある場合は、 **[証明書の追加]** を選択します。 作成後に、管理エクスペリエンスで証明書を追加することもできます。

    > [!NOTE]
    > ユーザーとして Azure キー コンテナーから証明書を追加するには、適切なアクセス許可が必要です。 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="カスタム作成でのシークレットの追加を示すスクリーンショット。":::

1. **[エンドポイント]** タブで、 **[エンドポイントの追加]** を選択し、グローバルに一意の名前をエンドポイントに付けます。 作成エクスペリエンスを完了したら、Azure Front Door Standard/Premium プロファイルに複数のエンドポイントを作成できます。 この例では、*contoso-frontend* を使用します。 [Origin response timeout (in seconds)]\(配信元の応答タイムアウト (秒単位)\) と [状態] は、既定値のままにします。 **[追加]** を選択してエンドポイントを追加します。
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="エンドポイントの追加のスクリーンショット。":::

1. 次に、2 つの Web アプリを含む配信元グループを追加します。  **[+ 追加]**  を選択して  **[配信元グループの追加]** ページを開きます。 [名前] には、「 *myOrignGroup*」と入力し、 **[+ Add an origin]\(+ 配信元の追加\)** を選択します。
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="配信元グループの追加のスクリーンショット。":::

1.  **[Add an origin]\(配信元の追加\)**  ページで、以下の情報を入力または選択します。 その後、 **[追加]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **名前** | 「**webapp1**」と入力します |
    | **配信元の種類** | **[App Services]** を選択します |
    | **ホスト名** | [`WebAppContoso-001.azurewebsites.net`] を選択します |
    | **配信元のホスト ヘッダー** | [`WebAppContoso-001.azurewebsites.net`] を選択します |
    | **その他のフィールド** | 他のフィールドはすべて既定値のままにします。 |

    > [!NOTE]
    > Azure Front Door Standard/Premium プロファイルを作成する際は、Azure Front Door Standard/Premium を作成しているのと同じサブスクリプションから配信元を選択する必要があります。
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="さらに配信元を追加しているところを示すスクリーンショット。":::

1. 手順 8. を繰り返して、2 番目の配信元である webapp002 を追加します。 **[配信元のホスト名]** および **[配信元のホスト ヘッダー]** として `webappcontoso-002.azurewebsite.net` を選択します。

1. **[配信元グループの追加]** ページで、2 つの配信元が追加されていることを確認できます。その他のフィールドはすべて既定値のままにします。
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="[配信元グループの追加] ページのスクリーンショット。":::

1. 次に、フロントエンド エンドポイントを配信元グループにマッピングするためのルートを追加します。 このルートは、エンドポイントからの要求を myOriginGroup に転送します。 [ルート] で **[+ 追加]** を選択して、ルートを構成します。  

1. **[ルートの追加]** ページで、以下の情報を入力または選択します。 その後、 **[追加]** を選択します。
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="キャッシュを使用せずにルートを追加する":::

    | 設定 | 値 |
    | --- | --- |
    | **名前** | 「**MyRoute**」と入力します |    
    | **[ドメイン]** | [`contoso-frontend.z01.azurefd.net`] を選択します | 
    | **ホスト名** | [`WebAppContoso-001.azurewebsites.net`] を選択します |
    | **一致するパターン** | 既定値のままにします。 |
    | **受け入れ済みのプロトコル** | 既定値のままにします。 | 
    | **リダイレクト** | 既定値のままにして、**HTTPS を使用するようにすべてのトラフィックをリダイレクトします**。 | 
    | **配信元グループ** | **[MyOriginGroup]** を選択します。 | 
    | **配信元のパス** | 既定値のままにします。 | 
    | **転送プロトコル** | **[一致受信要求]** を選択します。 | 
    | **キャッシュ** | このクイックスタートでは、オフのままにしておきます。 コンテンツをエッジにキャッシュする場合は、 **[キャッシュを有効にする]** のチェック ボックスをオンにします。 |
    | **ルール** | 既定値のままにします。 Front Door プロファイルを作成したら、カスタム ルールを作成して、それらをルートに適用できます。 |  
       
    >[!WARNING]
    > エンドポイントごとにルートがあることを **確認** します。 ルートがないと、エンドポイントが失敗する可能性があります。

1. 次に、[セキュリティ] で **[+ 追加]** を選択して WAF ポリシーを追加します。 **[新規追加]** を選択し、ポリシーに一意の名前を付けます。 **[ボット保護の追加]** のチェック ボックスをオンにします。 **[ドメイン]** でエンドポイントを選択し、 **[追加]** を選択します。

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="WAF ポリシーを追加する":::

1.  **[レビューと作成]** 、 **[作成]** の順に選択します。 構成がすべてのエッジ POP に反映されるには、数分を要します。 これで、最初の Front Door プロファイルとエンドポイントが作成されました。

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="カスタム作成を確認する":::

## <a name="verify-azure-front-door"></a>Azure Front Door を確認する

Azure Front Door Standard/Premium プロファイルを作成する際、グローバルに構成がデプロイされるまでに数分かかります。 完了したら、作成したフロントエンド ホストにアクセスできます。 ブラウザーで `contoso-frontend.z01.azurefd.net` にアクセスしてください。 要求は、配信元グループ内で指定したサーバーから最も近いサーバーに自動的にルーティングされます。

それらのアプリをこのクイックスタートで作成した場合は、情報ページが表示されます。

即時グローバル フェールオーバーをテストするには、次の手順を使用します。

1. 前述したように、ブラウザーを開き、フロントエンド アドレス (`contoso-frontend.azurefd.net`) にアクセスします。

1. Azure portal で、*App Services* を検索して選択します。 下へスクロールして、いずれかの Web アプリを見つけます (この例では **WebAppContoso-001**)。

1. Web アプリを選択し、 **[停止]** 、 **[はい]** の順に選択して確認します。

1. ブラウザーを更新します。 同じ情報ページが表示されるはずです。

   >[!TIP]
   >これらの操作では、わずかな待ち時間が発生します。 場合によっては、再度、最新の情報に更新する必要があります。

1. もう一方の Web アプリを探し、同様に停止します。

1. ブラウザーを更新します。 今回はエラー メッセージが表示されます。

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Web アプリの両方のインスタンスが停止":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作業が完了したら、作成した項目はすべて削除してかまいません。 リソース グループを削除すると、その内容も削除されます。 このフロント ドアを使用する予定がない場合は、不要な料金が発生しないようリソースを削除してください。

1. Azure portal で **[リソース グループ]** を探して選択するか、Azure portal のメニューから **[リソース グループ]** を選択します。

1. フィルターを使用するか、下へスクロールしてリソース グループを探します (**FrontDoorQS_rg0** など)。

1. リソース グループを選択し、 **[リソース グループの削除]** を選択します。

   >[!WARNING]
   >このアクションは元に戻すことができません。

1. リソース グループの名前を入力して確認し、 **[削除]** を選択します。

他の 2 つのグループについても手順を繰り返します。

## <a name="next-steps"></a>次の手順

次の記事に進んで、フロント ドアにカスタム ドメインを追加する方法を学習してください。
> [!div class="nextstepaction"]
> [カスタム ドメインの追加](how-to-add-custom-domain.md)
