---
title: Conversation Learner モデルで会話を記録する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで会話を記録する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e3a84bfa643ebe74983bcef0d0ea72c701ffa589
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170498"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Conversation Learner モデルで会話を記録する方法

このチュートリアルでは、会話記録を使用して、現実世界のユーザーとの記録された会話により Conversation Learner モデルのトレーニングを向上させる方法について説明します。

## <a name="video"></a>ビデオ

[![会話記録のチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

および、前のチュートリアルで作成した天気予報モデルが必要です。

## <a name="details"></a>詳細
会話記録とは、ボットとエンド ユーザーのやり取りの記録されたログです。 これらの会話記録を利用することで、エンティティ ラベルとアクションの選択を修正して、モデルのパフォーマンスとシステム全体のパフォーマンスを向上させることができます。

## <a name="steps"></a>手順

Web UI で、[Import Tutorials]\(チュートリアルのインポート\) をクリックし、"Tutorial-11-LogDialogs" という名前のモデルを選択します。

このモデルには、"city" という名前の 1 つのエンティティと、その都市の天気に関する問い合わせに応答するように設計されたアクションが含まれます。 モデルのトレーニングに使用されたトレーニング会話は 2 つなので、パフォーマンスの予想は低めです。 さらにトレーニングし、実際のユーザーのやり取りを適用することで、モデルは向上します。

### <a name="create-a-new-conversation"></a>新しい会話を作成する

1. 左側のパネルで、[Log Dialogs]\(会話記録\) をクリックしてから、[New Log Dialog]\(新しい会話記録\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「Austin weather forecast」(オースティンの天気予報) と入力します。
3. [Done Testing]\(テストの完了\) ボタンをクリックします。
4. 一覧から "Austin weather forecast" 会話記録をクリックします。
5. チャット パネルで発話 "Austin weather forecast" をクリックします。
6. "Austin" をクリックし、エンティティの一覧から "city" をクリックします。
7. [Submit Changes]\(変更の送信\) ボタンをクリックします。
    - メモリ内に新しいエンティティ値があるため、エンティティの値に対するこの変更により、ダウンストリームの会話が変更されます。 後続のアクション (特に "city" エンティティに関連するもの) は、無効になる可能性があります。
8. "Which city?" (どの都市?) という発話をチャット パネルでクリックします。
9. 応答 "The weather in Austin is probably sunny.」(オースティンの天気はたぶん晴れです。) を選択します
10. [Save As Train Dialog]\(トレーニング会話として保存\) ボタンをクリックします。
    - トレーニングはすぐに開始されます

![](../media/T11_logdialog.png)

最後に 1 つ注意事項があります。 ビジネス上必要な場合は、[Settings]\(設定\) に移動して [Log Conversations]\(会話記録\) をオフにすることで、会話記録機能をオフにできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティ検出コールバック](./12-entity-detection-callback.md)
