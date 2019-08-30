---
title: Azure API Management 開発者ポータルでページのスタイルをカスタマイズする方法 | Microsoft Docs
description: クイック スタートの手順に従って Azure API Management 開発者ポータルで要素のスタイルをカスタマイズします。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 047e724fe3e1c2e4738e5964326bf7719281f4af
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073706"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>開発者ポータル ページのスタイルをカスタマイズする

Azure API Management で開発者ポータルをカスタマイズするもっとも一般的な方法は 3 つあります。
 
* [静的なページの内容とページ レイアウト要素を編集する](api-management-modify-content-layout.md)
* 開発者ポータル全体のページ要素で使用されるスタイルを更新する (このガイドで説明します)
* [ポータルで生成されたページで使用されるテンプレートを変更する](api-management-developer-portal-templates.md) (例: API ドキュメント、製品、ユーザー認証など)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **開発者ポータル**のページで要素のスタイルをカスタマイズする
> * 変更を確認する

![スタイルのカスタマイズ](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>開発者ポータルをカスタマイズする

1. **[概要]** を選択します。
2. **[概要]** ウィンドウの上部にある **[開発者ポータル]** をクリックします。 または **[開発者ポータル URL]** のリンクをクリックすることもできます。
3. 画面の上部左側に、2 つのペイント ブラシでできたアイコンがあります。 このアイコンにカーソルを重ねると、ポータルのカスタマイズ メニューが開きます。

    ![スタイルのカスタマイズ](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. メニューから **[スタイル]** を選択し、スタイルのカスタマイズ ウィンドウを開きます。

    ページに表示される **[スタイル]** を使用して、すべての要素をカスタマイズできます。
5. **[Change variable values to customize developer portal appearance:]\(変数の値を変更して開発者ポータルの外観をカスタマイズする:\)** フィールドに、「headings-color (見出しの色)」と入力します。

    **\@headings-color** 要素がページに表示されます。 この変数はテキストの色を制御します。

    ![スタイルのカスタマイズ](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. **\@headings-color** 変数のフィールドをクリックします。 
    
    カラー ピッカーのドロップダウンが開きます。
7. カラー ピッカーのドロップダウンから、新しい色を選択します。

    > [!TIP]
    > 変更内容はすべてリアルタイムでプレビューできます。 カスタマイズ ウィンドウの上部には進行状況のインジケーターが表示されます。 2、3 秒後にヘッダー テキストが新しく選択した色に変わります。

8. メニューのカスタマイズ ウィンドウ メニューの左下から **[発行]** を選択します。
9. **[カスタマイズの公開]** を選択して、変更内容を一般に公開します。

## <a name="view-your-change"></a>変更を確認する

1. 開発者ポータルに移動します。
2. 行った変更内容を確認できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * **開発者ポータル**のページで要素のスタイルをカスタマイズする
> * 変更を確認する

[Azure API Management 開発者ポータルをテンプレートを使用してカスタマイズする方法](api-management-developer-portal-templates.md)についての説明も参照してください。