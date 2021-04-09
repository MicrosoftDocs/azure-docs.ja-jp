---
title: ユーザーの発話をレビューする - LUIS
description: アクティブ ラーニングによって、エンドポイント発話の意図とエンティティが正しいことをレビューします。 LUIS が確証を持てないエンドポイント発話が LUIS によって選択されます。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316445"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>エンドポイント発話のレビューによるアクティブ ラーニング実現の概念
アクティブ ラーニングは、3 つの予測精度の改善戦略の 1 つで、最も簡単に実装できます。 アクティブ ラーニングによって、エンドポイント発話の意図とエンティティが正しいことをレビューします。 LUIS が確証を持てないエンドポイント発話が LUIS によって選択されます。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは
アクティブ ラーニングは 2 段階のプロセスです。 まず、LUIS がアプリのエンドポイントで受信した発話が、LUIS によって選択されます。 次のステップで、アプリの所有者またはコラボレーターが、選択された発話を、正しい意図とその意図内の任意のエンティティを含め、[レビュー](luis-how-to-review-endpoint-utterances.md)のために検証します。 発話をレビューしたら、アプリをもう一度トレーニングして公開します。

## <a name="which-utterances-are-on-the-review-list"></a>どの発話がレビュー リストに追加されるか
最上位にある実行中の意図のスコアが低いとき、または上位 2 つの意図のスコアが非常に近いときに、その発話は LUIS によってレビュー リストに追加されます。

## <a name="single-pool-for-utterances-per-app"></a>アプリごとの発話のための 1 つのプール
**[エンドポイントの発話の確認]** 一覧は、バージョンによって変化しません。 どのバージョンの発話をアクティブに編集しているか、またはどのバージョンのアプリがエンドポイントで発行されたかには関係なく、確認すべき発話のプールが 1 つあります。

[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9) ではバージョン名が必要で、アプリケーション内になくてはなりませんが、その検証以外に使われることはありません。 レビュー発話はアプリケーション全体に適用されます。 1 つの _バージョン_ から発話を削除すると、すべてのバージョンが影響を受けます。

## <a name="where-are-the-utterances-from"></a>発話の取得元
エンドポイント発話は、アプリケーションの HTTP エンドポイントのエンド ユーザーのクエリから取得されます。 お使いのアプリが公開されていない場合や、まだヒットがない場合、レビュー対象の発話はありません。 特定の意図またはエンティティについて、エンドポイントでヒットがない場合は、それを含む、レビュー対象の発話がありません。

## <a name="schedule-review-periodically"></a>レビューを定期的にスケジュール設定
提案された発話は必ずしも毎日レビューする必要はありませんが、LUIS の定期的なメンテナンスに組み込むことをお勧めします。

## <a name="delete-review-items-programmatically"></a>プログラムによるレビュー項目の削除
**[ラベルなしの発話の削除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API を使用します。 **[ログ ファイルをエクスポートする](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** ことで、削除前にこれらの発話をバックアップします。

## <a name="enable-active-learning"></a>アクティブ ラーニングを有効にする

アクティブ ラーニングを有効にするには、ユーザー クエリをログに記録する必要があります。 そのためには、`log=true` という querystring パラメーターと値を指定し、[エンドポイント クエリ](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)を呼び出します。

## <a name="next-steps"></a>次のステップ

* エンドポイント発話を[レビュー](luis-how-to-review-endpoint-utterances.md)する方法を確認します
