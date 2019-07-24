---
title: チュートリアル - Azure App Service Web アプリへの Azure CDN の追加 | Microsoft Docs
description: このチュートリアルでは、静的なファイルをキャッシュし、世界各地の顧客に対して地理的に近いサーバーから配信するため、Azure App Service Web アプリに Azure Content Delivery Network (CDN) を追加します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 33b47d33262a4968a0eafb9ec70ef73e50975735
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602819"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>チュートリアル:Azure App Service Web アプリへの Azure CDN の追加

このチュートリアルでは、[Azure App Service 内の Web アプリ](../app-service/overview.md)に [Azure Content Delivery Network (CDN)](cdn-overview.md) を追加する方法について説明します。 Web Apps は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするためのサービスです。 

以下に示したのは、これから扱うサンプルの静的 HTML サイトのホーム ページです。

![サンプル アプリのホーム ページ](media/cdn-add-to-web-app/sample-app-home-page.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * CDN エンドポイントを作成する。
> * キャッシュされた資産を更新する。
> * キャッシュされたバージョンをクエリ文字列で制御する。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [Git をインストールする](https://git-scm.com/)
- [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Web アプリの作成

ここで扱う Web アプリは、[静的 HTML のクイックスタート](../app-service/app-service-web-get-started-html.md)の「**アプリの参照**」の手順に従って作成します。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

ブラウザーを開いて [Azure Portal](https://portal.azure.com) に移動します。

### <a name="dynamic-site-acceleration-optimization"></a>動的サイト アクセラレーションの最適化
動的サイト アクセラレータ (DSA) の CDN エンドポイントを最適化する場合は、[CDN ポータル](cdn-create-new-endpoint.md)を使用して、エンドポイントのプロファイルを作成する必要があります。 [DSA 最適化](cdn-dynamic-site-acceleration.md)を使用すると、動的コンテンツを含む Web ページのパフォーマンスがある程度まで向上します。 CDN ポータルからの DSA の CDN エンドポイント最適化方法については、「[動的ファイルの配信を高速化する CDN エンドポイントの構成](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files)」をご覧ください。 または、新しいエンドポイントを最適化したくない場合は、次のセクションの手順に従って、Web アプリ ポータルを使用して作成できます。 **Azure CDN from Verizon** プロファイルでは、CDN エンドポイントの作成後にエンドポイントの最適化を変更することはできません。

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN プロファイルと CDN エンドポイントを作成する

左側のナビゲーションで **[App Services]** を選択し、[静的 HTML のクイック スタート](../app-service/app-service-web-get-started-html.md)で作成したアプリを選択します。

![ポータルで App Service アプリを選択](media/cdn-add-to-web-app/portal-select-app-services.png)

**[App Service]** ページの **[設定]** セクションで、**[ネットワーク] の [アプリに Azure CDN を構成する]** を選択します。

![ポータルで CDN を選択](media/cdn-add-to-web-app/portal-select-cdn.png)

**[Azure Content Delivery Network]** ページで、**[新しいエンドポイント]** の設定を以下の表に従って指定します。

![ポータルでプロファイルとエンドポイントを作成](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Setting | 推奨値 | 説明 |
| ------- | --------------- | ----------- |
| **[CDN プロファイル]** | myCDNProfile | CDN プロファイルは、同じ価格レベルの CDN エンドポイントをまとめたものです。 |
| **[価格レベル]** | Standard Akamai | プロバイダーと使用可能な機能は、[価格レベル](cdn-features.md)によって規定されます。 このチュートリアルでは *Standard Akamai* を使用します。 |
| **[CDN エンドポイント名]** | azureedge.net ドメイン内で一意となる任意の名前 | キャッシュされたリソースには、*&lt;エンドポイント名&gt;*.azureedge.net ドメインでアクセスします。

CDN プロファイルを作成するには、**[作成]** を選択します。

Azure によってプロファイルとエンドポイントが作成されます。 **[エンドポイント]** の一覧に新しいエンドポイントが表示され、プロビジョニングされた時点で **[実行中]** 状態になります。

![リストに表示された新しいエンドポイント](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>CDN エンドポイントをテストする

 登録内容が反映されるまでに時間がかかるため、エンドポイントはすぐには使用できません。 
   - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
   - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
   - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 90 分以内で完了します。 

サンプル アプリには *index.html* ファイルのほか、静的な資産を格納する *css*、*img*、*js* の各フォルダーが存在します。 CDN エンドポイントでは、これらすべてのファイルに対して同じコンテンツ パスが使用されます。 たとえば次の 2 つの URL は、どちらも *css* フォルダー内の *bootstrap.css* ファイルにアクセスするものです。

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

![CDN から配信されたサンプル アプリのホーム ページ](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 先ほど Azure Web アプリで実行したのと同じページが表示されます。 Azure CDN は配信元の Web アプリの資産を取得し、それを CDN エンドポイントから配信しています。

このページを確実に CDN にキャッシュするには、ページを最新の情報に更新します。 要求されたコンテンツを CDN でキャッシュするためには、同じ資産に対して 2 回の要求が必要になる場合もあります。

Azure CDN のプロファイルとエンドポイントの詳細については、「[Azure CDN の概要](cdn-create-new-endpoint.md)」を参照してください。

## <a name="purge-the-cdn"></a>CDN を消去する

CDN はその配信元の Web アプリからのリソースを、Time to Live (TTL) の構成に基づいて定期的に更新します。 既定の TTL は 7 日間です。

最新のコンテンツを Web アプリにデプロイするときなど、場合によっては、TTL の期限が切れる前に CDN を更新しなければならないことがあります。 更新をトリガーするには、CDN のリソースを手動で消去します。 

このチュートリアル セクションでは、Web アプリに変更をデプロイし、CDN を消去してそのキャッシュの更新をトリガーします。

### <a name="deploy-a-change-to-the-web-app"></a>Web アプリに変更をデプロイする

次の例のように、*index.html* ファイルを開いて H1 見出しに *- V2* を追加します。 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

変更をコミットして Web アプリにデプロイします。

```bash
git commit -am "version 2"
git push azure master
```

デプロイの完了後、ブラウザーで Web アプリの URL にアクセスして変更を表示します。

```
http://<appname>.azurewebsites.net/index.html
```

![Web アプリのタイトルに V2 と表示される](media/cdn-add-to-web-app/v2-in-web-app-title.png)

ホーム ページの CDN エンドポイント URL にブラウザーでアクセスした場合、変更は表示されません。CDN にキャッシュされているバージョンの有効期限がまだ切れていないためです。 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN ではタイトルに V2 が表示されない](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>ポータルで CDN を消去する

CDN にキャッシュされたバージョンの更新をトリガーするには、CDN を消去します。

ポータルの左側のナビゲーションで **[リソース グループ]** を選択し、Web アプリ用に作成したリソース グループ (myResourceGroup) を選択します。

![リソース グループの選択](media/cdn-add-to-web-app/portal-select-group.png)

リソースの一覧から自分の CDN エンドポイントを選択します。

![エンドポイントの選択](media/cdn-add-to-web-app/portal-select-endpoint.png)

**[エンドポイント]** ページの上部にある **[消去]** を選択します。

![[消去] の選択](media/cdn-add-to-web-app/portal-select-purge.png)

消去するコンテンツ パスを入力します。 個々のファイルを消去するには、ファイルの完全パスを渡します。特定のフォルダーからすべてのコンテンツを消去し、最新の情報に更新するには、パス セグメントを渡します。 *index.html* を変更したため、それがいずれかのパスに含まれていることを確認します。

ページの下部にある **[消去]** を選択します。

![ページの消去](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>CDN が更新されていることを確認する

消去要求の処理が完了するまで待機します。これには通常、数分かかります。 現在の状態を確認するには、ページ上部のベル アイコンを選択します。 

![消去の通知](media/cdn-add-to-web-app/portal-purge-notification.png)

ブラウザーで *index.html* の CDN エンドポイント URL にアクセスすると、ホーム ページのタイトルに追加した *V2* が表示されます。これは、CDN キャッシュが最新の情報に更新されたことを示しています。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN でタイトルに V2 が表示されている](media/cdn-add-to-web-app/v2-in-cdn-title.png)

詳細については、「[Azure CDN エンドポイントの消去](../cdn/cdn-purge-endpoint.md)」を参照してください。 

## <a name="use-query-strings-to-version-content"></a>クエリ文字列を使用してコンテンツをバージョン管理する

Azure CDN では、次のキャッシュ動作を選択することができます。

* クエリ文字列を無視
* クエリ文字列に対するキャッシュをバイパス
* 一意の URL をすべてキャッシュ 

1 つ目のオプションが既定値です。つまり、URL 内のクエリ文字列に関係なく、資産のバージョンは 1 つしかキャッシュされません。 

このチュートリアル セクションでは、一意の URL をすべてキャッシュするようにキャッシュ動作を変更します。

### <a name="change-the-cache-behavior"></a>キャッシュ動作を変更する

Azure Portal の **[CDN エンドポイント]** ページで **[キャッシュ]** を選択します。

**[クエリ文字列のキャッシュ動作]** ボックスの一覧から **[一意の URL をすべてキャッシュ]** を選択します。

**[保存]** を選択します。

![クエリ文字列のキャッシュ動作の選択](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>一意の URL が個別にキャッシュされていることを確認する

ブラウザーで CDN エンドポイントにあるホーム ページにアクセスし、次のクエリ文字列を追加します。 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN から Web アプリの最新のコンテンツが返されます。見出しには *V2* と表示されます。 

このページを確実に CDN にキャッシュするには、ページを最新の情報に更新します。 

*index.html* を開き、*V2* を *V3* に変更してから、変更をデプロイします。 

```bash
git commit -am "version 3"
git push azure master
```

CDN エンドポイントの URL に新しいクエリ文字列 (`q=2` など) を追加し、ブラウザーでアクセスします。 Azure CDN によって最新の *index.html* ファイルが取得され、*V3* と表示されます。 ただし、`q=1` というクエリ文字列で CDN エンドポイントにアクセスした場合は、*V2* と表示されます。

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN でタイトルに V3 と表示される (クエリ文字列 2)](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN でタイトルに V2 と表示される (クエリ文字列 1)](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

この出力は、それぞれのクエリ文字列が区別して扱われていることを示しています。

* 以前は q=1 が使用されていたために、キャッシュされていたコンテンツ (V2) が返されています。
* q=2 は新しいクエリ文字列であるため、最新の Web アプリ コンテンツ (V3) が取得されて返されています。

詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御](../cdn/cdn-query-string.md)」を参照してください。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * CDN エンドポイントを作成する。
> * キャッシュされた資産を更新する。
> * キャッシュされたバージョンをクエリ文字列で制御する。

次の各記事に、CDN のパフォーマンスを最適化する方法が紹介されています。

> [!div class="nextstepaction"]
> [チュートリアル: カスタム ドメインを Azure CDN エンドポイントに追加する](cdn-map-content-to-custom-domain.md)


