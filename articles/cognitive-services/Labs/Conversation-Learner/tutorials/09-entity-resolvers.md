---
title: Conversation Learner モデルのエンティティ リゾルバー - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner のエンティティ リゾルバーを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca78e2536a922165f40bbcbabcae005021aa70b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167849"
---
# <a name="entity-resolvers"></a>エンティティ リゾルバー

このチュートリアルでは、Conversation Learner のエンティティ リゾルバーを使用する方法について説明します

## <a name="video"></a>ビデオ

[![エンティティ リゾルバーのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

- [Resolver Type]\(リゾルバーの種類\) は、カスタム エンティティの省略可能なプロパティです。
- エンティティ リゾルバーは、LUIS の事前トレーニング済みのエンティティ認識エンジンの能力を利用して、カスタム エンティティに追加の詳細とわかりやすさを提供します。

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルを作成する

1. **[新しいモデル]** を選択します。
2. **[名前]** に「**エンティティ リゾルバー**」と入力します。
3. **作成**を選択します。

### <a name="create-a-pair-of-entities"></a>エンティティのペアを作成する

1. 左側のパネルで **[エンティティ]** を選択し、**[新しいエンティティ]** を選択します。
2. **[エンティティ名]** に「**departure**」(出発) と入力します。
3. **[Resolver Type]\(リゾルバーの種類\)** に **[datetimeV2]** を選択します。
4. **作成**を選択します。 **[OK]** を選択して情報のポップアップを閉じます。
5. 手順 1 から 4 を繰り返して、リゾルバーの種類が **datetimeV2** で **return** という名前の 2 つ目のエンティティを作成します。

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>アクションのペアを作成する

1. 左側のパネルで **[アクション]** を選択し、**[新しいアクション]** を選択します。
2. **[Bot's response]\(ボットの応答\)** に「**You are leaving on $departure and returning on $return**」(出発日は $departure で、戻りは $return です) と入力します。
    - 重要 - 「$[entityName]」と入力した場合は、Enter キーを押すか、ドロップ ダウンでエンティティをクリックする必要があります。そうしないと、Conversation Learner ではこれがエンティティではなくテキストと見なされます。
    - **[Required Entities]\(必須エンティティ\)** フィールドにもこれらのエンティティが表示され、削除できないことにご注意ください。 これにより、両方の必須エンティティが提示されるまで、このアクションは使用可能になりません。
3. **作成**を選択します。
4. **[新しいアクション]** を選択して 2 つ目のアクションを作成します。
5. **[Bot's response]\(ボットの応答\)** に「**When are you planning to travel?**」(旅行の予定はいつですか?) と入力します。
6. **[Disqualifying Entities]\(不適格エンティティ\)** に「**departure**」(出発) と「**return**」(戻り) を入力します。 これらは、これらのエンティティのいずれかに値が含まれている場合にこのアクションを実行しないようボットに伝えます。
7. **作成**を選択します。

![](../media/T09_actions.png)

### <a name="training"></a>トレーニング

1. **[完了済み]** の左上にある **[Training: [Status]]\(トレーニング: [状態]\)** を確認します。
    - この時間がかかりすぎる場合は、[Refresh]\(更新\) リンクをクリックすることができます。
    - トレーニングの "完了" 状態は、モデルをトレーニングするときにエンティティ リゾルバーが動作するために必要です。

2. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
3. 最初のユーザー発話「book me a flight」(フライトを予約してください) を入力します。 
4. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
5. 応答 "When are you planning to travel?" (旅行の予定はいつですか?) を選択します。
6. ユーザーとして、「leaving tomorrow and returning Sunday next week」(明日出発し、来週の日曜日に戻ります) と応答します。
    - Conversation Learner によってそのユーザーの操作中に 2 つの "事前トレーニング済み日付" エンティティがどのように検出されたかにご注意ください。
7. [Entity Detection]\(エンティティの検出\) パネルで、テキスト "tomorrow" (明日) を選択し、「departure」(出発) というラベルを付けます
8. また、テキスト "Sunday next week" (来週の日曜日) に「return」(戻り) というラベルを付けます
9. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - [メモリ] ウィンドウに、departure (出発) と return (戻り) の日付がどのように表示されるかにご注意ください。
    - それぞれにカーソルを合わせて、エンティティが日付オブジェクトであり、"Sunday" (日曜日) や "tomorrow" (明日) ではなく実際のカレンダー日付が明確にキャプチャされることを確認します。
10. "You are leaving on..." (出発日は...) を選択しますボットが応答します。
11. [保存] ボタンをクリックします。

![](../media/T09_training.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [代替入力](./10-alternative-inputs.md)
