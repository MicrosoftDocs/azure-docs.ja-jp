---
title: チュートリアル:既存のカスタム DNS 名をマップする
description: 既存のカスタム DNS ドメイン名 (バニティ ドメイン) を、Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリに追加する方法について説明します。
keywords: App Service, Azure App Service, ドメイン マッピング, ドメイン名, 既存のドメイン, ホスト名
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 4a2c65e2685dada6412adf8c8ad9c63f472b91e8
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962283"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>チュートリアル:既存のカスタム DNS 名を Azure App Service にマップする

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、既存のカスタム DNS 名を Azure App Service にマップする方法について説明します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメイン (たとえば `www.contoso.com`) をマップする
> * A レコードを使用してルート ドメイン (たとえば `contoso.com`) をマップする
> * CNAME レコードを使用してワイルドカード ドメイン (たとえば `*.contoso.com`) をマップする
> * 既定の URL をカスタム ディレクトリにリダイレクトする
> * スクリプトでドメイン マッピングを自動化する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。
* ドメイン名を購入し、ドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権があることを確認します。

  たとえば、`contoso.com` と `www.contoso.com` の DNS エントリを追加するには、`contoso.com` ルート ドメインに対して DNS 設定を構成できる必要があります。

  > [!NOTE]
  > 既存のドメイン名がない場合は、[Azure Portal を使用してドメインを購入する](manage-custom-dns-buy-domain.md)ことを検討してください。

## <a name="prepare-the-app"></a>アプリの準備

Web アプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (Azure Functions の **Shared**、**Basic**、**Standard**、**Premium**、または **従量課金**) である必要があります。 この手順では、App Service アプリがサポートされている価格レベルであることを確認します。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

### <a name="select-the-app-in-the-azure-portal"></a>Azure portal でアプリを選択します

**[App Services]** を検索して選択します。

![[App Services] を選択します](./media/app-service-web-tutorial-custom-domain/app-services.png)

**[App Services]** ページで、Azure アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service アプリの管理ページが表示されます。  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>価格レベルの確認

アプリ ページの左側のナビゲーションで、 **[設定]** セクションまでスクロールし、 **[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **F1** レベルに含まれていないことを確認します。 カスタム DNS は、**F1** レベルではサポートされていません。 

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service プランが **F1** レベルではない場合は、 **[スケール アップ]** ページを閉じて、「[CNAME レコードのマップ](#cname)」に進みます。

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

非 Free レベルのいずれかを選びます (**D1**、**B1**、**B2**、**B3**、または**運用**カテゴリのいずれかのレベル)。 その他のオプションについては、 **[See additional options]\(その他のオプションを参照する\)** をクリックします。

**[Apply]** をクリックします。

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール操作の確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-domain-verification-id"></a>ドメイン検証 ID を取得する

アプリにカスタム ドメインを追加するには、ドメイン プロバイダーで検証 ID を TXT レコードとして追加して、ドメインの所有権を確認する必要があります。 アプリ ページの左側のナビゲーションで、 **[カスタム ドメイン]** をクリックします。 次のステップのために、 **[カスタム ドメイン]** ページの **[Custom Domain Verification ID]\(カスタム ドメイン検証 ID\)** をコピーしておきます。

![カスタム ドメイン検証 ID を取得する](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> ドメイン検証 ID をカスタム ドメインに追加すると、未解決の DNS エントリが回避され、サブドメインの乗っ取りを防ぐことができます。 この一般的な重大度の高い脅威の詳細については、[サブドメインの乗っ取り](../security/fundamentals/subdomain-takeover.md)に関するページを参照してください。

## <a name="map-your-domain"></a>ドメインをマップする

**CNAME レコード**または **A レコード**のいずれかを使用して、カスタム DNS 名を App Service にマップします。 それぞれに対応する手順を実行します。

- [CNAME レコードのマップ](#map-a-cname-record)
- [A レコードのマップ](#map-an-a-record)
- [ワイルドカード ドメインのマップ (CNAME レコードを使用)](#map-a-wildcard-domain)

> [!NOTE]
> ルート ドメインを除くすべてのカスタム DNS 名 (たとえば、`contoso.com`) には CNAME レコードを使用する必要があります。 ルート ドメインの場合は A レコードを使用します。

### <a name="map-a-cname-record"></a>CNAME レコードのマップ

このチュートリアルの例では、`www` サブドメイン (たとえば `www.contoso.com`) の CNAME レコードを追加します。

`www` 以外のサブドメインがある場合は、`www` を自分のサブドメインに置き換えます (たとえば、カスタム ドメインが `sub.constoso.com` の場合は、`sub` に置き換えます)。

#### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME レコードを作成する

サブドメインをアプリの既定のドメイン名 (`<app-name>.azurewebsites.net`、`<app-name>` はアプリの名前) にマップします。 `www` サブドメインの CNAME マッピングを作成するには、次の 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid.www` | [前に取得した検証 ID](#get-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

CNAME レコードと TXT レコードを追加した後の DNS レコード ページは次の例のようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure で CNAME レコード マッピングを有効にする

1. Azure Portal のアプリ ページの左側のナビゲーションで、 **[カスタム ドメイン]** を選択します。

    ![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. アプリの **[カスタム ドメイン]** ページで、完全修飾カスタム DNS 名 (`www.contoso.com`) を一覧に追加します。

1. **[カスタム ドメインの追加]** を選択します。

    ![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 先ほど CNAME レコードを追加した完全修飾ドメイン名 (`www.contoso.com` など) を入力します。

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが開きます。

1. **[ホスト名レコード タイプ]** が **[CNAME (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

    ![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、それがまだ TLS/SSL 証明書にバインドされていないことを意味し、ブラウザーによっては、ブラウザーからカスタム ドメインへの HTTPS 要求でエラーまたは警告が発生します。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。

    手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

    ![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A レコードのマップ

このチュートリアルの例では、ルート ドメイン (たとえば `contoso.com`) の A レコードを追加します。

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>アプリの IP アドレスをコピーする

A レコードをマップするには、アプリの外部 IP アドレスが必要です。 この IP アドレスは、Azure Portal のアプリの **[カスタム ドメイン]** ページで見つけることができます。

Azure Portal のアプリ ページの左側のナビゲーションで、 **[カスタム ドメイン]** を選択します。

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、アプリの IP アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>A レコードを作成する

A レコードをアプリ (通常はルート ドメイン) にマップするには、次の 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| A | `@` | 「[アプリの IP アドレスをコピーする](#info)」で取得した IP アドレス | ドメイン マッピング自体 (通常、`@` はルート ドメインを表します)。 |
| TXT | `asuid` | [前に取得した検証 ID](#get-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 ルート ドメインの場合は、`asuid` を使用します。 |

> [!NOTE]
> 推奨される [CNAME レコード](#map-a-cname-record)の代わりに A レコードを使用してサブドメイン (`www.contoso.com`など) を追加するには、A レコードと TXT レコードが次の表のようになっている必要があります。
>
> | レコード タイプ | Host | 値 |
> | - | - | - |
> | A | `www` | 「[アプリの IP アドレスをコピーする](#info)」で取得した IP アドレス |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

レコードが追加されると、DNS レコード ページは次の例のようになります。

![DNS レコード ページ](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>アプリの A レコード マッピングを有効にする

Azure Portal のアプリの **[カスタム ドメイン]** ページに戻り、完全修飾カスタム DNS 名 (たとえば、`contoso.com`) を一覧に追加します。

1. **[カスタム ドメインの追加]** を選択します。

    ![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 先ほど A レコードを構成した完全修飾ドメイン名 (`contoso.com` など) を入力します。 

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが開きます。

1. **[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

    ![追加された A レコード](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、それがまだ TLS/SSL 証明書にバインドされていないことを意味し、ブラウザーによっては、ブラウザーからカスタム ドメインへの HTTPS 要求でエラーまたは警告が発生します。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。
    
    手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。
    
    ![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>ワイルドカード ドメインのマップ

このチュートリアルの例では、CNAME レコードを追加して、[ワイルドカード DNS 名](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (たとえば `*.contoso.com`) を App Service アプリにマップします。

#### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME レコードを作成する

ワイルドカード名 `*` をアプリの既定のドメイン名 (`<app-name>.azurewebsites.net`、`<app-name>` はアプリの名前) にマップします。 ワイルドカード名をマップするには、次の 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid` | [前に取得した検証 ID](#get-domain-verification-id) | App Service は、`asuid` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

`*.contoso.com` ドメインの例では、CNAME レコードは名前 `*` を `<app-name>.azurewebsites.net` にマップします。

CNAME が追加されると、DNS レコード ページは次の例のようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>アプリの CNAME レコード マッピングを有効にする

これで、ワイルドカード名と一致するすべてのサブドメインをアプリに追加できるようになりました (たとえば、`sub1.contoso.com`、`sub2.contoso.com`、`*.contoso.com` は、いずれも `*.contoso.com` に一致します)。

1. Azure Portal のアプリ ページの左側のナビゲーションで、 **[カスタム ドメイン]** を選択します。

    ![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **[カスタム ドメインの追加]** を選択します。

    ![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. ワイルドカード ドメインと一致する完全修飾ドメイン名 (たとえば `sub1.contoso.com`) を入力し、 **[検証]** を選択します。

    **[カスタム ドメインの追加]** ボタンがアクティブになります。

1. **[ホスト名レコード タイプ]** が **[CNAME レコード (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

1. **+** アイコンをもう一度選択して、ワイルドカード ドメインと一致する別のカスタム ドメインを追加します。 たとえば、`sub2.contoso.com` を追加します。

    ![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、それがまだ TLS/SSL 証明書にバインドされていないことを意味し、ブラウザーによっては、ブラウザーからカスタム ドメインへの HTTPS 要求でエラーまたは警告が発生します。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。
    
## <a name="test-in-browser"></a>ブラウザーでテストする

先ほど構成した DNS 名 (たとえば、`contoso.com`、`www.contoso.com`、`sub1.contoso.com`、および `sub2.contoso.com`) を参照します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404 "Not Found" (見つかりません) を解決する

カスタム ドメインの URL 参照時に HTTP 404 (Not Found) エラーが発生した場合は、<a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> を使用して、お客様のドメインがアプリの IP アドレスに解決されることを確認します。 解決されない場合、次のいずれかの理由が考えられます。

- 構成されているカスタム ドメインに A レコードまたは CNAME レコード (またはその両方) がない。
- クライアントのブラウザーが、ドメインの古い IP アドレスをキャッシュしている。 キャッシュをクリアして、DNS の解決をもう一度テストします。 Windows コンピューターでは、`ipconfig /flushdns` でキャッシュをクリアします。

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>アクティブなドメインの移行

ライブ サイトとその DNS ドメイン名を App Service にダウンタイムなしで移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](manage-custom-dns-migrate-domain.md)」をご覧ください。

## <a name="redirect-to-a-custom-directory"></a>カスタム ディレクトリにリダイレクトする

既定では、App Service は Web 要求をアプリ コードのルート ディレクトリに送信します。 ただし、特定の Web フレームワークはルート ディレクトリで開始されません。 たとえば、[Laravel](https://laravel.com/) は `public` サブディレクトリで開始されます。 `contoso.com` の DNS の例を継続する場合、そのようなアプリには `http://contoso.com/public` でアクセス可能ですが、実際は `http://contoso.com` を `public` ディレクトリに転送したいと考えます。 この手順に DNS の解決は含まれませんが、仮想ディレクトリのカスタマイズは含まれます。

これを行うには、Web アプリ ページの左側のナビゲーションで **[アプリケーション設定]** を選択します。 

ページの下部でルート仮想ディレクトリ `/` が既定で `site\wwwroot` をポイントしていますが、これがお客様のアプリ コードのルート ディレクトリです。 たとえば、代わりに `site\wwwroot\public` をポイントするように変更して、変更内容を保存できます。

![仮想ディレクトリのカスタマイズ](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

操作が完了すると、アプリからルート パスの正しいページが返されます (`http://contoso.com` など)。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/) を使用すると、カスタム ドメインの管理をスクリプトで自動化できます。 

### <a name="azure-cli"></a>Azure CLI 

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。 

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

詳細については、「[カスタム ドメインを Web アプリにマップする](scripts/cli-configure-custom-domain.md)」を参照してください

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

詳細については、「[カスタム ドメインを Web アプリに割り当てる](scripts/powershell-configure-custom-domain.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメインをマップする
> * A レコードを使用してルート ドメインをマップする
> * CNAME レコードを使用してワイルドカード ドメインをマップする
> * 既定の URL をカスタム ディレクトリにリダイレクトする
> * スクリプトでドメイン マッピングを自動化する

次のチュートリアルに進み、カスタム TLS/SSL 証明書を Web アプリにバインドする方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)