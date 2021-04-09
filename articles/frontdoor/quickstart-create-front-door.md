---
title: 'クイックスタート: Azure Front Door Service を使用して高可用性を設定する - Azure portal'
description: このクイックスタートでは、Azure portal で Azure Front Door Service を使用してグローバル Web アプリケーションの高可用性とハイパフォーマンスを実現する方法について示します。
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 4846438f8479fe622570aa515a4d8b40cccc57b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91252308"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>クイック スタート:グローバル Web アプリケーションの高可用性を実現するフロント ドアを作成する

Azure portal で Azure Front Door を使用して Web アプリケーションの高可用性を設定してみましょう。

このクイックスタートでは、別々の Azure リージョンで実行される、Web アプリケーションの 2 つのインスタンスを Azure Front Door でプールします。 等しく重み付けされた同じ優先度のバックエンドに基づいて Front Door の構成を作成します。 この構成では、アプリケーションが実行されている最も近いサイトにトラフィックが誘導されます。 Web アプリケーションは、Azure Front Door によって絶えず監視されます。 最も近いサイトが利用できない状態になると、サービスによって、使用可能な次のサイトへの自動フェールオーバーが行われます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-two-instances-of-a-web-app"></a>Web アプリの 2 つのインスタンスを作成する

このクイックスタートには、別々の Azure リージョンで実行される、Web アプリケーションの 2 つのインスタンスが必要です。 どちらの Web アプリケーション インスタンスも "*アクティブ/アクティブ* " モードで実行されるため、トラフィックをどちらでも受け入れることができます。 この構成は、一方がフェールオーバーとして機能する "*アクティブ/スタンバイ*" 構成とは異なります。

まだ Web アプリがない場合は、次の手順に従ってサンプル Web アプリをセットアップしてください。

1. Azure Portal ( https://portal.azure.com ) にサインインします。

1. 画面の左上で、 **[リソースの作成]**  >   **[WebApp]** の順に選択します。

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Azure portal で Web アプリを作成する":::

1. **[Web アプリの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | **サブスクリプション**               | サブスクリプションを選択します。 |    
    | **リソース グループ**       | **[新規作成]** を選択し、テキスト ボックスに 「*FrontDoorQS_rg1*」 と入力します。|
    | **名前**                   | Web アプリの一意の **名前** を入力します。 この例では *WebAppContoso-1* を使用します。 |
    | **発行** | **[コード]** を選択します。 |
    | **ランタイム スタック**         | **[.NET Core 2.1 (LTS)]** を選択します。 |
    | **オペレーティング システム**          | **[Windows]** を選択します。 |
    | **[リージョン]**           | **[米国中部]** を選択します。 |
    | **Windows プラン** | **[新規作成]** を選択し、テキスト ボックスに「*myAppServicePlanCentralUS*」と入力します。 |
    | **SKU とサイズ** | **[Standard S1 100 total ACU, 1.75 GB memory]\(Standard S1 合計 100 ACU、1.75 GB メモリ\)** を選択します。 |

1. **[確認および作成]** を選択し、 **[概要]** を確認して、 **[作成]** を選択します。 デプロイが完了するまでに数分かかる場合があります。

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Web アプリの概要を確認する":::

デプロイが完了したら、もう 1 つ Web アプリを作成します。 次の値を除き、同じ手順と値を使用します。

| 設定          | 値     |
| ---              | ---  |
| **リソース グループ**   | **[新規作成]** を選択し、「*FrontDoorQS_rg2*」 と入力します。 |
| **名前**             | Web アプリの一意の名前を入力します (この例では *WebAppContoso-2*)。  |
| **[リージョン]**           | 異なるリージョンを選択します (この例では *[米国中南部]* )。 |
| **App Service プラン** > **Windows プラン**         | **[新規]** を選択し、「*myAppServicePlanSouthCentralUS*」と入力して、 **[OK]** を選択します。 |

## <a name="create-a-front-door-for-your-application"></a>アプリケーション用のフロント ドアを作成する

2 つの Web アプリ サーバーのうち待ち時間が短い方にユーザー トラフィックを誘導するように Azure Front Door を構成します。 最初に、Azure Front Door のフロントエンド ホストを追加します。

1. ホーム ページまたは Azure メニューから **[リソースの作成]** を選択します。 **[ネットワーク]**  >  **[すべて表示]**  >  **[フロントドア]** の順に選択します。

1. **[フロント ドアを作成する]** ページの **[基本]** タブで、次の情報を入力または選択してから、 **[次へ: 構成]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **サブスクリプション** | サブスクリプションを選択します。 |    
    | **リソース グループ** | **[新規作成]** を選択し、テキスト ボックスに「*FrontDoorQS_rg0*」 と入力します。|
    | **リソース グループの場所** | **[米国中部]** を選択します。 |

1. **[Frontends/domains]\(フロントエンド/ドメイン\)** で、 **[+]** を選択して **[フロントエンド ホストの追加]** を開きます。

1. グローバルに一意のホスト名を **[ホスト名]** に入力します。 この例では、*contoso-frontend* を使用します。 **[追加]** を選択します。

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Azure Front Door のフロントエンド ホストを追加する":::

次に、2 つの Web アプリを含んだバックエンド プールを作成します。

1. 引き続き **[フロント ドアを作成する]** の **[バックエンド プール]** で **[+]** を選択し、 **[バックエンド プールの追加]** を開きます。

1. **[名前]** として「*myBackendPool*」を入力してから、 **[バックエンドの追加]** を選択します。

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="バックエンド プールを追加する":::

1. **[バックエンドの追加]** ブレードで、次の情報を選択し、 **[追加]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **バックエンド ホストの種類** | **[App Service]** を選択します。 |   
    | **サブスクリプション** | サブスクリプションを選択します。 |    
    | **バックエンド ホスト名** | 最初に作成した Web アプリを選択します。 この例では、*WebAppContoso-1* という Web アプリが該当します。 |

    "**他のフィールドはすべて既定値のままにします。* "

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="ご利用のフロント ドアにバックエンド ホストを追加する":::

1. もう一度 **[バックエンドの追加]** を選択します。 次の情報を選択し、 **[追加]** を選択します。

    | 設定 | 値 |
    | --- | --- |
    | **バックエンド ホストの種類** | **[App Service]** を選択します。 |   
    | **サブスクリプション** | サブスクリプションを選択します。 |    
    | **バックエンド ホスト名** | 作成した 2 つ目の Web アプリを選択します。 この例では、*WebAppContoso-2* という Web アプリが該当します。 |

    "**他のフィールドはすべて既定値のままにします。* "

1. **[バックエンド プールの追加]** ブレードで **[追加]** を選択して、バックエンド プールの構成を完了します。

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Azure Front Door 用のバックエンド プールを追加する":::

最後に、ルーティング規則を追加します。 フロントエンド ホストは、ルーティング規則によってバックエンド プールにマップされます。 この規則によって、`contoso-frontend.azurefd.net` への要求が **myBackendPool** に転送されます。

1. 引き続き **[フロント ドアを作成する]** の **[ルーティング規則]** で **[+]** を選択し、ルーティング規則を構成します。

1. **[規則を追加する]** の **[名前]** に「*LocationRule*」と入力します。 すべて既定値のままにし、 **[追加]** を選択してルーティング規則を追加します。

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="フロント ドアにルールを追加する":::

   >[!WARNING]
   > Front Door 内の各フロントエンド ホストに、既定のパス (`\*`) が関連付けられたルーティング規則があることを確認する **必要があります**。 つまり、すべてのルーティング規則について、既定のパス (`\*`) で定義された各フロントエンド ホストに対するルーティング規則が少なくとも 1 つは存在する必要があります。 そうなっていないと、エンド ユーザーのトラフィックが正しくルーティングされない可能性があります。

1. **[確認および作成]** 、 **[作成]** の順に選択します。

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="構成された Azure Front Door":::

## <a name="view-azure-front-door-in-action"></a>Azure Front Door の動作を確認する

フロント ドアを作成した後、グローバルに構成がデプロイされるまでに数分かかります。 完了したら、作成したフロントエンド ホストにアクセスします。 ブラウザーで `contoso-frontend.azurefd.net` にアクセスしてください。 要求は、バックエンド プール内の指定されたサーバーの中から、自分に最も近いサーバーに自動的にルーティングされます。

それらのアプリをこのクイックスタートで作成した場合は、情報ページが表示されます。

即時グローバル フェールオーバーの動作をテストするために、次の手順を試してみましょう。

1. 前述したように、ブラウザーを開き、フロントエンド アドレス (`contoso-frontend.azurefd.net`) にアクセスします。

1. Azure portal で、*App Services* を検索して選択します。 下へスクロールして、いずれかの Web アプリを探します (この例では **WebAppContoso-1**)。

1. Web アプリを選択し、 **[停止]** 、 **[はい]** の順に選択して確認します。

1. ブラウザーを更新します。 同じ情報ページが表示されるはずです。

   >[!TIP]
   >これらの操作では、わずかな待ち時間が発生します。 場合によっては、再度、最新の情報に更新する必要があります。

1. もう一方の Web アプリを探し、同様に停止します。

1. ブラウザーを更新します。 今回はエラー メッセージが表示されます。

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Web アプリの両方のインスタンスが停止":::

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
> [カスタム ドメインの追加](front-door-custom-domain.md)
