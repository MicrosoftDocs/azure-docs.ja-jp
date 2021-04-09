---
title: Azure Static Web Apps にカスタム ドメインを設定する
description: カスタム ドメインを Azure Static Web Apps にマップする方法について説明します
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173683"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Azure Static Web Apps Preview にカスタム ドメインを設定する

Azure Static Web Apps では既定で、自動生成されたドメイン名が提供されます。 この記事では、Azure Static Web Apps アプリケーションにカスタム ドメイン名をマップする方法について説明します。

## <a name="prerequisites"></a>前提条件

- 購入済みのドメイン名
- ドメインの DNS 構成プロパティへのアクセス

ドメイン名を構成する際には、ルート ドメイン (`example.com` など) を IP アドレスにマップするために、"1 つの" レコードが使用されます。 DNS の仕様では、ドメイン間の相互のマッピングが許可されていないため、ルート ドメインは IP アドレスに直接マップされる必要があります。

## <a name="dns-configuration-options"></a>DNS の構成オプション

アプリケーションに使用できる DNS 構成には、いくつかの種類があります。

| 目的 | THEN |
|--|--|
| `www.example.com` または `blog.example.net` をサポートする | [CNAME レコードのマップ](#map-a-cname-record) |
| `example.com` をサポートします | [ルート ドメインの構成](#configure-a-root-domain) |
| `www.example.com` へのすべてのサブドメインを参照 | [ワイルドカードのマップ](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>CNAME レコードのマップ

CNAME レコードでは、あるドメインが別のドメインにマップされます。 CNAME レコードを使用して、Azure Static Web Apps によって提供される自動生成ドメインに `www.example.com`、`blog.example.com`、またはその他のサブドメインをマップできます。

1. [Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

1. **[Static Web Apps]** を検索して選択します

1. _[Static Web Apps]\(Static Web Apps\)_ ページで、アプリの名前を選択します。

1. メニューで **[カスタム ドメイン]** をクリックします。

1. **[追加]** ボタンをクリックします。

1. _[カスタム ドメイン]_ ウィンドウで、 **[値]** フィールドに URL をコピーします。

### <a name="configure-dns-provider"></a>DNS プロバイダーを構成する

1. ドメイン プロバイダーの Web サイトにサインインします。

2. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

3. 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[ドメイン]** などのリンクを探します。 そのページに移動して、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

    以下のスクリーンショットは、DNS レコード ページの例です。

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="サンプルの DNS プロバイダー構成":::

4. 次の値を使用して、新しい **CNAME** レコードを作成します

    | 設定             | 値                     |
    | ------------------- | ------------------------- |
    | Type                | CNAME                     |
    | Host                | www                       |
    | 値               | クリップボードから貼り付ける |
    | TTL (該当する場合) | 既定値のままにする    |

5. DNS プロバイダーに変更内容を保存します。

### <a name="validate-cname"></a>CNAME を検証する

1. Azure portal 上の _[カスタム ドメイン]_ ウィンドウに戻ります。

1. _[カスタム ドメインの検証]_ セクションに、`www` 部分を含めてドメインを入力します。

1. **[検証]** ボタンをクリックします。

これで、カスタム ドメインが構成されました。DNS プロバイダーによって世界規模で変更が反映されるには、数時間かかる場合があります。 [dnspropagation.net](https://dnspropagation.net) に移動して、反映の状態を確認できます。 `www` を含めてお使いのカスタム ドメインを入力し、ドロップダウンから [CNAME] を選択して、 **[開始]** を選択します。

DNS の変更が設定されると、Web サイトでは Static Web App の自動生成された URL (_random-name-123456789c.azurestaticapps.net_ など) が返されます。

## <a name="configure-a-root-domain"></a>ルート ドメインを構成する

ルート ドメインとは、ドメインから `www`を含めてすべてのサブドメインを差し引いたものです。 たとえば、`www.example.com` のルート ドメインは `example.com` です。 これは "APEX" ドメインとも呼ばれます。

プレビュー期間中はルート ドメインのサポートは利用できませんが、Static Web App を使用してルート ドメインのサポートを構成する方法の詳細については、[Azure Static Web Apps でのルート ドメインの構成](https://burkeholland.github.io/posts/static-app-root-domain)に関するブログ投稿で確認できます。

## <a name="map-a-wildcard-domain"></a>ワイルドカード ドメインのマップ

状況によっては、サブドメインに送信されたすべてのトラフィックを、別のドメインにルーティングしたい場合があります。 一般的な例としては、すべてのサブドメイン トラフィックを `www.example.com` にマッピングする場合があります。 これにより、`www.example.com` ではなく `w.example.com` と入力した場合でも、要求が `www.example.com` に送信されます。

### <a name="configure-dns-provider"></a>DNS プロバイダーを構成する

1. ドメイン プロバイダーの Web サイトにサインインします。

2. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

3. 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[ドメイン]** などのリンクを探します。 そのページに移動して、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

    以下のスクリーンショットは、DNS レコード ページの例です。

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="サンプルの DNS プロバイダー構成":::

4. 次の値を使用して新しい **CNAME** レコードを作成し、`www.example.com` をカスタム ドメイン名に置き換えます。

    | 設定 | 値                  |
    | ------- | ---------------------- |
    | Type    | CNAME                  |
    | Host    | \*                     |
    | 値   | www.example.com        |
    | TTL     | 既定値のままにする |

5. DNS プロバイダーに変更内容を保存します。

これで、ワイルドカード ドメインが構成されました。世界規模で変更が反映されるには、数時間かかる場合があります。 [dnspropagation.net](https://dnspropagation.net) に移動して、反映の状態を確認できます。 (`www` 以外の) サブドメインをすべて含めてお使いのドメインのカスタム ドメインを入力し、ドロップダウンから [CNAME] を選択して、 **[開始]** を選択します。

DNS の変更が設定されると、Web サイトでは、Static Web App 用に構成されたカスタム ドメイン (`www.example.com` など) が返されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](application-settings.md)
