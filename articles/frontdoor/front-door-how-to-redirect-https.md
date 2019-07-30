---
title: Azure portal を使用して HTTP から HTTPS にリダイレクトする Front Door を作成する
description: Azure portal を使用して、HTTP から HTTPS にトラフィックがリダイレクトされる Front Door を作成する方法について説明します。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 7fabc1e3445d3dbd357700ffde3caeb985cc60c4
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67601967"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure portal を使用して HTTP から HTTPS にリダイレクトする Front Door を作成する

Azure portal を使用して、SSL 終了の証明書で [Front Door](front-door-overview.md) を作成できます。 ルーティング規則を使用して、HTTP トラフィックを HTTPS にリダイレクトします。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 既存の Web アプリ リソースを使用して Front Door を作成する
> * SSL 証明書を使用してカスタム ドメインを追加する 
> * カスタム ドメインで HTTPS リダイレクトをセットアップする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>既存の Web アプリ リソースを使用して Front Door を作成する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
2. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
3. 検索バーを使用して **Front Door** を検索し、リソースの種類を見つけたら、 **[作成]** をクリックします。
4. サブスクリプションを選択して、既存のリソース グループを使用するか、新しいものを作成します。 UI で指定するよう求められる場所は、このリソース グループ専用です。 Front Door 構成は、[Azure Front Door の POP の場所](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service)全体にわたってデプロイされます。

    ![新規 Front Door の基本を構成する](./media/front-door-url-redirect/front-door-create-basics.png)

5. **[次へ]** をクリックして構成タブに進みます。Front Door の構成は、3 つの手順、つまり既定のフロントエンド ホストの追加、バックエンド プールへのバックエンドの追加、およびフロントエンド ホストのルーティング動作をマップするルーティング規則の作成によって行われます。 

     ![Front Door 構成デザイナー](./media/front-door-url-redirect/front-door-designer.png)

6. _フロントエンド ホスト_の ' **+** ' アイコンをクリックしてフロントエンド ホストを作成し、Front Door の既定のフロントエンド ホストに対するグローバルで一意な名前を入力します (`\<**name**\>.azurefd.net`)。 **[追加]** をクリックして次の手順に進みます。

     ![フロントエンド ホストを追加する](./media/front-door-url-redirect/front-door-create-fehost.png)

7. _[バックエンド プール]_ の ' **+** ' アイコンをクリックして、バックエンド プールを作成します。 バックエンド プールの名前を指定し、 **[バックエンドの追加]** をクリックします。
8. [バックエンド ホストの種類] で _[App service]_ を選択します。 お使いの Web アプリがホストされているサブスクリプションを選択し、**バックエンド ホスト名**用のドロップダウンから特定の Web アプリを選択します。
9. **[追加]** をクリックしてバックエンドを保存し、もう一度 **[追加]** をクリックしてバックエンド プールの構成を保存します。 ![バックエンド プールにバックエンドを追加する](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. _[ルーティング規則]_ で ' **+** ' アイコンをクリックしてルートを作成します。 ルートの名前 ('HttpToHttpsRedirect' など) を指定し、 _[受け入れ済みのプロトコル]_ フィールドを **[HTTP のみ]** に設定します。 適切な_フロントエンド ホスト_が選択されていることを確認します。  
11. _[ルートの詳細]_ セクションで、 _[ルートの種類]_ を **[リダイレクト]** に設定し、 _[リダイレクトの種類]_ が **[検出 (302)]** に設定され、 _[プロトコルをリダイレクトします]_ が **[HTTPS のみ]** に設定されていることを確認します。 
12. [追加] をクリックして、HTTP から HTTPS へのリダイレクト用のルーティング規則を保存します。
     ![HTTP から HTTPS へのリダイレクト ルートを追加する](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. HTTPS トラフィックを処理するための別のルーティング規則を追加します。 _[ルーティング規則]_ で ' **+** ' 記号をクリックして、ルートの名前 ('DefaultForwardingRoute' など) を指定し、 _[受け入れ済みのプロトコル]_ フィールドを **[HTTP のみ]** に設定します。 適切な_フロントエンド ホスト_が選択されていることを確認します。
14. [ルートの詳細] セクションで、 _[ルートの種類]_ を **[転送]** に設定し、適切なバックエンド プールが選択されていること、および _[転送プロトコル]_ が **[HTTPS のみ]** に設定されていることを確認します。 
15. [追加] をクリックして、要求の転送用のルーティング規則を保存します。
     ![HTTPS トラフィックの転送ルートを追加する](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. **[レビュー + 作成]** 、 **[作成]** の順にクリックし、Front Door プロファイルを作成します。 作成されたリソースに移動します。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Front Door にカスタム ドメインを追加し、そこで HTTPS を有効にする
次の手順では、既存の Front Door リソースにカスタム ドメインを追加し、そこで HTTP から HTTPS へのリダイレクトを有効にする方法を紹介します。 

### <a name="add-a-custom-domain"></a>カスタム ドメインの追加

この例では、`www` サブドメイン (たとえば `www.contosonews.com`) の CNAME レコードを追加します。

#### <a name="create-the-cname-record"></a>CNAME レコードを作成する

サブドメインを Front Door の既定のフロントエンド ホスト (`<name>.azurefd.net`、ここで、`<name>` は Front Door プロファイルの名前) にマップする CNAME レコードを追加します。

たとえば `www.contoso.com` ドメインの場合、名前 `www` を `<name>.azurefd.net` にマップする CNAME レコードを追加します。

CNAME を追加した後の DNS レコード ページは次の例のようになります。

![Front Door に対する CNAME カスタム ドメイン](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>カスタム ドメインを Front Door にオンボードする

1. [Front Door デザイナー] タブで、フロントエンド ホスト セクションの '+' アイコンをクリックして、新しいカスタム ドメインを追加します。 
2. カスタム ホスト名フィールドに完全修飾カスタム DNS 名を入力します (例: `www.contosonews.com`)。 
3. ドメインから Front Door への CNAME マッピングを確認したら、 **[追加]** をクリックしてカスタム ドメインを追加します。
4. **[保存]** をクリックして、変更を送信します。

![[カスタム ドメイン] メニュー](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>カスタム ドメインで HTTPS を有効にする

1. 追加されたカスタム ドメインをクリックし、 **[カスタム ドメイン HTTPS]** セクションで、状態を **[有効]** に変更します。
2. Front Door によって維持、管理、およびオートローテーションされる無料の証明書については、 **[証明書の管理の種類]** を _[Front Door managed]/(Front Door による管理/)_ のままにすることができます。 また、Azure Key Vault に格納されている独自のカスタム SSL 証明を使用することもできます。 このチュートリアルでは、Front Door によって管理される証明書を使用することを前提としています。
![カスタム ドメインに対して HTTPS を有効にする](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. **[更新]** をクリックして選択内容を保存し、 **[保存]** をクリックします。
4. 数分経った後で **[更新]** をクリックし、カスタム ドメインをもう一度クリックして、証明書のプロビジョニングの進行状況を確認します。 

> [!WARNING]
> カスタム ドメインの HTTPS の有効化は、数分かかる場合があり、また、CNAME が Front Door ホスト `<name>.azurefd.net` に直接マップされていない場合は、ドメインの所有権の検証によっても異なります。 [カスタム ドメインの HTTPS を有効にする方法](./front-door-custom-domain-https.md)に関する記事を参照してください。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>カスタム ドメインのルーティング規則を構成する

1. 先ほど作成したリダイレクト ルーティング規則をクリックします。
2. フロントエンド ホストについてのドロップダウンをクリックし、使用するカスタム ドメインを選択して、このルートを該当のドメインにも適用します。
3. **[Update]** をクリックします。
4. 他のルーティング規則についても (つまりカスタム ドメインを追加するための転送ルートについても)、同じ操作を実行します。
5. **[保存]** をクリックして変更を送信します。

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
- [Front Door の URL リダイレクト](front-door-url-redirect.md)について詳しく学習します。
