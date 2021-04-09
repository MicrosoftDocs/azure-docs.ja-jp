---
title: アクティブな DNS 名を移行する
description: 既にライブ サイトに割り当てられているカスタム DNS ドメイン名を、ダウンタイムを発生させずに Azure App Service に移行する方法について説明します。
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89484302"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Azure App Service へのアクティブな DNS 名の移行

この記事では、ダウンタイムを発生させずにアクティブな DNS 名を [Azure App Service](../app-service/overview.md) に移行する方法について説明します。

ライブ サイトとその DNS ドメイン名を App Service に移行するとき、その DNS 名は既にライブ トラフィックを配信しています。 移行中の DNS 解決のダウンタイムを回避するには、アクティブな DNS 名をプリエンプティブに App Service アプリにバインドします。

DNS 解決のダウンタイムの心配がない場合は、[Azure App Service への既存のカスタム DNS 名のマッピング](app-service-web-tutorial-custom-domain.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

この手順を完了するには、以下が必要です。

- [App Service アプリが Free レベルに含まれていないことを確認します](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>ドメイン名の事前バインド

カスタム ドメインを事前にバインドする場合は、既存の DNS レコードに変更を加える前に、次に示す両方の作業を実行します。

- ドメインの所有権を検証する
- アプリのドメイン名を有効にする

最終的に古いサイトから App Service アプリにカスタム DNS 名を移行する際、DNS 解決のダウンタイムは発生しません。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>ドメイン検証 ID を取得する

「[ドメイン検証 ID を取得する](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)」の手順に従って、アプリのドメイン検証 ID を取得します。

### <a name="create-domain-verification-record"></a>ドメイン確認レコードを作成する

ドメインの所有権を確認するには、ドメイン確認用の TXT レコードを追加します。 TXT レコードのホスト名は、マップする DNS レコードの種類によって異なります。 次の表をご覧ください (通常、`@` はルート ドメインを表します)。

| DNS レコードの例 | TXT ホスト | TXT 値 |
| - | - | - |
| \@ (ルート) | _asuid_ | [アプリのドメイン検証 ID](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (サブ) | _asuid.www_ | [アプリのドメイン検証 ID](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* (ワイルドカード) | _asuid_ | [アプリのドメイン検証 ID](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

DNS レコードのページで、移行する DNS 名のレコード タイプを書き留めておきます。 App Service では、CNAME レコードおよび A レコードからのマッピングをサポートしています。

> [!NOTE]
> ワイルドカード `*` レコードでは、既存の CNAME レコードでサブドメインが検証されません。 サブドメインごとに TXT レコードを明示的に作成することが必要な場合があります。

### <a name="enable-the-domain-for-your-app"></a>アプリのドメインを有効にする

1. [Azure Portal](https://portal.azure.com) で、アプリ ページの左側のナビゲーションにある **[カスタム ドメイン]** を選択します。 

    ![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **[カスタム ドメイン]** ページで、 **[カスタム ドメインの追加]** を選択します。

    ![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. `contoso.com`、`www.contoso.com`、`*.contoso.com`など、作成する TXT レコードに対応する、移行したい完全修飾ドメイン名を入力します。 **[検証]** を選択します。

    **[カスタム ドメインの追加]** ボタンがアクティブになります。 

1. **[ホスト名レコード タイプ]** が、移行する DNS レコード タイプと一致していることを確認します。 **[ホスト名の追加]** を選択します。

    ![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

    ![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    カスタム DNS 名が Azure アプリで有効になります。 

## <a name="remap-the-active-dns-name"></a>アクティブな DNS 名の再マップ

最後の手順では、アクティブな DNS レコードが App Service をポイントするよう再マップします。 現時点では、DNS レコードはまだ古いサイトをポイントしています。

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>アプリの IP アドレスをコピーします (A レコードのみ)

CNAME レコードを再マップしている場合、このセクションは省略します。 

A レコードを再マップするには、 **[カスタム ドメイン]** ページに示されている App Service アプリの外部 IP アドレスが必要です。

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

## <a name="migrate-domain-from-another-app"></a>別のアプリからドメインを移行する

Azure でのアクティブなカスタム ドメインは、サブスクリプション間または同じサブスクリプション内で移行できます。 ただし、ダウンタイムを伴わない移行にはソース アプリが必要であり、特定の時点で同じカスタム ドメインにターゲット アプリが割り当てられます。 そのため、2 つのアプリが同じ展開単位 (内部的には、Web スペースとして知られています) には展開されないようにする必要があります。 1 つのドメイン名は、展開単位ごとに 1 つのアプリにのみ割り当てできます。

FTP/S URL `<deployment-unit>.ftp.azurewebsites.windows.net` のドメイン名を確認することで、アプリの展開単位がわかります。 ソース アプリとターゲット アプリ間で展開単位が必ず異なることを確認してください。 アプリの展開単位は、それ自体が含まれている [App Service プラン](overview-hosting-plans.md)によって決まります。 プランを作成するときに Azure によってランダムに選択され、変更することはできません。 Azure では、[同じリソース グループ *かつ* 同じリージョン内に作成する](app-service-plan-manage.md#create-an-app-service-plan)場合、2 つのプランが同じ展開単位内にあることだけを確認し、異なる展開単位にプランがあることを確認するロジックは用意されていません。 異なる展開単位にプランを作成する唯一の方法は、別の展開単位を取得するまで、新しいリソース グループまたはリージョン内にプランを作成し続けることです。

## <a name="next-steps"></a>次のステップ

カスタム TLS/SSL 証明書を App Service にバインドする方法を確認します。

> [!div class="nextstepaction"]
> [Azure App Service で TLS バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)
