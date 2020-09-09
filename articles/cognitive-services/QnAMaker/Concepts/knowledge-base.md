---
title: データ ソースからのインポート - QnA Maker
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: eaa19cb2abf84f31cda9d8894e91ec1540980b27
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993101"
---
# <a name="importing-from-data-sources"></a>データ ソースからのインポート

ナレッジ ベースは、パブリック URL とファイルで取り込まれる質問と回答のペアで構成されます。

## <a name="data-source-locations"></a>データ ソースの場所

コンテンツは、データ ソースからナレッジ ベースに取り込まれます。 データ ソースの場所は、認証を必要としない**パブリック URL またはファイル**です。

認証で保護された [SharePoint ファイル](../how-to/add-sharepoint-datasources.md)は例外です。 SharePoint リソースは、Web ページではなく、ファイルになっている必要があります。 URL が .ASPX のような Web 拡張子で終わる場合、SharePoint から QnA Maker にインポートされません。

## <a name="chit-chat-content"></a>おしゃべりコンテンツ

おしゃべり QnA コンテンツ セットは、いくつかの言語と会話スタイルで、完全なコンテンツ データ ソースとして提供されています。 これを使ってボットのパーソナリティの作成を始めることができ、それにより最初から作成するよりもコストと時間が節約されます。 このコンテンツ セットをナレッジ ベースに[追加する方法](../how-to/chit-chat-knowledge-base.md)を確認してください。

## <a name="structured-data-format-through-import"></a>インポートでの構造化データ形式

ナレッジ ベースをインポートすると、既にあるナレッジ ベースの内容が置き換えられます。 インポートには、質問と回答を含む構造化された `.tsv` ファイルが必要です。 この情報によって、QnA Maker は質問とその回答のペアをグループ化し、その帰属先となるデータ ソースを特定することができます。

| Question  | Answer  | source| メタデータ (1 つのキー: 1 つの値) |
|-----------|---------|----|---------------------|
| 質問 1 | 回答 1 | URL 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 質問 2 | 回答 2 | 編集|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>インポートによる構造化された複数ターン形式

複数ターンの会話は、`.tsv` ファイル形式で作成できます。 この形式を使うと、(QnA Maker を使用せずに他のプロセスを使用して) 以前のチャット ログを分析して複数ターンの会話を作成してから、自動化によって `.tsv` ファイルを作成することができます。 ファイルをインポートし、既存のナレッジ ベースを置き換えます。

> [!div class="mx-imgBorder"]
> ![3 レベルの複数ターンの質問の概念モデル](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

複数ターンに固有の複数ターンの `.tsv` の列は **Prompts** です。 Excel に表示される例 `.tsv` は、複数ターンの子を定義するために含める情報を示しています。

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**displayOrder** は数値であり、**displayText** はマークダウンを含まないテキストです。

> [!div class="mx-imgBorder"]
> ![Excel に表示される複数ターンの質問の例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>例としてエクスポートする

`.tsv` ファイルで QnA ペアを表す方法が不明な場合:
* この [GitHub からダウンロードできるサンプル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)を使用します
* または、QnA Maker ポータルでペアを作成し、保存してから、ペアを表す方法の例としてナレッジ ベースをエクスポートします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの開発ライフサイクル](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>関連項目

QnA Maker の[マークダウンのリファレンス](../reference-markdown-format.md)を参照して、回答の形式を整えます。

[QnA Maker の概要](../Overview/overview.md)

次を使用してナレッジ ベースを作成、編集します。
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

次を使用して回答を生成します。
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
