---
title: ユーザーの発話をレビューする
titleSuffix: Language Understanding - Azure Cognitive Services
description: アクティブ ラーニングによって、エンドポイント発話の意図とエンティティが正しいことをレビューします。 LUIS が確証を持てないエンドポイント発話が LUIS によって選択されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 2af11d7776a29288801e5db049262481ae27c102
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785358"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>エンドポイント発話のレビューによるアクティブ ラーニング実現の概念
アクティブ ラーニングは、3 つの予測精度の改善戦略の 1 つで、最も簡単に実装できます。 アクティブ ラーニングによって、エンドポイント発話の意図とエンティティが正しいことをレビューします。 LUIS が確証を持てないエンドポイント発話が LUIS によって選択されます。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは
アクティブ ラーニングは 2 段階のプロセスです。 まず、LUIS がアプリのエンドポイントで受信した発話が、LUIS によって選択されます。 次のステップで、アプリの所有者またはコラボレーターが、選択された発話を、正しい意図とその意図内の任意のエンティティを含め、[レビュー](luis-how-to-review-endpoint-utterances.md)のために検証します。 発話をレビューしたら、アプリをもう一度トレーニングして公開します。 

## <a name="which-utterances-are-on-the-review-list"></a>どの発話がレビュー リストに追加されるか
最上位にある実行中の意図のスコアが低いとき、または上位 2 つの意図のスコアが非常に近いときに、その発話は LUIS によってレビュー リストに追加されます。 

## <a name="single-pool-for-utterances-per-app"></a>アプリごとの発話のための 1 つのプール
**[エンドポイントの発話の確認]** 一覧は、バージョンによって変化しません。 どのバージョンの発話をアクティブに編集しているか、またはどのバージョンのアプリがエンドポイントで発行されたかには関係なく、確認すべき発話のプールが 1 つあります。 

## <a name="where-are-the-utterances-from"></a>発話の取得元
エンドポイント発話は、アプリケーションの HTTP エンドポイントのエンド ユーザーのクエリから取得されます。 お使いのアプリが公開されていない場合や、まだヒットがない場合、レビュー対象の発話はありません。 特定の意図またはエンティティについて、エンドポイントでヒットがない場合は、それを含む、レビュー対象の発話がありません。 

## <a name="schedule-review-periodically"></a>レビューを定期的にスケジュール設定
提案された発話は必ずしも毎日レビューする必要はありませんが、LUIS の定期的なメンテナンスに組み込むことをお勧めします。 

## <a name="delete-review-items-programmatically"></a>プログラムによるレビュー項目の削除
**[ラベルなしの発話の削除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API を使用します。 **[ログ ファイルをエクスポートする](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** ことで、削除前にこれらの発話をバックアップします。

## <a name="next-steps"></a>次の手順

* エンドポイント発話を[レビュー](luis-how-to-review-endpoint-utterances.md)する方法を確認します
