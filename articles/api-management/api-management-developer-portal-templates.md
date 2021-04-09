---
title: API Management 開発者ポータルをテンプレートを使用してカスタマイズする
titleSuffix: Azure API Management
description: Azure API Management 開発者ポータルをテンプレートを使用してカスタマイズする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092887"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Azure API Management 開発者ポータルをテンプレートを使用してカスタマイズする方法

Azure API Management で開発者ポータルをカスタマイズする基本的な方法は 3 つあります。

* [静的なページの内容とページ レイアウト要素を編集する][modify-content-layout]
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles]
* [ポータルで生成されるページに使用するテンプレートを変更する][portal-templates] (このガイドで説明します)

テンプレートは、システムで生成された開発者ポータル ページのコンテンツ (API ドキュメント、製品、ユーザー認証など) をカスタマイズするために使用します。 [DotLiquid](http://dotliquidmarkup.org/) 構文と、用意されているローカライズされた文字列リソース、アイコン、およびページ コントロールのセットを使用して、表示されるページの内容を自由に構成できます。

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>開発者ポータル テンプレートの概要

テンプレートの編集は、管理者としてログインしているときに **開発者ポータル** から実行できます。 開発者ポータルにアクセスするには、まず Azure portal を開き、API Management インスタンスのサービス ツール バーにある **[開発者ポータル]** をクリックします。

開発者ポータル テンプレートにアクセスするには、左側のカスタマイズ アイコンをクリックしてカスタマイズ メニューを表示し、 **[テンプレート]** をクリックします。

![カスタマイズ メニューを表示するカスタマイズ アイコンが強調表示されているスクリーンショット。][api-management-customize-menu]

テンプレートの一覧には、開発者ポータルのさまざまなページをカバーするテンプレートのカテゴリが表示されます。 各テンプレートは異なりますが、それらを編集して変更を発行する手順は同じです。 テンプレートを編集するには、テンプレートの名前をクリックします。

![開発者ポータル テンプレート][api-management-templates-menu]

テンプレートをクリックすると、そのテンプレートによってカスタマイズできる開発者ポータルのページが表示されます。 この例では、**製品リスト** テンプレートが表示されています。 **製品リスト** テンプレートは、赤色の四角形によって示されている画面の領域を制御します。

![製品リスト テンプレート][api-management-developer-portal-templates-overview]

**ユーザー プロファイル** テンプレートなどのいくつかのテンプレートと同じように、同じページのさまざまな部分をカスタマイズします。

![ユーザー プロファイル テンプレート][api-management-user-profile-templates]

各開発者ポータル テンプレート用のエディターでは、ページの下部に 2 つのセクションが表示されます。 左側にはテンプレートの編集ウィンドウが表示され、右側にはテンプレートのデータ モデルが表示されます。

テンプレート編集ウィンドウには、開発者ポータルの対応するページの外観と動作を制御するマークアップが含まれています。 テンプレートのマークアップでは、 [DotLiquid](http://dotliquidmarkup.org/) 構文を使用します。 DotLiquid 用の一般的なエディターの 1 つに、 [設計者向け DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)があります。 編集中にテンプレートに加えられた変更はブラウザーにリアルタイムで表示されますが、テンプレートを[保存](#to-save-a-template)して[発行](#to-publish-a-template)するまで顧客の目に触れることはありません。

![テンプレートのマークアップ][api-management-template]

**テンプレート データ** ウィンドウには、特定のテンプレートで使用できるエンティティのデータ モデル用のガイドが提供されます。 このガイドは、開発者ポータルに現在表示されているライブ データを表示することによって提供されます。 テンプレート ウィンドウは、 **テンプレート データ** ウィンドウの右上にある長方形をクリックすることで拡大できます。

![テンプレートのデータ モデル][api-management-template-data]

前の例には、次の例に示す **[Template data]\(テンプレート データ\)** ウィンドウに表示されたデータから取得され、開発者ポータルに表示される 2 つの製品があります。

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

**製品リスト** テンプレートのマークアップは、製品のコレクションを反復処理して各製品の情報とリンクを表示することによってデータを処理して、目的の出力を提供します。 マークアップの `<search-control>` 要素と `<page-control>` 要素に注目してください。 これらは、ページでの検索コントロールとページング コントロールの表示を制御します。 `ProductsStrings|PageTitleProducts` は、ページの `h2` ヘッダー テキストを含むローカライズされた文字列参照です。 開発者ポータル テンプレートで使用できる文字列リソース、ページ コントロール、およびアイコンの一覧については、 [API Management 開発者ポータル テンプレート リファレンスに関するページ](api-management-developer-portal-templates-reference.md)を参照してください。

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>テンプレートを保存するには
テンプレートを保存するには、テンプレート エディターで [保存] をクリックします。

![テンプレートを保存する][api-management-save-template]

保存された変更は、発行されるまで、開発者ポータルに反映されません。

## <a name="to-publish-a-template"></a>テンプレートを発行するには
保存したテンプレートは、個別に発行することもすべてをまとめて発行することもできます。 個々のテンプレートを発行するには、テンプレート エディターで [発行] をクリックします。

![テンプレートを発行する][api-management-publish-template]

**[はい]** をクリックして確認すると、テンプレートが開発者ポータルで有効になります。

![テンプレートを有効にするための [はい] を選択する場所を示すスクリーンショット。][api-management-publish-template-confirm]

現在未発行のテンプレートのすべてのバージョンを発行するには、テンプレートの一覧で **[発行]** をクリックします。 未発行のテンプレートは、テンプレート名の後ろのアスタリスクによって示されます。 この例では、**製品リスト** テンプレートと **製品** テンプレートが発行されます。

![テンプレートを発行する][api-management-publish-templates]

**[Publish customizations (カスタマイズの発行)]** をクリックして確認します。

![発行を確認する][api-management-publish-customizations]

新しく発行されたテンプレートは、開発者ポータルですぐに有効になります。

## <a name="to-revert-a-template-to-the-previous-version"></a>テンプレートを前のバージョンに戻すには
テンプレートを前に発行したバージョンに戻すには、テンプレート エディターで [戻す] をクリックします。

![テンプレートを元に戻すために使用するアイコンが強調表示されているスクリーンショット。][api-management-revert-template]

**[はい]** をクリックして確定します。

![変更を確認するための [はい] を選択する場所を示すスクリーンショット。][api-management-revert-template-confirm]

元に戻す操作が完了すると、前に発行したバージョンのテンプレートが開発者ポータルで有効になります。

## <a name="to-restore-a-template-to-the-default-version"></a>テンプレートを既定のバージョンに復元するには
テンプレートを既定のバージョンに復元する操作は、2 段階のプロセスです。 まずテンプレートを復元し、その後復元したバージョンを発行する必要があります。

1 つのテンプレートを既定のバージョンに復元するには、テンプレート エディターで [復元] をクリックします。

![テンプレートを元に戻す][api-management-reset-template]

**[はい]** をクリックして確定します。

![Confirm][api-management-reset-template-confirm]

すべてのテンプレートを既定のバージョンに復元するには、テンプレートの一覧で **[Restore default templates (既定のテンプレートの復元)]** をクリックします。

![テンプレートを復元する][api-management-restore-templates]

その後、復元したテンプレートを、「 [テンプレートを発行するには](#to-publish-a-template)」の手順に従って、個別にまたはまとめて発行する必要があります。

## <a name="next-steps"></a>次のステップ
開発者ポータル テンプレート、文字列リソース、アイコン、およびページ コントロールのリファレンス情報については、 [API Management 開発者ポータル テンプレート リファレンスに関するページ](api-management-developer-portal-templates-reference.md)を参照してください。

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
