---
title: QnA Maker ナレッジ ベースへのおしゃべりの追加
titleSuffix: Azure Cognitive Services
description: ボットに個人的なおしゃべりを追加すると、KB を作成するときにより会話的で魅力的なものになります。 QnA Maker を使用すると、定義済みの最上位のおしゃべりのセットを、KB に簡単に追加することができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 60a17859fd74b1972e0905a830ba984838a94ffd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447494"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>ナレッジ ベースにおしゃべりを追加する

ボットにおしゃべりを追加すると、ボットがより会話的で魅力的なものになります。 QnA Maker のおしゃべり機能を使用すると、定義済みのおしゃべりのセットを、ナレッジ ベース (KB) に簡単に追加することができます。 これを使ってボットのパーソナリティの作成を始めることができ、それにより最初から作成するよりもコストと時間が節約されます。  

このデータセットには、複数のペルソナ (Professional、Friendly、Witty など) の音声の約 100 のシナリオのおしゃべりが含まれています。 ボットの音声に最も近いペルソナを選択します。 ユーザー クエリを指定すると、QnA Maker がそれを、最も近い既知のおしゃべり QnA と一致させようとします。  

さまざまなパーソナリティのいくつかの例を、次に示します。 すべてのパーソナリティの[データセット](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)とパーソナリティの詳細を確認できます。

`When is your birthday?` のユーザー クエリでは、パーソナリティごとに定型の応答が用意されています。

<!-- added quotes so acrolinx doesn't score these sentences -->
|パーソナリティ|例|
|--|--|
|Professional|"Age doesn't really apply to me." (年齢は、私にはまったく当てはまりません。)|
|Friendly|"I don't really have an age." (私には、年齢なんて本当にありません。)|
|Witty|"I'm age-free." (私は、年齢にとらわれない存在です。)|
|Caring (温かい)|"I don't have an age." (私には、年齢はありません。)|
|Enthusiastic (熱心)|"I'm a bot, so I don't have an age." (私はボットですから、年齢はありません。)|
||

> [!NOTE]
> おしゃべりのサポートは、現時点では英語でのみ提供されています。 

## <a name="add-chit-chat-during-kb-creation"></a>KB 作成時におしゃべりを追加する
ナレッジ ベースの作成中に、ソース URL とファイルを追加した後で、おしゃべりの追加を選択することができます。 おしゃべりのベースとして使用するパーソナリティを選択します。 おしゃべりを追加しない場合、またはデータ ソース内におしゃべりのサポートが既にある場合は、 **[なし]** を選択します。 
   
![作成時におしゃべりを追加する](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>既存の KB におしゃべりを追加する
KB を選択し、 **[設定]** ページに移動します。 適切な **.tsv** 形式のすべてのおしゃべりデータセットへのリンクがあります。 目的のパーソナリティをダウンロードして、ファイル ソースとしてアップロードします。 ファイルをダウンロードおよびアップロードするときは、形式またはメタデータを編集しないでください。 
  
![既存の KB におしゃべりを追加する](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>おしゃべりの質問や返答を編集する
KB を編集するときは、選択したパーソナリティに基づいて、おしゃべりの新しいソースが表示されます。 ここで、他のソースと同様に、変更した質問を追加したり返答を編集したりできます。 

![おしゃべり QnA を編集する](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

メタデータを表示するには、ツールバーの **[表示オプション]** を選択し、 **[Show metadata]\(メタデータを表示する\)** を選択します。

## <a name="add-additional-chit-chat-questions-and-answers"></a>追加のおしゃべりの質問と返答を追加する
定義済みのセットに含まれていない、新しいおしゃべり QnA を追加することができます。 既におしゃべりセットで対象となっている QnA ペアは、複製しないようにしてください。 追加した新しいおしゃべり QnA は、 **[編集]** ソースに追加されます。 これがおしゃべりであることをランカーが確実に理解できるようにするには、次の図に示すように、メタデータ キー/値ペアである "編集: おしゃべり" を追加します。
   
![![Add chit-chat QnAs](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>既存の KB からおしゃべりを削除する
KB を選択し、 **[設定]** ページに移動します。 選択したパーソナリティ名を持つ特定のおしゃべりソースが、ファイルとして表示されます。 ソース ファイルとしてこれを削除することができます。

![KB からおしゃべりを削除する](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースをインポートする](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)
