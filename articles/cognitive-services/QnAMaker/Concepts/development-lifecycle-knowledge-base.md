---
title: ナレッジ ベースのライフサイクル - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 52d4efaba25dcede3322586d05fceb4d166f51e3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973803"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker におけるナレッジ ベースのライフサイクル
QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。 

![作成サイクル](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成
QnA Maker ナレッジ ベース (KB) エンドポイントでは、KB のコンテンツに基づいて、ユーザー クエリに対する最も一致する回答を提供します。 ナレッジ ベースの作成は、質問、回答、関連付けられているメタデータのコンテンツ リポジトリを設定する場合に行う 1 回限りの操作です。 ナレッジ ベースは、FAQ ページ、製品マニュアル、構造化された Q と A のペアなど、既存のコンテンツをクロールすることで作成できます。 [ナレッジ ベースの作成](../quickstarts/create-publish-knowledge-base.md)方法を確認してください。

## <a name="testing-and-updating-the-knowledge-base"></a>ナレッジ ベースのテストと更新

編集または自動抽出を行ってナレッジ ベースにコンテンツを取り込んだら、テストすることができます。 対話型のテストは、QnA Maker ポータル内で **[テスト]** パネルから実行できます。その際、一般的なユーザー クエリを入力し、正し応答と十分な信頼度スコアによって応答が返されたことを確認します。 

* **低い信頼度スコアを修正するには**: 別の質問を追加します。 
* **クエリから誤って[既定の応答](confidence-score.md#change-default-answer)が返された場合**: 正しい質問に新しい回答を追加します。 

結果に満足するまで、このテストと更新の短いループが続きます。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。

大規模な KB には、[generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) と、公開されているナレッジ ベースではなく `test` ナレッジ ベースを照会する本文のプロパティ `isTest` を利用して、自動化されたテストを使用します。 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>ナレッジ ベースの公開
ナレッジ ベースのテストが完了したら、それを公開することができます。 公開時に、テスト済みのナレッジ ベースの最新バージョンが、**公開済み**のナレッジ ベースを表す専用の Azure Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

この方法では、ナレッジ ベースのテスト バージョンに加えられた変更は、実稼働アプリケーションに存在する可能性のある公開済みのバージョンには影響しません。

これらのナレッジ ベースをそれぞれ別個にテスト対象とすることができます。 API を使用して、generateAnswer 呼び出しで本文のプロパティ `isTest` を指定し、ナレッジ ベースのテスト バージョンを対象とすることができます。

[ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。

## <a name="monitor-usage"></a>使用状況の監視
サービスのチャット ログを記録できるようにするには、[QnA Maker サービスを作成する](../How-To/set-up-qnamaker-service-azure.md)際に Application Insights を有効にする必要があります。

サービスの使用状況について、さまざまな分析を行うことができます。 Application Insights を使用して [QnA Maker サービスを分析する](../How-To/get-analytics-knowledge-base.md)方法の詳細を確認してください。

分析から学んだ内容に基づいて、適切な[ナレッジ ベースの更新](../How-To/edit-knowledge-base.md)を行います。

## <a name="version-control-of-a-knowledge-base"></a>ナレッジ ベースのバージョン管理

QnA Maker では、バージョン管理は提供されません。 **[設定]** ページからナレッジ ベースをエクスポートして、独自の方法とツールを使用する必要があります。

**[設定]** ページから、ナレッジ ベースの TSV 形式または XLS 形式へのエクスポートを完了します。 

特定のバージョンに戻る必要がある場合は、自分のローカル システムからそのファイルをインポートする必要があります。 **[設定]** ページから、TSV ファイルまたは XLS ファイルをインポートします。 これにより、ナレッジ ベース内の現在の質問と回答が、インポートされたファイルの内容に置き換えられます。   

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [信頼度スコア](./confidence-score.md)