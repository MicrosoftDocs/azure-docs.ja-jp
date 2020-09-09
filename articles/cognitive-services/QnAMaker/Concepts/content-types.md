---
title: コンテンツの種類 - QnA Maker
description: コンテンツの種類には、PDF、DOCX、TXT など、多くの標準的な構造化ドキュメントが含まれています。
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 04d7c46a7aa347718cf6bc593a92a2f0430eb6fd
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263165"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>ナレッジ ベースに追加できるドキュメントのコンテンツの種類
コンテンツの種類には、PDF、DOC、TXT など、多くの標準的な構造化ドキュメントが含まれています。

## <a name="file-and-url-data-types"></a>ファイルと URL のデータの種類

次の表は、QnA Maker でサポートされているコンテンツの種類とファイル形式をまとめたものです。

|ソースの種類|コンテンツの種類| 例|
|--|--|--|
|URL|FAQ<br> (基本、セクション付き、またはトピックのホーム ページ付き)<br>サポート ページ <br> (単一ページのハウツー記事や、トラブルシューティングの記事など)|[基本的な FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[リンク付きの FAQ](https://www.microsoft.com/en-us/software-download/faq)、<br> [トピック ホーム ページ付きの FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[サポート記事](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQ、<br> 製品マニュアル、<br> パンフレット、<br> 論文、<br> チラシ ポリシー、<br> サポート ガイド、<br> 構造化 QnA、<br> など|**マルチターンなし**<br>[Structured QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)、<br><br>**マルチターン**：<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|構造化 QnA ファイル<br> (RTF、HTML サポートなど)|**マルチターンなし**:<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**マルチターン**：<br>[構造化された簡単な FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface ノート PC の FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|構造化 QnA ファイル|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

データ ソースに認証が必要な場合、QnA Maker にそのコンテンツを取り込む次の方法を検討してください。

* ファイルを手動でダウンロードし、QnA Maker にインポートする
* 認証された [SharePoint の場所](../how-to/add-sharepoint-datasources.md)からファイルを追加する

## <a name="url-content"></a>URL コンテンツ

QnA Maker で **URL** を使用して、次の 2 種類のドキュメントをインポートできます。

* FAQ URL
* サポート URL

各種類は、想定される形式を示します。

## <a name="file-based-content"></a>ファイルベースのコンテンツ

パブリック ソースまたはローカル ファイル システムからナレッジ ベースにファイルを追加するには、[QnA Maker ポータル](https://www.qnamaker.ai)を使用します。

## <a name="content-format-guidelines"></a>コンテンツ形式のガイドライン

さまざまなファイルの[形式のガイドライン](../reference-document-format-guidelines.md)の詳細を確認してください。

## <a name="next-steps"></a>次のステップ

[質問と回答 (QnA) ペア](question-answer-set.md)に格納されている情報について理解します。
