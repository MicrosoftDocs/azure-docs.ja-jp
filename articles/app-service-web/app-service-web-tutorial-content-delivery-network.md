---
title: "Azure App Service に CDN を追加する | Microsoft Docs"
description: "静的なファイルをキャッシュし、世界各地の顧客に対して地理的に近いサーバーから配信するには、Azure App Service にコンテンツ配信ネットワーク (CDN) を追加します。"
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: a4f5113c4cc0ffb5fdd072e9a59743c83154c38c
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Azure App Service にコンテンツ配信ネットワーク (CDN) を追加する

[Azure Content Delivery Network (CDN)](../cdn/cdn-overview.md) では、効果を狙って配置された場所に静的 Web コンテンツをキャッシュすることで、ユーザーへのコンテンツ配信のスループットを最大化できます。 Web アプリでのサーバーの負荷も CDN によって軽減されます。 このチュートリアルでは、[Azure App Service 内の Web アプリ](app-service-web-overview.md)に Azure CDN を追加する方法について説明します。 

以下に示したのは、これから扱うサンプルの静的 HTML サイトのホーム ページです。

![サンプル アプリのホーム ページ](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * CDN エンドポイントを作成する。
> * キャッシュされた資産を更新する。
> * キャッシュされたバージョンをクエリ文字列で制御する。
> * CDN エンドポイントにカスタム ドメインを使用する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [Git をインストールする](https://git-scm.com/)
- [Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Web アプリの作成

ここで扱う Web アプリは、[静的 HTML のクイックスタート](app-service-web-get-started-html.md)の「**アプリの参照**」の手順に従って作成します。

### <a name="have-a-custom-domain-ready"></a>カスタム ドメインの準備

このチュートリアルのカスタム ドメインに関する手順を実行するには、カスタム ドメインを所有し、ドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権を持っている必要があります。 たとえば、`contoso.com` と `www.contoso.com` の DNS エントリを追加するには、`contoso.com` ルート ドメインに対して DNS 設定を構成するためのアクセス権が必要です。

既存のドメイン名がない場合は、[App Service ドメインのチュートリアル](custom-dns-web-site-buydomains-web-app.md)に従って、Azure Portal を使用してドメインを購入することを検討してください。 

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

ブラウザーを開いて [Azure Portal](https://portal.azure.com) に移動します。

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN プロファイルと CDN エンドポイントを作成する

左側のナビゲーションで **[App Services]** を選択し、[静的 HTML のクイック スタート](app-service-web-get-started-html.md)で作成したアプリを選択します。

![ポータルで App Service アプリを選択](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

**[App Service]** ページの **[設定]** セクションで、**[ネットワーク] の [アプリに Azure CDN を構成する]** を選択します。

![ポータルで CDN を選択](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

**[Azure Content Delivery Network]** ページで、**[新しいエンドポイント]** の設定を以下の表に従って指定します。

![ポータルでプロファイルとエンドポイントを作成](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Setting | 推奨値 | Description |
| ------- | --------------- | ----------- |
| **[CDN プロファイル]** | myCDNProfile | 新しい CDN プロファイルを作成するには、**[新規作成]** を選択します。 CDN プロファイルは、同じ価格レベルの CDN エンドポイントをまとめたものです。 |
| **[価格レベル]** | Standard Akamai | プロバイダーと使用可能な機能は、[価格レベル](../cdn/cdn-overview.md#azure-cdn-features)によって規定されます。 このチュートリアルでは、Standard Akamai を使用します。 |
| **[CDN エンドポイント名]** | azureedge.net ドメイン内で一意となる任意の名前 | キャッシュされたリソースには、*\<エンドポイント名>.azureedge.net* ドメインでアクセスします。

**[作成]**を選択します。

Azure によってプロファイルとエンドポイントが作成されます。 同じページにある **[エンドポイント]** の一覧に新しいエンドポイントが表示され、プロビジョニングされた時点で **[実行中]** 状態になります。

![リストに表示された新しいエンドポイント](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>CDN エンドポイントをテストする

Verizon 価格レベルを選択した場合、通常はエンドポイントの伝播に約 90 分かかります。 Akamai の場合、伝播にかかる時間は数分です。

サンプル アプリには `index.html` ファイルのほか、静的な資産を格納する *css*、*img*、*js* の各フォルダーが存在します。 CDN エンドポイントでは、これらすべてのファイルに対して同じコンテンツ パスが使用されます。 たとえば次の 2 つの URL は、どちらも *css* フォルダー内の *bootstrap.css* ファイルにアクセスするものです。

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

ブラウザーで次の URL に移動します。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN から配信されたサンプル アプリのホーム ページ](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 先ほど Azure Web アプリで実行したのと同じページが表示されます。 Azure CDN は配信元の Web アプリの資産を取得し、それを CDN エンドポイントから配信しています。

このページを確実に CDN にキャッシュするには、ページを最新の情報に更新します。 要求されたコンテンツを CDN でキャッシュするためには、同じ資産に対して 2 回の要求が必要になる場合もあります。

Azure CDN のプロファイルとエンドポイントの詳細については、「[Azure CDN の概要](../cdn/cdn-create-new-endpoint.md)」を参照してください。

## <a name="purge-the-cdn"></a>CDN を消去する

CDN はその配信元の Web アプリからのリソースを、Time to Live (TTL) の構成に基づいて定期的に更新します。 既定の TTL は 7 日間です。

最新のコンテンツを Web アプリにデプロイするときなど、場合によっては、TTL の期限が切れる前に CDN を更新しなければならないことがあります。 CDN のリソースを手動で消去すると、更新をトリガーすることができます。 

このチュートリアル セクションでは、Web アプリに変更をデプロイし、CDN を消去してそのキャッシュの更新をトリガーします。

### <a name="deploy-a-change-to-the-web-app"></a>Web アプリに変更をデプロイする

次の例のように、`index.html` ファイルを開いて H1 見出しに "- V2" を追加します。 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

変更をコミットして Web アプリにデプロイします。

```bash
git commit -am "version 2"
git push azure master
```

デプロイの完了後、ブラウザーで Web アプリの URL にアクセスすると、その変更が表示されます。

```
http://<appname>.azurewebsites.net/index.html
```

![Web アプリのタイトルに V2 と表示される](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

ホーム ページの CDN エンドポイント URL にブラウザーでアクセスしても、変更は表示されません。CDN にキャッシュされているバージョンの有効期限がまだ切れていないためです。 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN ではタイトルに V2 が表示されない](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>ポータルで CDN を消去する

CDN にキャッシュされたバージョンの更新をトリガーするには、CDN を消去します。

ポータルの左側のナビゲーションで **[リソース グループ]** を選択し、Web アプリ用に作成したリソース グループ (myResourceGroup) を選択します。

![リソース グループの選択](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

リソースの一覧から自分の CDN エンドポイントを選択します。

![エンドポイントの選択](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

**[エンドポイント]** ページの上部にある **[消去]** をクリックします。

![[消去] の選択](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

消去するコンテンツ パスを入力します。 個々のファイルを消去するには、ファイルの完全パスを渡します。特定のフォルダーからすべてのコンテンツを消去し、最新の情報に更新するには、パス セグメントを渡します。 変更を加えた `index.html` が、いずれかのパスに該当することを確認してください。

ページの下部にある **[消去]** を選択します。

![ページの消去](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>CDN が更新されていることを確認する

消去要求の処理が完了するまで待ちます (通常、数分)。 現在の状態を確認するには、ページ上部のベル アイコンを選択します。 

![消去の通知](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

CDN エンドポイントの `index.html` の URL にブラウザーでアクセスすると、今度は、ホーム ページのタイトルに追加した V2 が表示されていることがわかります。 これは CDN のキャッシュが更新されていることを示しています。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN でタイトルに V2 が表示されている](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

詳細については、「[Azure CDN エンドポイントの消去](../cdn/cdn-purge-endpoint.md)」を参照してください。 

## <a name="use-query-strings-to-version-content"></a>クエリ文字列を使用してコンテンツをバージョン管理する

Azure CDN では、次のキャッシュ動作を選択することができます。

* クエリ文字列を無視
* クエリ文字列に対するキャッシュをバイパス
* 一意の URL をすべてキャッシュ 

既定では 1 つ目の動作が選択されます。つまり URL 内のクエリ文字列に関係なく、資産のバージョンは 1 つしかキャッシュされません。 

このチュートリアル セクションでは、一意の URL をすべてキャッシュするようにキャッシュ動作を変更します。

### <a name="change-the-cache-behavior"></a>キャッシュ動作を変更する

Azure Portal の **[CDN エンドポイント]** ページで **[キャッシュ]** を選択します。

**[クエリ文字列のキャッシュ動作]** ボックスの一覧から **[一意の URL をすべてキャッシュ]** を選択します。

[ **保存**] を選択します。

![クエリ文字列のキャッシュ動作の選択](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>一意の URL が個別にキャッシュされていることを確認する

ブラウザーで CDN エンドポイントにあるホーム ページにアクセスします。ただし、次のクエリ文字列を追加します。 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

CDN から Web アプリの最新のコンテンツが返されます。見出しには "V2" と表示されます。 

このページを確実に CDN にキャッシュするには、ページを最新の情報に更新します。 

`index.html` を開いて "V2" を "V3" に変更し、その変更をデプロイします。 

```bash
git commit -am "version 3"
git push azure master
```

CDN エンドポイントの URL に新しいクエリ文字列 (`q=2` など) を追加し、ブラウザーでアクセスします。 CDN によって最新の `index.html` ファイルが取得され、"V3" と表示されます。  一方、`q=1` というクエリ文字列で CDN エンドポイントにアクセスした場合は、"V2" と表示されます。

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN でタイトルに V3 と表示される (クエリ文字列 2)](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN でタイトルに V2 と表示される (クエリ文字列 1)](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

この出力は、それぞれのクエリ文字列が区別して扱われていることを示しています。

* 以前は q=1 が使用されていたために、キャッシュされていたコンテンツ (V2) が返されています。
* q=2 は新しいクエリ文字列であるため、最新の Web アプリ コンテンツ (V3) が取得されて返されています。

詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御](../cdn/cdn-query-string.md)」を参照してください。

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>カスタム ドメインを CDN エンドポイントにマッピングする

カスタム ドメインを CDN エンドポイントにマッピングするには、CNAME レコードを作成します。 CNAME レコードは、ソース ドメインを目的のドメインにマッピングする DNS 機能です。 たとえば、`cdn.contoso.com` や `static.contoso.com` を `contoso.azureedge.net` にマッピングすることができます。

カスタム ドメインがない場合は、[App Service ドメインのチュートリアル](custom-dns-web-site-buydomains-web-app.md)に従って、Azure Portal を使用してドメインを購入することを検討してください。 

### <a name="find-the-hostname-to-use-with-the-cname"></a>CNAME で使用するホスト名を探す

Azure Portal の **[エンドポイント]** ページで、左側のナビゲーションの **[概要]** が選択されていることを確認し、ページ上部の **[+ カスタム ドメイン]** ボタンを選択します。

![カスタム ドメインの追加を選択](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

**[カスタム ドメインの追加]** ページに、CNAME レコードの作成に使用するエンドポイントのホスト名が表示されます。 ホスト名は、CDN エンドポイントの URL から生成されます (**&lt;エンドポイント名>.azureedge.net**)。 

![ドメインの追加ページ](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>ドメイン レジストラーで CNAME を構成する

ドメイン レジストラーの Web サイトに移動して、DNS レコードを作成するセクションを探します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。

CNAME 管理セクションを見つけます。 詳細設定ページに進み、"CNAME"、"エイリアス"、"サブドメイン" などの語句を探します。

選択したサブドメイン (**static**、**cdn** など) を、先ほど紹介したポータルの**エンドポイントのホスト名**にマッピングする CNAME レコードを作成します。 

### <a name="enter-the-custom-domain-in-azure"></a>Azure でカスタム ドメインを入力する

**[カスタム ドメインの追加]** ページに戻り、サブドメインを含むカスタム ドメインをダイアログ ボックスに入力します。 たとえば、「 `cdn.contoso.com`」のように入力します。   
   
入力したドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。

CNAME レコードがインターネット上のネーム サーバーに伝播するまでしばらく時間がかかる場合があります。 ドメインがすぐに検証されない場合は、数分間待ってからやり直してください。

### <a name="test-the-custom-domain"></a>カスタム ドメインをテストする

ブラウザーからカスタム ドメイン (例: `cdn.contoso.com/index.html`) で `index.html` ファイルにアクセスし、直接 `<endpointname>azureedge.net/index.html` にアクセスしたときと同じ結果が表示されることを確認します。

![サンプル アプリのホーム ページ (カスタム ドメインの URL を使用)](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

詳細については、「[カスタム ドメインへの Azure CDN コンテンツのマッピング](../cdn/cdn-map-content-to-custom-domain.md)」を参照してください。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * CDN エンドポイントを作成する。
> * キャッシュされた資産を更新する。
> * キャッシュされたバージョンをクエリ文字列で制御する。
> * CDN エンドポイントにカスタム ドメインを使用する。

次の各記事に、CDN のパフォーマンスを最適化する方法が紹介されています。

> [!div class="nextstepaction"]
> [Azure CDN でのファイル圧縮によるパフォーマンスの向上](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Azure CDN エンドポイント上のアセットを事前に読み込む](../cdn/cdn-preload-endpoint.md)

