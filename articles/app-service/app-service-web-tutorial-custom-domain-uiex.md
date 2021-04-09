---
title: チュートリアル:既存のカスタム DNS 名をマップする
description: 既存のカスタム DNS ドメイン名 (カスタム ドメイン) を、Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリに追加する方法について説明します。
keywords: App Service, Azure App Service, ドメイン マッピング, ドメイン名, 既存のドメイン, ホスト名, バニティ ドメイン
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32ad6fa122083b40a948345e360bf5b9b0f09e96
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954855"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>チュートリアル:既存のカスタム DNS 名を Azure App Service にマップする

このチュートリアルでは、既存の <abbr title="ドメイン レジストラー (GoDaddy など) から購入したドメイン名、または購入済みドメインのサブドメイン。">カスタム DNS ドメイン名を Azure App Service にマップする方法について説明します。</abbr> to <abbr title="Web アプリケーション、REST API、およびモバイル バックエンド アプリケーションをホストするための HTTP ベースのサービス。">Azure App Service</abbr>.

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 次を使用してサブドメインをマップする: <abbr title="DNS 正規名レコードによって、あるドメインが別のものにマップされます。">CNAME レコード</abbr>.
> * 次を使用してルート ドメインをマップする: <abbr title="DNS のアドレス レコードによって、ホスト名が IP アドレスにマップされます。">A レコード</abbr>.
> * CNAME レコードを使用してワイルドカード ドメインをマップする。
> * 既定の URL をカスタム ディレクトリにリダイレクトする。

<hr/> 

## <a name="1-prepare-your-environment"></a>1.環境を準備する

* [App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。
* 自分のカスタム ドメインの DNS レコードを編集できることを確認します。 カスタム ドメインをまだお持ちでない場合は、[App Service ドメイン](manage-custom-dns-buy-domain.md)を購入できます。

    <details>
        <summary>DNS レコードを編集するには何が必要ですか?</summary>
        自分のドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権が必要です。 たとえば、<code>contoso.com</code> と <code>www.contoso.com</code> の DNS エントリを追加するには、<code>contoso.com</code> ルート ドメインに対して DNS 設定を構成できる必要があります。
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. アプリの準備

カスタム DNS 名をアプリにマップするには、アプリの <abbr title="アプリをホストする Web サーバー ファームの場所、サイズ、機能を指定します。">App Service プラン</abbr> が有料レベル ( <abbr title="その他のアプリ (他の顧客のアプリなど) と同じ VM でアプリを実行する Azure App Service レベル。 このレベルは開発とテストを目的としています。">**Free (F1)** 以外) であることが必要です</abbr>). 詳細については、「[Azure App Service プランの概要](overview-hosting-plans.md)」を参照してください。

#### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

#### <a name="select-the-app-in-the-azure-portal"></a>Azure portal でアプリを選択します

1. **[App Services]** を検索して選択します。

   ![App Services の選択を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. **[App Services]** ページで、Azure アプリの名前を選択します。

   ![Azure アプリへのポータル ナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service アプリの管理ページが表示されます。

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>価格レベルの確認

1. アプリ ページの左側のウィンドウで、 **[設定]** セクションまでスクロールし、 **[スケール アップ (App Service プラン)]** を選択します。

   ![[スケール アップ (App Service プラン)] メニューを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **F1** レベルに含まれていないことを確認します。 カスタム DNS は、**F1** レベルではサポートされていません。

   ![[推奨される価格レベル] を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. App Service プランが **F1** レベルでない場合は、 **[スケール アップ]** ページを閉じて、「[3. ドメイン検証 ID を取得する](#3-get-a-domain-verification-id)」に進みます。

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

1. 非 Free レベルのいずれかを選びます (**D1**、**B1**、**B2**、**B3**、または **運用** カテゴリのいずれかのレベル)。 その他のオプションについては、 **[その他のオプションを参照する]** をクリックします。

1. **[適用]** を選択します。

   ![価格レベルの確認を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   次の通知が表示されたら、スケール操作は完了です。

   ![スケール操作の確認を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3.ドメイン検証 ID を取得する

アプリにカスタム ドメインを追加するには、ドメイン プロバイダーで検証 ID を TXT レコードとして追加して、ドメインの所有権を確認する必要があります。 

1. アプリ ページの左側のウィンドウで、 **[カスタム ドメイン]** を選択します。 
1. 次の手順のために、 **[カスタム ドメイン]** ページの **[カスタム ドメイン検証 ID]** をコピーしておきます。

    ![[カスタム ドメイン検証 ID] ボックスの ID を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>これが必要なのはなぜですか?</summary>
        ドメイン検証 ID をカスタム ドメインに追加すると、未解決の DNS エントリが回避され、サブドメインの乗っ取りを防ぐのに役立ちます。 前にこの検証 ID を使わずに構成したカスタム ドメインについては、DNS レコードに検証 ID を追加して、同じリスクからそれらを保護する必要があります。 この一般的な重大度の高い脅威の詳細については、<a href="/azure/security/fundamentals/subdomain-takeover">サブドメインの乗っ取り</a>に関するページを参照してください。
    </details>
    
<a name="info"></a>

3. **(A レコードのみ)** <abbr title="DNS のアドレス レコードによって、ホスト名が IP アドレスにマップされます。">A レコード</abbr>をマップするには、アプリの外部 IP アドレスが必要です。 **[カスタム ドメイン]** ページで、 **[IP アドレス]** の値をコピーします。

   ![Azure アプリへの portal ナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4.DNS レコードを作成する

1. ドメイン プロバイダーの Web サイトにサインインします。

    <details>
        <summary>Azure を使用して自社のドメイン プロバイダーの DNS を管理できますか?</summary>
        必要に応じて、Azure DNS を使用して自社のドメインの DNS レコードを管理したり、Azure App Service のカスタム DNS 名を構成したりできます。 詳しくは、「<a href="/azure/dns/dns-delegate-domain-azure-dns">チュートリアル: Azure DNS でドメインをホストする</a>」を参照してください。
    </details>

1. DNS レコードの管理ページを探します。 

    <details>
        <summary>このページを見つけるにはどうすればよいですか?</summary>
        <p>各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 <strong>[ドメイン名]</strong> 、 <strong>[DNS]</strong> 、 <strong>[ネーム サーバー管理]</strong> というラベルが付いたサイトの領域を探します。</p>
        <p>多くの場合、DNS レコードのページを見つけるには、アカウント情報を表示し、 <strong>[マイドメイン]</strong> などのリンクを探します。 そのページに移動し、 <strong>[ゾーン ファイル]</strong> 、 <strong>[DNS レコード]</strong> 、 <strong>[詳細構成]</strong> のような名前のリンクを探します。</p>
    </details>

   以下のスクリーンショットは、DNS レコード ページの例です。

   ![DNS レコード ページの例を示すスクリーンショット。](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. **[追加]** または適切なウィジェットを選択して、レコードを作成します。 

1. 作成するレコードの種類を選択して、手順に従います。 お客様は <abbr title="DNS の正規名レコードによって、あるドメイン名 (別名) が別のもの (正規名) にマップされます。">CNAME レコード</abbr> または <abbr title="DNS のアドレス レコードによって、ホスト名が IP アドレスにマップされます。">A レコード</abbr> を使用して、カスタム DNS 名を App Service にマップできます。 

    <details>
        <summary>どちらのレコードを使用すべきですか?</summary>
        <div>
            <ul>
            <li>ルート ドメイン (<code>contoso.com</code> など) をマップするには、A レコードを使用します。 CNAME レコードはルート レコードに使用しないようにしてください (詳しくは、<a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedia のエントリ</a>を参照してください)。</li>
            <li>サブドメイン (<code>www.contoso.com</code> など) をマップするには、CNAME レコードを使用します。</li>
            <li>A レコードを使用してサブドメインをアプリの IP アドレスに直接マップできますが、<a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP アドレスは変更される</a>可能性があります。 代わりに、変更の影響を受けにくい CNAME レコードでアプリのホスト名をマップします。</li>
            <li><a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">ワイルドカード ドメイン</a> (<code>*.contoso.com</code> など) をマップするには、CNAME レコードを使用します。</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

`www.contoso.com` の `www` のようなサブドメインについては、次の表に従って 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `<subdomain>` (例: `www`) | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid.<subdomain>` (例: `asuid.www`) | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

![Azure アプリへのポータルのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

`contoso.com` のようなルート ドメインについては、次の表に従って 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| A | `@` | 「[アプリの IP アドレスをコピーする](#3-get-a-domain-verification-id)」で取得した IP アドレス | ドメイン マッピング自体 (通常、`@` はルート ドメインを表します)。 |
| TXT | `asuid` | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 ルート ドメインの場合は、`asuid` を使用します。 |

![DNS レコード ページを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>サブドメインを A レコードにマップするにはどうすればよいですか?</summary>
推奨される CNAME レコードの代わりに A レコードを使用してサブドメイン (`www.contoso.com` など) をマップするには、A レコードと TXT レコードが次の表のようになっている必要があります。

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">表 3</caption>
<thead>
<tr>
<th>レコード タイプ</th>
<th>Host</th>
<th>値</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (例: <code>www</code>)</td>
<td>「<a href="#info" data-linktype="self-bookmark">アプリの IP アドレスをコピーする</a>」で取得した IP アドレス</td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (例: <code>asuid.www</code>)</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">前に取得した検証 ID</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[ワイルドカード (CNAME)](#tab/wildcard)

`*.contoso.com` の `*` のようなワイルドカード名については、次の表に従って 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid` | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

![Azure アプリへのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>ページから離れると、変更は消去されます。</summary>
<p>一部のプロバイダー (GoDaddy など) では、別の <strong>[変更を保存]</strong> リンクを選択するまで DNS レコードの変更が反映されません。</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5.アプリでマッピングを有効にする

1. Azure Portal のアプリ ページの左側に表示されるウィンドウで、 **[カスタム ドメイン]** を選択します。

    ![[カスタム ドメイン] メニューを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **[カスタム ドメインの追加]** を選択します。

    ![[ホスト名の追加] 項目を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. 先ほど CNAME レコードを追加した完全修飾ドメイン名 (`www.contoso.com` など) を入力します。

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが表示されます。

1. **[ホスト名レコード タイプ]** が **[CNAME (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![[カスタム ドメインの追加] ボタンを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    ![CNAME レコードの追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary><strong>セキュリティ保護なし</strong>という警告ラベルにはどのような意味がありますか?</summary>
        カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「<a href="/azure/app-service/configure-ssl-bindings">Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する</a>」を参照してください。
    </details>

    手順を行っていなかったり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

    ![検証エラーを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. 先ほど A レコードを構成した完全修飾ドメイン名 (`contoso.com` など) を入力します。 

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが開きます。

1. **[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    ![A レコードの追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary><strong>セキュリティ保護なし</strong>という警告ラベルにはどのような意味がありますか?</summary>
        カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「<a href="/azure/app-service/configure-ssl-bindings">Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する</a>」を参照してください。
    </details>
    
    手順を行っていなかったり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。
    
    ![検証エラーを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[ワイルドカード (CNAME)](#tab/wildcard)

3. ワイルドカード ドメインと一致する完全修飾ドメイン名を入力します。 たとえば、`*.contoso.com` という例の場合には、`sub1.contoso.com`、`sub2.contoso.com`、`*.contoso.com`、またはワイルドカードのパターンに一致するその他の文字列を使用できます。 次に、 **[検証]** を選択します。

    **[カスタム ドメインの追加]** ボタンがアクティブになります。

1. **[ホスト名レコード タイプ]** が **[CNAME レコード (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    <details>
        <summary><strong>セキュリティ保護なし</strong>という警告ラベルにはどのような意味がありますか?</summary>
        カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「<a href="/azure/app-service/configure-ssl-bindings">Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する</a>」を参照してください。
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6.ブラウザーでテストする

先ほど構成した DNS 名を参照します。

![Azure アプリへのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>HTTP 404 (Not Found) エラーが表示されます。</summary>
<ul>
<li>構成されているカスタム ドメインに A レコードまたは CNAME レコードがない。</li>
<li>クライアントのブラウザーが、ドメインの古い IP アドレスをキャッシュしている。 キャッシュをクリアして、DNS 解決をもう一度テストします。 Windows コンピューターでは、<code>ipconfig /flushdns</code> でキャッシュをクリアします。</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>アクティブなドメインの移行

ライブ サイトとその DNS ドメイン名を App Service にダウンタイムなしで移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](manage-custom-dns-migrate-domain.md)」をご覧ください。

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>カスタム ディレクトリにリダイレクトする

<details>
<summary>これは必要ですか?</summary>
<p>アプリによって異なります。 既定では、App Service は Web 要求をアプリ コードのルート ディレクトリに送信します。 しかし、特定の Web フレームワークはルート ディレクトリで開始されません。 たとえば、<a href="https://laravel.com/">Laravel</a> は <code>public</code> サブディレクトリで開始されます。 <code>contoso.com</code> の DNS の例を引き続き使用すると、このようなアプリには <code>http://contoso.com/public</code> でアクセスできますが、代わりに<code>http://contoso.com</code> を <code>public</code> ディレクトリにダイレクトするという方法もあります。 </p>
</details>

これは一般的なシナリオですが、実際はカスタム ドメイン マッピングに関わるものではなく、アプリ内の仮想ディレクトリのカスタマイズに関するものです。

1. Web アプリ ページの左側のペインで、 **[アプリケーション設定]** を選択します。

1. ページの下部でルート仮想ディレクトリ `/` が既定で `site\wwwroot` をポイントしていますが、これがお客様のアプリ コードのルート ディレクトリです。 たとえば、代わりに `site\wwwroot\public` をポイントするように変更して、変更内容を保存できます。

    ![仮想ディレクトリのカスタマイズを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. 操作の完了後、ブラウザーで自分のアプリのルート パス (例: `http://contoso.com` や `http://<app-name>.azurewebsites.net`) に移動して確認を行います。

<hr/> 

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/) を使用すると、カスタム ドメインの管理をスクリプトで自動化できます。

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

詳細については、「[カスタム ドメインを Web アプリにマップする](scripts/cli-configure-custom-domain.md)」を参照してください

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

詳細については、「[カスタム ドメインを Web アプリに割り当てる](scripts/powershell-configure-custom-domain.md)」を参照してください。

<hr/> 

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進み、カスタム TLS/SSL 証明書を Web アプリにバインドする方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)