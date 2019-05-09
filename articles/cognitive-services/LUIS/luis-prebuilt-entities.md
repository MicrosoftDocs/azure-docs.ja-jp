---
title: 事前構築済みのエンティティ
titleSuffix: Azure Cognitive Services
description: LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 作成済みエンティティのサポートは、LUIS アプリのカルチャによって異なります。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cfc4ff58cfeb65f80f9ac5ce2dd532defde5ef8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119090"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>一般的なデータ型を認識するための作成済みエンティティ

LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

1. **[マイ アプリ]** ページで名前をクリックしてアプリを開き、左側で **[エンティティ]** をクリックします。 

1. **[エンティティ]** ページで **[Add prebuilt entity]\(事前構築済みのエンティティを追加する\)** をクリックします。

1. **[Add prebuilt entities]\(事前構築済みエンティティの追加\)** ダイアログ ボックスで、datetimeV2 事前構築済みエンティティを選択します。 

    ![[Add prebuilt entities]\(作成済みエンティティの追加\) ダイアログ ボックス](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. **[完了]** を選択します。

## <a name="publish-the-app"></a>アプリの発行

事前構築済みエンティティの値を見る最も簡単な方法は、公開されているエンドポイントに対してクエリを行うことです。 

1. 上部ツール バーの **[発行]** を選択します。 **[運用]** に発行します。 

1. 緑の成功通知が表示されたら、**[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** リンクを選択して、エンドポイントを表示します。

1. エンドポイントを選択します。 新しいブラウザー タブでそのエンドポイントが開きます。 ブラウザー タブを開いたままにして、**[テスト]** セクションに移動します。

## <a name="test"></a>テスト
エンティティを追加した後、アプリをトレーニングする必要はありません。 

**q** パラメーターに値を追加して、エンドポイントで新しい意図をテストします。 **q** に対する推奨される発話の次の表を使用します。

|テスト発話|エンティティの値|
|--|:--|
|明日の便を予約する|2018-10-19|
|3 月 3 日の予定をキャンセルする|発話で年が指定されていないため、LUIS は過去で直近の 3 月 3 日 (2018-03-03) と未来で直近の 3 月 3日 (2019-03-03) を返しました。|
|午前 10 時に会議をスケジュールする|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>事前構築済みのエンティティ トークンを含んでいるエンティティをマークする
 カスタム エンティティとしてマークしたいテキスト (`HH-1234` など) があり、"_なおかつ_"[事前構築済みの Number](luis-reference-prebuilt-number.md) をモデルに追加した場合、LUIS ポータルでカスタム エンティティをマークすることはできません。 これをマークするためには API を使用します。 

 このタイプの (一部が事前構築済みエンティティで既にマークされている) トークンをマークするためには、その事前構築済みエンティティを LUIS アプリから削除する必要があります。 アプリをトレーニングする必要はありません。 そのうえで、独自のカスタム エンティティでトークンをマークします。 その後もう一度、事前構築済みエンティティを LUIS アプリに追加します。

 もう 1 つの例として、`I want first year spanish, second year calculus, and fourth year english lit.` という、一連の授業を選択する発話を考えてみましょう。LUIS アプリに事前構築済みの Ordinal が追加されている場合、`first`、`second`、`fourth` はあらかじめ序数でマークされます。 序数と授業をキャプチャしたい場合は、複合エンティティを作成し、事前構築済みの Ordinal と授業名のカスタム エンティティを複合エンティティで囲むようにラップしてください。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)
