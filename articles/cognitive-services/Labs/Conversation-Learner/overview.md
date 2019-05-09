---
title: Conversation Learner とは - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner とその仕組みについて説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c46ffe2076d4b1491a3b27958dfbf5ed09115eaa
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221286"
---
# <a name="what-is-conversation-learner"></a>Conversation Learner とは

Conversation Learner を使用して、対話例から学習する対話型インターフェイスを構築して学習させることができます。 

従来のアプローチとは異なり、Conversation Learner は、対話の始めから終わりまで、その文脈を考慮して応答を向上させることで、説得力のあるユーザー エクスペリエンスを提供します。 タスク志向の幅広い使用例にわたって、Conversation Learner は、舞台裏で機械学習を適用して、ボットとインテリジェント エージェントがユーザーをいらだたせる可能性を減らし、追加のカスタマー サービス コストを負担し、より直観的に対話できるようにします。

最初に、開発者は、模倣する典型的な対話を入力します。 対話をさらに入力して、モデルの学習を行います。 モデルが十分に機能したら、ボットをエンドユーザーに展開できます。 Conversation Learner はユーザーとの対話を記録し、開発者はそれらをレビューできます。 誤りが見つかった場合、開発者はその場で修正を行うことができ、モデルはすぐに再トレーニングされ、使用可能になります。

このアプローチにより、対話制御ロジックの手動でのコーディングが減少し、事業主やドメイン エキスパートが、機械学習の知識を事前に習得することなく、対話型インターフェイスに貢献できます。 Conversation Learner は、ボット、スマート デバイス、またはインテリジェント エージェントの一部として展開される場合でも、新しいスキル、反応、または能力を迅速に反復して、それらの品質をすぐに改善できます。 

Conversation Learner は、開発者が製品を市場に出すまでの時間を短縮できるようにし、Microsoft Bot Framework を通した、または独自のインフラストラクチャを使用するスタンドアロンでの複数の会話チャネルでの対話の成功を推進します。

まとめとハイライト:

- Conversation Learner は、タスク試行ボットを構築するための AI ファーストの方法です。

- それは、エンドツーエンドの再帰型ニューラル ネットワーク (LSTM) に依存し、複数の会話例から直接学習します。 

- デザイナー、開発者、ビジネス ユーザー、およびコール センターの勤務者が、ボットを構築して管理できます。 

- ビジネス ルールと常識をコードで表現する能力を備えています。

- 学習セッション中は、ニューラル ネットワーク モデルを使用して、会話の中で次に予期されるアクションのセットにスコアを付けます。 ボット開発者は、適切なアクションを選択し、適切な応答を提供するようにネットワークをトレーニングします。
 
- トレーニングが完了したら、開発者は、ユーザーとの対話のログを使用して、ボットの応答を修正し、モデルを再トレーニングできます。 

- ドメイン固有のサード パーティの API を呼び出して、タスクを完了できます。

