---
title: レビュー ツールを利用してコンテンツ レビューを使用する - Content Moderator
titlesuffix: Azure Cognitive Services
description: レビュー ツールで、モデレート担当者が Web ポータル内の画像をレビューできる方法を説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758482"
---
# <a name="create-human-reviews"></a>目視レビューを作成する

このガイドでは、レビュー ツール Web サイト上で[レビュー](../review-api.md#reviews)を設定する方法について説明します。 レビューにより、人間のモデレーターが評価するコンテンツが格納おされて表示されます。 モデレート担当者は適用されたタグを変更し、必要に応じて独自のカスタム タグを適用することができます。 ユーザーがレビューを完了すると、結果が指定のコールバック エンドポイントに送信され、コンテンツはサイトから削除されます。

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、アカウントを作成します。

## <a name="image-reviews"></a>画像のレビュー

1. [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動し、**[Try]\(試す\)** タブを選択して、レビューするいくつかの画像をアップロードします。
1. アップロードされた画像の処理が完了したら、**[レビュー]** タブに移動し、**[画像]** を選択します。

    ![Review Image オプションが強調表示されたレビュー ツールを示す Chrome ブラウザー](images/review-images-1.png)

    自動モデレーション プロセスによって割り当てられたラベルと共に画像が表示されます。 レビュー ツールを使用して送信した画像は、他のレビュー担当者には表示されません。

1. 必要に応じて、**表示するレビュー**のスライダー (1) を動かして、画面に表示される画像の数を調整します。 **タグ付き**または**タグなし**のボタン (2) をクリックして、画像を適宜並べ替えます。 タグ パネル (3) をクリックして、オンとオフを切り替えます。

    ![レビュー用のタグが付いた画像とレビュー ツールが表示された Chrome ブラウザー](images/review-images-2.png)

1. 画像の詳細を表示するには、サムネイル内の省略記号をクリックし、**[詳細の表示]** をクリックします。 **[移動先]** オプションを使用して、サブチームに画像を割り当てることができます (サブチームの詳細については、[チーム](./configure.md#manage-team-and-subteams)に関するセクションを参照してください)。

    ![[詳細の表示] オプションが強調表示された画像](images/review-images-3.png)

1. 詳細ページで画像モデレーション情報を参照します。

    ![別のウィンドウにモデレーションの詳細が一覧表示された画像](images/review-images-4.png)

1. タグの割り当てをレビューし、必要に応じて更新したら、**[次へ]** をクリックしてレビューを送信します。 送信後、約 5 秒間は **[前へ]** をクリックして前の画面に戻り、画像をもう一度レビューできます。 その後、画像は送信キューになくなり、**[前へ]** ボタンは使用できなくなります。

## <a name="text-reviews"></a>テキストのレビュー

テキストのレビューは、画像のレビューと同じように機能します。 コンテンツをアップロードする代わりに、テキスト (最大 1,024 文字) を書き込むか、貼り付けるだけです。 その後、Content Moderator によって、テキストが分析され、(不適切な表現や個人データなど、その他のモデレート情報に加えて) タグが適用されます。 テキストのレビューでは、レビューを送信する前に、適用されたタグを切り替えたり、カスタム タグを適用したりできます。

![Chrome ブラウザー ウィンドウにフラグが付けられたテキストが表示されたレビュー ツールのスクリーンショット](../images/reviewresults_text.png)

## <a name="next-steps"></a>次の手順

このガイドでは、Content Moderator の[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)からレビューを設定して使用する方法について学習しました。 次は、[REST API ガイド](../try-review-api-review.md)または [.NET SDK ガイド](../moderation-reviews-quickstart-dotnet.md)を参照して、プログラムによってレビューを作成する方法について学習してください。