---
title: Traffic Manager を使用した DNS 名の構成
description: 負荷分散のために Traffic Manager と統合する Azure App Service アプリのカスタムドメインを構成する方法について学びます。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709106"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Traffic Manager 統合を使用して Azure App Service 内のカスタム ドメイン名を構成する

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services については、「[Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。

[Azure Traffic Manager](../traffic-manager/index.yml) を使用してトラフィックを [Azure App Service](overview.md) に負荷分散する場合、App Service アプリには **\<traffic-manager-endpoint>.trafficmanager.net** を使用してアクセスできます。 ユーザーにわかりやすいドメイン名を付けるために、www\.contoso.com のようなカスタム ドメイン名を App Service に割り当てることができます。

この記事では、[Traffic Manager](../traffic-manager/traffic-manager-overview.md)と統合された App Service アプリを使用してカスタム ドメイン名を構成する方法について説明します。

> [!NOTE]
> Traffic Manager エンドポイントを使用してドメイン名を構成した場合、[CNAME](https://en.wikipedia.org/wiki/CNAME_record) レコードのみがサポートされます。 A レコードはサポートされていないため、ルート ドメイン マッピング (contoso.com など) もサポートされていません。
> 

## <a name="prepare-the-app"></a>アプリの準備

Azure Traffic Manager に統合されたアプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/) が **Standard** レベル以上である必要があります。 この手順では、App Service アプリがサポートされている価格レベルであることを確認します。

### <a name="check-the-pricing-tier"></a>価格レベルの確認

[Azure portal](https://portal.azure.com) で、**App Services** を検索して選択します。

**[App Services]** ページで、Azure アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

そのアプリ ページの左のナビゲーションで、 **[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **Standard** レベル以上であることを確認してください (**Production** または **Isolated** カテゴリにある任意のレベル)。 [はい] の場合は、 **[スケール アップ]** ページを閉じ、[CNAME マッピング](#create-the-cname-mapping) にスキップします。

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

お使いのアプリをスケール アップする必要がある場合は、 **[Production]** カテゴリで任意の価格レベルを選択します。 その他のオプションについては、 **[See additional options]\(その他のオプションを参照する\)** をクリックします。

**[Apply]** をクリックします。

## <a name="create-traffic-manager-endpoint"></a>Traffic Manager エンドポイントの作成

「 [エンドポイントの追加と削除](../traffic-manager/traffic-manager-manage-endpoints.md) 」の手順に従って、App Service アプリをお使いの Traffic Manager プロファイルのエンドポイントとして追加します。

App Service アプリがサポートされている価格レベルになると、エンドポイントを追加するときに、使用可能な App Service ターゲットのリストが表示されます。 アプリのリストが表示されない場合は、[お使いのアプリの価格レベルを確認します](#prepare-the-app)。

## <a name="create-the-cname-mapping"></a>CNAME マッピングを作成する
> [!NOTE]
> [購入した App Service ドメイン](manage-custom-dns-buy-domain.md) を構成するには、このセクションをスキップし、「[カスタム ドメインを有効にする](#enable-custom-domain)」に進んでください。
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

ドメイン プロバイダーによって仕様が異なりますが、[ルート以外のカスタム ドメイン名](#what-about-root-domains) (**www.contoso.com** など) *から*、お使いのアプリと統合されている Traffic Manager のドメイン名 (**contoso.trafficmanager.net**) *に* マッピングします。 

> [!NOTE]
> レコードが既に使用されており、事前にアプリをバインドする必要がある場合は、追加の CNAME レコードを作成できます。 たとえば、**www\.contoso.com** をお使いのアプリに事前にバインドするには、**awverify.www** から **contoso.trafficmanager.net** への CNAME レコードを作成します。 その後、"www" CNAME レコードに変更を加えることなく、"www\.contoso.com" をお使いのアプリに追加できます。 詳細については、「[アクティブな DNS 名を Azure App Service に移行する](manage-custom-dns-migrate-domain.md)」を参照してください。

ドメイン プロバイダーで DNS レコードの追加または変更が完了したら、変更を保存します。

### <a name="what-about-root-domains"></a>ルート ドメインについて

Traffic Manager では CNAME レコードを使用したカスタム ドメイン マッピングのみがサポートされており、DNS 標準では CNAME レコードがルート ドメインのマッピング (**contoso.com** など) にサポートされていないため、Traffic Manager はルート ドメインへのマッピングをサポートしていません。 この問題を回避するには、アプリ レベルで URL リダイレクトを使用します。 たとえば ASP.NET Core では、[URL の書き換え](/aspnet/core/fundamentals/url-rewriting)を使用できます。 次に、Traffic Manager を使用してサブドメインを負荷分散します (**www.contoso.com**)。 別の方法として、[Azure Traffic Manager プロファイルを参照するためのドメイン名の頂点に対するエイリアス レコードを作成する](../dns/tutorial-alias-tm.md)ことができます。 一例として contoso.com があります。 サービスのリダイレクトを使用する代わりに、ゾーンから直接 Traffic Manager プロファイルを参照するように Azure DNS を構成できます。 

高可用性シナリオでは、ルート ドメインから各アプリのコピーの IP アドレスにポイントする複数の *A レコード* を作成することによって、Traffic Manager を使用せずに負荷分散 DNS セットアップを実装できます。 次に、[同じルート ドメインをすべてのアプリのコピーにマップします](app-service-web-tutorial-custom-domain.md#map-an-a-record)。 同じドメイン名を同じリージョン内の 2 つの異なるアプリにマップすることはできないため、このセットアップは、アプリのコピーが異なるリージョンにある場合にのみ機能します。

## <a name="enable-custom-domain"></a>カスタム ドメインを有効にする
ドメイン名のレコードが反映されたら、ブラウザーを使用して、カスタム ドメイン名が App Service アプリに解決することを確認します。

> [!NOTE]
> CNAME が DNS に反映されるまで多少の時間がかかります。 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> などのサービスを使用して、CNAME を利用できるかどうかを確認できます。
> 
> 

1. ドメイン解決が成功したら、[Azure Portal](https://portal.azure.com) のアプリページに戻ります
2. 左のナビゲーションで、 **[カスタム ドメイン]**  >  **[ホスト名の追加]** を選択します。
4. 以前にマップしたカスタム ドメイン名を入力し、 **[検証]** を選択します。
5. **[ホスト名レコード タイプ]** が **[CNAME (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。

6. App Service アプリが Traffic Manager エンドポイントと統合されるようになったため、**CNAME 構成** に Traffic Manager ドメイン名が表示されます。 それを選択して **[カスタム ドメインの追加]** をクリックします。

    ![アプリへの DNS 名の追加](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure App Service で SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)