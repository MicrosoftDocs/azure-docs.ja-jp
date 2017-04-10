---
title: "Azure API Management での開発者ポータルのスタイルのカスタマイズ | Microsoft Docs"
description: "Azure API Management で開発者ポータルの任意のページで使用されているスタイルを変更する方法について説明します。"
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 336d4f80f0357796fb29eb9314c11edfce831a69
ms.openlocfilehash: bd08eb476a4bd7298c5650977b88ba0b24deddec
ms.lasthandoff: 02/23/2017


---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Azure API Management での開発者ポータルのスタイルのカスタマイズ
Azure API Management で開発者ポータルをカスタマイズする基本的な方法は&3; つあります。

* [静的なページの内容とページ レイアウト要素を編集する][modify-content-layout]
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles] (このガイドで説明します)
* [ポータルで生成されたページで使用されるテンプレートを変更する][portal-templates] (例: API ドキュメント、製品、ユーザー認証など)

## <a name="change-headers-styling"> </a>ページ要素のスタイルを変更する

色、フォント、サイズ、文字間隔など、ポータル ページのスタイルに関連したあらゆる要素は、スタイルのルールによって定義されます。 

スタイル ルールの編集は、管理者としてログインしているときに**開発者ポータル**から実行できます。 開発者ポータルにアクセスするには、まず Azure Portal を開き、API Management インスタンスのサービス ツール バーにある **[パブリッシャー ポータル]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

右上にある **[開発者ポータル]** をクリックします。 

![パブリッシャー ポータルにある開発者ポータル リンク][api-management-pp-dp-link]

カスタマイズ ツール バーを開くには、マウスをカスタマイズ アイコンの上に移動 (またはアイコンを選択) し、ツールバーの [STYLES (スタイル)] をクリックします。

![カスタマイズ ツール バー ボタン][api-management-customization-toolbar-button]

スタイルのルールを編集する方法は、主に&2; つあります。1 つ目は、既定で表示される、いずれかの場所で使用されているすべてのスタイル ルールの一覧でスタイルを探し、必要に応じて変更する方法です。もう&1; つは、**[Select an element on the page (ページの要素を選択)]** を選択し、ページの任意の場所をクリックして、その要素のスタイルだけを表示する方法です。

![Customization toolbar][api-management-customization-toolbar]

この例の **[Select an element on the page (ページの要素を選択)]** オプションをクリックします。  要素にマウスを重ねると、その要素が強調表示されるようになり、クリックした場合にどの要素のスタイルが編集対象となるかを把握できます。 ヘッダーのテキストの上にマウスを移動し (通常ここには会社名が表示されます)、このテキストをクリックします。 名前とカテゴリを持つ一連のスタイル ルールがスタイル エディター内に表示されます。 それぞれのルールは、選択された要素のスタイルに関するプロパティを表します。 たとえば、上で選択したヘッダー テキストの場合、テキストのサイズは @font-size-h1 で定義され、フォント (代替候補を含む) の名前は @headings-font-family で定義されています。

> [Bootstrap][bootstrap] をご存じの方のためにいうと、実際には、開発者ポータルに使用されている Bootstrap テーマの [LESS 変数][LESS variables]にこれらのルールが含まれています。
> 
> 

見出しテキストの色を変更してみましょう。 **@headings-color** ボックスの入力内容を選択し、「**#000000**」と入力します。 これは、黒色を表す&16; 進コードです。 すると、テキスト ボックスの端に正方形のカラー インジケーターが表示されます。 このインジケーターをクリックするとカラー ピッカーが表示され、色を選択することができます。

![Color picker][api-management-customization-toolbar-color-picker]

変更は操作時にリアルタイムでプレビューされますが、管理者だけに表示されます。 変更を全員が閲覧できるようにするには、スタイル エディターで **[発行]** をクリックして変更を確定してください。

![[発行] メニュー][api-management-customization-toolbar-publish-form]

> ページの他の要素に適用されるスタイル ルールを変更するには、ヘッダーの場合と同じ手順を実行します。 スタイル エディターで **[Select an element on the page (ページの要素を選択)]** をクリックし、対象の要素を選択して、画面に表示されるスタイル ルールの値の変更を開始します。
> 
> 


## <a name="next-steps"> </a>次のステップ
* [開発者ポータル テンプレート](api-management-developer-portal-templates.md)を使用して開発者ポータル ページの内容をカスタマイズする方法について説明します。

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

