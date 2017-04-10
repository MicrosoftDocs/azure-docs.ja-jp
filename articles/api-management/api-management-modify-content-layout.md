---
title: "Azure API Management での開発者ポータルのページ コンテンツの変更 | Microsoft Docs"
description: "Azure API Management での開発者ポータルのページ コンテンツの編集方法について説明します。"
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
ms.sourcegitcommit: ecae1da20551d8372331124b07c4aca2e15f55bb
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.lasthandoff: 02/23/2017


---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Azure API Management で開発者ポータルのページのコンテンツとレイアウトを変更する
Azure API Management で開発者ポータルをカスタマイズする基本的な方法は&3; つあります。

* [静的なページとページ レイアウト要素の内容を編集する][modify-content-layout] (このガイドで説明します)
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles]
* [ポータルで生成されたページで使用されるテンプレートを変更する][portal-templates] (例: API ドキュメント、製品、ユーザー認証など)

## <a name="page-structure"> </a>開発者ポータルのページの構造

開発者ポータルは、コンテンツ管理システムがベースとなっています。 すべてのページのレイアウトは、ウィジェットと呼ばれる小さなページ要素のセットを基にして構築されています。

![開発者ポータル ページの構造][api-management-customization-widget-structure]

ウィジェットはすべて編集できます。 
* 各ページ固有の中心的なコンテンツは、"コンテンツ" ウィジェットに存在します。 ページを編集することは、このウィジェットの内容を編集することを意味します。
* すべてのページ レイアウト要素は、残りのウィジェットに含まれます。 これらのウィジェットに加えられた変更は、すべてのページに適用されます。 これらを "レイアウト ウィジェット" と呼びます。

日常的なページの編集では、通常、ページごとに異なる内容が含まれるコンテンツ ウィジェットのみが変更されます。

## <a name="modify-layout-widget"> </a>レイアウト ウィジェットの内容の変更

開発者ポータル内のコンテンツは、Azure Portal からアクセスできるパブリッシャー ポータルを使用して変更します。 パブリッシャー ポータルにアクセスするには、API Management インスタンスのサービス ツール バーの **[パブリッシャー ポータル]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

ウィジェットの内容を編集するには、左側の **[開発者ポータル]** メニューで **[ウィジェット]** をクリックします。 この例では、ヘッダー ウィジェットの内容を変更します。 リストから **[ヘッダー]** ウィジェットを選択します。

![Widgets header][api-management-widgets-header]

ヘッダーの内容は、 **[本文]** フィールドで編集することができます。 必要に応じてテキストを変更し、ページの下部にある **[保存]** をクリックします。

以後、この新しいヘッダーが開発者ポータル内のすべてのページに表示されます。

> パブリッシャー ポータルで開発者ポータルを開くには、上部のバーにある **[開発者ポータル]** をクリックします。
> 
> 

## <a name="edit-page-contents"> </a>ページの内容を編集する

既存のコンテンツ ページをすべて一覧表示するには、パブリッシャー ポータルの **[開発者ポータル]** メニューにある **[コンテンツ]** をクリックします。

![Manage content][api-management-customization-manage-content]

開発者ポータルのホーム ページに表示される内容を編集するには、 **ウェルカム** ページをクリックします。 目的の変更を行い、必要に応じてプレビューしてから **[今すぐ発行]** をクリックすると、すべての人がその内容を閲覧できるようになります。

> ホーム ページには、最上部にバナーを表示できる特殊なレイアウトが使用されています。 このバナーを **[コンテンツ]** セクションから編集することはできません。 このバナーを編集するには、**[開発者ポータル]** メニューの **[ウィジェット]** をクリックし、**[現在のレイヤー]** ボックスの一覧の **[ホーム ページ]** を選択して、**[おすすめ]** セクションにある **[バナー]** 項目を開きます。 このウィジェットの内容は、他のページとまったく同じように編集できます。
> 
> 

## <a name="next-steps"> </a>次のステップ
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles]
* [ポータルで生成されたページで使用されるテンプレートを変更する][portal-templates] (例: API ドキュメント、製品、ユーザー認証など)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png

