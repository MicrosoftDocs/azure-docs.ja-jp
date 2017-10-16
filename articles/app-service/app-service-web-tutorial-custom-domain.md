---
title: "既存のカスタム DNS 名を Azure Web Apps にマップする | Microsoft Docs"
description: "既存のカスタム DNS ドメイン名 (バニティ ドメイン) を、Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリに追加する方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ac105b82b9bc25e6cc9f5cdf21f34400a09b3934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>既存のカスタム DNS 名を Azure Web Apps にマップする

[Azure Web Apps](app-service-web-overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、既存のカスタム DNS 名を Azure Web Apps にマップする方法について説明します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメイン (たとえば `www.contoso.com`) をマップする
> * A レコードを使用してルート ドメイン (たとえば `contoso.com`) をマップする
> * CNAME レコードを使用してワイルドカード ドメイン (たとえば `*.contoso.com`) をマップする
> * スクリプトでドメイン マッピングを自動化する

**CNAME レコード**または **A レコード**のいずれかを使用して、カスタム DNS 名を App Service にマップします。 

> [!NOTE]
> ルート ドメイン (`contoso.com` など) を除くすべてのカスタム DNS 名に CNAME を使用することをお勧めします。

ライブ サイトとその DNS ドメイン名を App Service に移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](app-service-custom-domain-name-migrate.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [App Service アプリを作成する](/azure/app-service/)か、別のチュートリアルで作成したアプリを使用します。
* ドメイン名を購入し、ドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権があることを確認します。

  たとえば、`contoso.com` と `www.contoso.com` の DNS エントリを追加するには、`contoso.com` ルート ドメインに対して DNS 設定を構成できる必要があります。

  > [!NOTE]
  > 既存のドメイン名がない場合は、[Azure Portal を使用してドメインを購入する](custom-dns-web-site-buydomains-web-app.md)ことを検討してください。 

## <a name="prepare-the-app"></a>アプリの準備

Web アプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (**Shared**、**Basic**、**Standard**、または **Premium**) である必要があります。 この手順では、App Service アプリがサポートされている価格レベルであることを確認します。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal でアプリに移動する

左側のメニューで、**[App Services]** を選択し、アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service アプリの管理ページが表示されます。  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>価格レベルの確認

アプリ ページの左側のナビゲーションで、**[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **Free** レベルに含まれていないことを確認します。 カスタム DNS は、**Free** レベルではサポートされていません。 

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service プランが **Free** ではない場合は、**[価格レベルの選択]** ページを閉じて、[[Map a CNAME record]\(CNAME レコードをマップ\)](#cname) にスキップします。

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

Free 以外のレベル (**Shared**、**Basic**、**Standard**、または **Premium**) を選択します。 

**[選択]**をクリックします。

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール操作の確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>CNAME レコードのマップ

このチュートリアルの例では、`www` サブドメイン (たとえば `www.contoso.com`) の CNAME レコードを追加します。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>CNAME レコードを作成する

サブドメインをアプリの既定のホスト名 (`<app_name>.azurewebsites.net`、`<app_name>` はアプリの名前) にマップするための CNAME レコードを追加します。

`www.contoso.com` ドメインの例では、名前 `www` を `<app_name>.azurewebsites.net` にマップする CNAME レコードを追加します。

CNAME を追加した後の DNS レコード ページは次の例のようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure で CNAME レコード マッピングを有効にする

Azure Portal のアプリ ページの左側のナビゲーションで、**[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

アプリの **[カスタム ドメイン]** ページで、完全修飾カスタム DNS 名 (`www.contoso.com`) を一覧に追加します。

**[ホスト名の追加]** の横の **+** アイコンを選択します。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

先ほど CNAME レコードを追加した完全修飾ドメイン名 (`www.contoso.com` など) を入力します。 

**[検証]** を選択します。

**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[CNAME (www.example.com または任意のサブドメイン)]** に設定されていることを確認します。

**[ホスト名の追加]** を選択します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>A レコードのマップ

このチュートリアルの例では、ルート ドメイン (たとえば `contoso.com`) の A レコードを追加します。 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>アプリの IP アドレスをコピーする

A レコードをマップするには、アプリの外部 IP アドレスが必要です。 この IP アドレスは、Azure Portal のアプリの **[カスタム ドメイン]** ページで見つけることができます。

Azure Portal のアプリ ページの左側のナビゲーションで、**[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、アプリの IP アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>A レコードを作成する

A レコードをアプリにマップする場合、App Service では **2 つ**の DNS レコードが必要になります。

- アプリの IP アドレスにマップするための **A** レコード。
- アプリの既定のホスト名 `<app_name>.azurewebsites.net` にマップするための **TXT** レコード。 App Service は、このレコードを、カスタム ドメインの所有者であることを検証するために構成時にのみ使用します。 App Service でカスタム ドメインが検証されて構成された後は、この TXT レコードを削除できます。 

`contoso.com` ドメインの場合、次の表に従って A および TXT レコードを作成します (`@` は、通常、ルート ドメインを表します)。 

| レコード タイプ | Host | 値 |
| - | - | - |
| A | `@` | 「[アプリの IP アドレスをコピーする](#info)」で取得した IP アドレス |
| TXT | `@` | `<app_name>.azurewebsites.net` |

レコードが追加されると、DNS レコード ページは次の例のようになります。

![DNS レコード ページ](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>アプリの A レコード マッピングを有効にする

Azure Portal のアプリの **[カスタム ドメイン]** ページに戻り、完全修飾カスタム DNS 名 (たとえば、`contoso.com`) を一覧に追加します。

**[ホスト名の追加]** の横の **+** アイコンを選択します。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

先ほど A レコードを構成した完全修飾ドメイン名 (`contoso.com` など) を入力します。

**[検証]** を選択します。

**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** を選択します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![追加された A レコード](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>ワイルドカード ドメインのマップ

このチュートリアルの例では、CNAME レコードを追加して、[ワイルドカード DNS 名](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (たとえば `*.contoso.com`) を App Service アプリにマップします。 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>CNAME レコードを作成する

ワイルドカード名をアプリの既定のホスト名 (`<app_name>.azurewebsites.net`) にマップするための CNAME レコードを追加します。

`*.contoso.com` ドメインの例では、CNAME レコードは名前 `*` を `<app_name>.azurewebsites.net` にマップします。

CNAME が追加されると、DNS レコード ページは次の例のようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>アプリの CNAME レコード マッピングを有効にする

これで、ワイルドカード名と一致するすべてのサブドメインをアプリに追加できるようになりました (たとえば、`sub1.contoso.com` および `sub2.contoso.com` は `*.contoso.com` に一致します)。 

Azure Portal のアプリ ページの左側のナビゲーションで、**[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[ホスト名の追加]** の横の **+** アイコンを選択します。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

ワイルドカード ドメインと一致する完全修飾ドメイン名 (たとえば `sub1.contoso.com`) を入力し、**[検証]** を選択します。

**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[CNAME レコード (www.example.com または任意のサブドメイン)]** に設定されていることを確認します。

**[ホスト名の追加]** を選択します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

**+** アイコンをもう一度選択して、ワイルドカード ドメインと一致する別のホスト名を追加します。 たとえば、`sub2.contoso.com` を追加します。

![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>ブラウザーでテストする

先ほど構成した DNS 名 (たとえば、`contoso.com`、`www.contoso.com`、`sub1.contoso.com`、および `sub2.contoso.com`) を参照します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、カスタム ドメインの管理をスクリプトで自動化できます。 

### <a name="azure-cli"></a>Azure CLI 

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resource_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

詳細については、「[カスタム ドメインを Web アプリにマップする](scripts/app-service-cli-configure-custom-domain.md)」を参照してください 

### <a name="azure-powershell"></a>Azure PowerShell 

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

詳細については、「[カスタム ドメインを Web アプリに割り当てる](scripts/app-service-powershell-configure-custom-domain.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメインをマップする
> * A レコードを使用してルート ドメインをマップする
> * CNAME レコードを使用してワイルドカード ドメインをマップする
> * スクリプトでドメイン マッピングを自動化する

次のチュートリアルに進み、カスタム SSL 証明書を Web アプリにバインドする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム SSL 証明書を Azure Web Apps にバインドする](app-service-web-tutorial-custom-ssl.md)
