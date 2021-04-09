---
title: データ ソースとコンテンツの種類 - QnA Maker
description: データ ソースから、また、PDF、DOCX、TXT などさまざまな標準的構造化ドキュメントなど、サポートされているコンテンツの種類から質問と回答の組み合わせをインポートする方法について説明します (QnA Maker)。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99556038"
---
# <a name="importing-from-data-sources"></a>データ ソースからのインポート

ナレッジ ベースは、パブリック URL とファイルで取り込まれる質問と回答のペアで構成されます。

## <a name="data-source-locations"></a>データ ソースの場所

コンテンツは、データ ソースからナレッジ ベースに取り込まれます。 データ ソースの場所は、認証を必要としない **パブリック URL またはファイル** です。

認証で保護された [SharePoint ファイル](../how-to/add-sharepoint-datasources.md)は例外です。 SharePoint リソースは、Web ページではなく、ファイルになっている必要があります。 URL が .ASPX のような Web 拡張子で終わる場合、SharePoint から QnA Maker にインポートされません。

## <a name="chit-chat-content"></a>おしゃべりコンテンツ

おしゃべりコンテンツ セットは、いくつかの言語と会話スタイルで、完全なコンテンツ データ ソースとして提供されています。 これを使ってボットのパーソナリティの作成を始めることができ、それにより最初から作成するよりもコストと時間が節約されます。 ナレッジ ベースに[おしゃべりコンテンツを追加する方法](../how-to/chit-chat-knowledge-base.md)を確認してください。

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

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>ナレッジ ベースに追加できるドキュメントのコンテンツの種類
コンテンツの種類には、PDF、DOC、TXT など、多くの標準的な構造化ドキュメントが含まれています。

### <a name="file-and-url-data-types"></a>ファイルと URL のデータの種類

次の表は、QnA Maker でサポートされているコンテンツの種類とファイル形式をまとめたものです。

|ソースの種類|コンテンツの種類| 例|
|--|--|--|
|URL|FAQ<br> (基本、セクション付き、またはトピックのホーム ページ付き)<br>サポート ページ <br> (単一ページのハウツー記事や、トラブルシューティングの記事など)|[基本的な FAQ](../troubleshooting.md)、 <br>[リンク付きの FAQ](https://www.microsoft.com/en-us/software-download/faq)、<br> [トピック ホーム ページ付きの FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[サポート記事](./best-practices.md)|
|PDF / DOC|FAQ、<br> 製品マニュアル、<br> パンフレット、<br> 論文、<br> チラシ ポリシー、<br> サポート ガイド、<br> 構造化 QnA、<br> など|**マルチターンなし**<br>[Structured QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)、<br><br>**マルチターン**：<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|構造化 QnA ファイル<br> (RTF、HTML サポートなど)|**マルチターンなし**:<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**マルチターン**：<br>[構造化された簡単な FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface ノート PC の FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|構造化 QnA ファイル|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

データ ソースに認証が必要な場合、QnA Maker にそのコンテンツを取り込む次の方法を検討してください。

* ファイルを手動でダウンロードし、QnA Maker にインポートする
* 認証された [SharePoint の場所](../how-to/add-sharepoint-datasources.md)からファイルを追加する

### <a name="url-content"></a>URL コンテンツ

QnA Maker で **URL** を使用して、次の 2 種類のドキュメントをインポートできます。

* FAQ URL
* サポート URL

各種類は、想定される形式を示します。

### <a name="file-based-content"></a>ファイルベースのコンテンツ

パブリック ソースまたはローカル ファイル システムからナレッジ ベースにファイルを追加するには、[QnA Maker ポータル](https://www.qnamaker.ai)を使用します。

### <a name="content-format-guidelines"></a>コンテンツ形式のガイドライン

さまざまなファイルの[形式のガイドライン](../reference-document-format-guidelines.md)の詳細を確認してください。

## <a name="next-steps"></a>次のステップ

[QnAs の編集](../how-to/edit-knowledge-base.md)方法について説明します。
