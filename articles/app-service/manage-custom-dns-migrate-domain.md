---
title: アクティブな DNS 名の移行 - Azure App Service | Microsoft Docs
description: 既にライブ サイトに割り当てられているカスタム DNS ドメイン名を、ダウンタイムを発生させずに Azure App Service に移行する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999091"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Azure App Service へのアクティブな DNS 名の移行

この記事では、ダウンタイムを発生させずにアクティブな DNS 名を [Azure App Service](../app-service/overview.md) に移行する方法について説明します。

ライブ サイトとその DNS ドメイン名を App Service に移行するとき、その DNS 名は既にライブ トラフィックを配信しています。 移行中の DNS 解決のダウンタイムを回避するには、アクティブな DNS 名をプリエンプティブに App Service アプリにバインドします。

DNS 解決のダウンタイムの心配がない場合は、[Azure App Service への既存のカスタム DNS 名のマッピング](app-service-web-tutorial-custom-domain.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

この手順を完了するには、以下が必要です。

- [App Service アプリが Free レベルに含まれていないことを確認します](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>ドメイン名の事前バインド

カスタム ドメインを事前にバインドする場合は、DNS レコードに変更を加える前に、次に示す両方の作業を実行します。

- ドメインの所有権を検証する
- アプリのドメイン名を有効にする

最終的に古いサイトから App Service アプリにカスタム DNS 名を移行する際、DNS 解決のダウンタイムは発生しません。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>ドメイン確認レコードを作成する

ドメインの所有権を確認するには、TXT レコードを追加します。 TXT レコードは、_awverify.&lt;subdomain>_ から _&lt;appname>.azurewebsites.net_ にマップします。 

必要な TXT レコードは、移行する DNS レコードによって異なります。 例については、次の表をご覧ください (通常、`@` はルート ドメインを表します)。

| DNS レコードの例 | TXT ホスト | TXT 値 |
| - | - | - |
| \@ (ルート) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (サブ) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (ワイルドカード) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

DNS レコードのページで、移行する DNS 名のレコード タイプを書き留めておきます。 App Service では、CNAME レコードおよび A レコードからのマッピングをサポートしています。

> [!NOTE]
> CloudFlare などの一部のプロバイダーの場合、`awverify.*` は有効なレコードではありません。 代わりに `*` だけを使用してください。

> [!NOTE]
> ワイルドカード `*` レコードでは、既存の CNAME レコードでサブドメインが検証されません。 サブドメインごとに TXT レコードを明示的に作成することが必要な場合があります。


### <a name="enable-the-domain-for-your-app"></a>アプリのドメインを有効にする

[Azure Portal](https://portal.azure.com) で、アプリ ページの左側のナビゲーションにある **[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、**[ホスト名の追加]** の横にある **+** アイコンをクリックします。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

先ほど TXT レコードを追加した完全修飾ドメイン名 (`www.contoso.com` など) を入力します。 ワイルドカードを使ったドメイン (\*.contoso.com など) の場合は、一致する任意の DNS 名を使用できます。 

**[検証]** を選択します。

**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が、移行する DNS レコード タイプと一致していることを確認します。

**[ホスト名の追加]** を選択します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

カスタム DNS 名が Azure アプリで有効になります。 

## <a name="remap-the-active-dns-name"></a>アクティブな DNS 名の再マップ

最後の手順では、アクティブな DNS レコードが App Service をポイントするよう再マップします。 現時点では、DNS レコードはまだ古いサイトをポイントしています。

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>アプリの IP アドレスをコピーします (A レコードのみ)

CNAME レコードを再マップしている場合、このセクションは省略します。 

A レコードを再マップするには、**[カスタム ドメイン]** ページに示されている App Service アプリの外部 IP アドレスが必要です。

右上隅の **[X]** をクリックして **[ホスト名の追加]** ページを閉じます。 

**[カスタム ドメイン]** ページで、アプリの IP アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>DNS レコードの更新

ドメイン プロバイダーの DNS レコードのページに戻り、再マップする DNS レコードを選択します。

`contoso.com` ルート ドメインの例では、A レコードまたは CNAME レコードを、次の表の例に示すように再マップします。 

| FQDN の例 | レコード タイプ | Host | 値 |
| - | - | - | - |
| contoso.com (ルート) | A | `@` | 「[アプリの IP アドレスをコピーする](#info)」で取得した IP アドレス |
| www\.contoso.com (サブ) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (ワイルドカード) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

設定を保存します。

DNS の伝播が始まるとすぐに、DNS クエリが App Service アプリの解決を開始します。

## <a name="next-steps"></a>次の手順

カスタム SSL 証明書を App Service にバインドする方法を確認します。

> [!div class="nextstepaction"]
> [既存のカスタム SSL 証明書を Azure App Service にバインドする](app-service-web-tutorial-custom-ssl.md)
