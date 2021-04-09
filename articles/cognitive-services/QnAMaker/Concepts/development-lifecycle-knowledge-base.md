---
title: ナレッジ ベースのライフサイクル - QnA Maker
description: QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e52e7151bc30a19bd6f6041d52effdd799a87c99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776971"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker におけるナレッジ ベースのライフサイクル
QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。

![作成サイクル](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成
QnA Maker ナレッジ ベース (KB) エンドポイントでは、KB のコンテンツに基づいて、ユーザー クエリに対する最も一致する回答を提供します。 ナレッジ ベースの作成は、質問、回答、関連付けられているメタデータのコンテンツ リポジトリを設定する場合に行う 1 回限りの操作です。 KB は、次のソースなどの既存のコンテンツをクロールすることによって作成できます。

- FAQ ページ
- 製品マニュアル
- 質問と回答のペア

[ナレッジ ベースの作成](../quickstarts/create-publish-knowledge-base.md)方法を確認してください。

## <a name="testing-and-updating-the-knowledge-base"></a>ナレッジ ベースのテストと更新

編集または自動抽出を行ってナレッジ ベースにコンテンツを取り込んだら、テストすることができます。 対話型テストは、QnA Maker ポータルで **[テスト]** パネルを使用して実行できます。 一般的なユーザー クエリを入力します。 次に、応答の正確性と十分な信頼度スコアという両方の点について、返された応答を検証します。


* **低い信頼度スコアを修正するには**: 別の質問を追加します。
* **クエリから誤って [既定の応答](../How-to/change-default-answer.md)が返された場合**: 正しい質問に新しい回答を追加します。

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
ナレッジ ベースのテストが完了したら、それを公開することができます。 公開時に、テスト済みのナレッジ ベースの最新バージョンが、**公開済み** のナレッジ ベースを表す専用の Azure Cognitive Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

ナレッジ ベースのテスト バージョンにその他の変更を加えても、公開操作により、公開されたバージョンは影響を受けません。 公開されたバージョンは、実稼働アプリケーションに存在する場合があります。

これらのナレッジ ベースをそれぞれ別個にテスト対象とすることができます。 API を使用して、generateAnswer 呼び出しで本文のプロパティ `isTest` を指定し、ナレッジ ベースのテスト バージョンを対象とすることができます。

[ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。

## <a name="monitor-usage"></a>使用状況の監視
サービスのチャット ログを記録できるようにするには、[QnA Maker サービスを作成する](../How-To/set-up-qnamaker-service-azure.md)際に Application Insights を有効にする必要があります。

サービスの使用状況について、さまざまな分析を行うことができます。 Application Insights を使用して [QnA Maker サービスを分析する](../How-To/get-analytics-knowledge-base.md)方法の詳細を確認してください。

分析から学んだ内容に基づいて、適切な[ナレッジ ベースの更新](../How-To/edit-knowledge-base.md)を行います。

## <a name="version-control-for-data-in-your-knowledge-base"></a>ナレッジ ベースのデータのバージョン管理

データのバージョン管理は、QnA Maker ポータルの **[設定]** ページのインポートおよびエクスポート機能を使用して利用できます。

ナレッジ ベースをバックアップするには、ナレッジ ベースを `.tsv` または `.xls` 形式でエクスポートします。 エクスポートしたら、通常のソース管理チェックの一部としてこのファイルを含めます。

特定のバージョンに戻る必要がある場合は、自分のローカル システムからそのファイルをインポートする必要があります。 エクスポートされたナレッジベースは、**常に****[設定]** ページのインポートを介して使用する必要があります。 ファイルまたは URL ドキュメントのデータ ソースとして使用することはできません。 これにより、ナレッジ ベース内の現在の質問と回答が、インポートされたファイルの内容に置き換えられます。

## <a name="test-and-production-knowledge-base"></a>テストと運用環境のナレッジ ベース
ナレッジ ベースは質問と回答セットのリポジトリであり、その作成、管理、および使用は QnA Maker を通して行われます。 各 QnA Maker リソースには、複数のナレッジ ベースを保持できます。

ナレッジ ベースには、"*テスト*" と "*公開*" という 2 つの状態があります。

### <a name="test-knowledge-base"></a>テスト ナレッジ ベース

"*テスト ナレッジ ベース*" は、現在編集され、保存されているバージョンです。 テスト バージョンは、精度および応答の完全性について、テストが実施されています。 テスト ナレッジ ベースに加えられた変更は、アプリケーションまたはチャット ボットのエンド ユーザーに影響しません。 テスト ナレッジ ベースは HTTP 要求では `test` になります。 `test` のナレッジは、QnA Maker のポータルの対話型 **[テスト]** ペインで使用できます。

### <a name="production-knowledge-base"></a>運用環境のナレッジ ベース

"*公開ナレッジ ベース*" は、チャット ボットまたはアプリケーションで使用されるバージョンです。 ナレッジ ベースを公開すると、そのテスト バージョンの内容が、公開されたバージョンになります。 公開ナレッジ ベースは、エンドポイントを介してアプリケーションによって使用されるバージョンです。 コンテンツが正しく、適切にテストされていることを確認します。 公開ナレッジ ベースは HTTP 要求では `prod` になります。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アクティブ ラーニングの提案](./active-learning-suggestions.md)