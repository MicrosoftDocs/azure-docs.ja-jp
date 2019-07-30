---
title: サポートされているデータ ソース - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker は、Web ページ、PDF ファイル、または MS Word doc ファイルとして保存された FAQ、製品マニュアル、ガイドライン、サポート ドキュメント、ポリシーなどの半構造化コンテンツから質問と回答のペアを自動的に抽出します。 また、構造化 QnA コンテンツ ファイルのコンテンツをナレッジ ベースに追加することもできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: diberry
ms.openlocfilehash: efb2524b430935e6c74415efe850b69835825bc7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447682"
---
# <a name="data-sources-for-qna-maker-content"></a>QnA Maker コンテンツのデータ ソース

QnA Maker は、Web ページ、PDF ファイル、または MS Word doc ファイルとして保存された FAQ、製品マニュアル、ガイドライン、サポート ドキュメント、ポリシーなどの半構造化コンテンツから質問と回答のペアを自動的に抽出します。 また、構造化 QnA コンテンツ ファイルのコンテンツをナレッジ ベースに追加することもできます。 

次の表は、QnA Maker でサポートされているコンテンツの種類とファイル形式をまとめたものです。

|ソースの種類|コンテンツの種類| 例|
|--|--|--|
|URL|FAQ<br> (基本、セクション付き、またはトピックのホーム ページ付き)<br>サポート ページ <br> (単一ページのハウツー記事や、トラブルシューティングの記事など)|[基本的な FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[リンク付きの FAQ](https://www.microsoft.com/software-download/faq)、<br> [トピック ホーム ページ付きの FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[サポート記事](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQ、<br> 製品マニュアル、<br> パンフレット、<br> 論文、<br> チラシ ポリシー、<br> サポート ガイド、<br> 構造化 QnA、<br> など|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|構造化 QnA ファイル<br> (RTF、HTML サポートなど)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|構造化 QnA ファイル|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>データ ソースの場所

ほとんどのデータ ソースの場所には、認証を必要としないパブリック URL またはファイルを指定する必要があります。 

[Sharepoint データ ソースの場所](../How-to/add-sharepoint-datasources.md)には、認証済みのファイルを指定することができます。 Sharepoint リソースは、Web ページではなく、ファイルになっている必要があります。 

認証済みのファイルまたは URL がある場合、別のオプションとして、認証済みのサイトからローカル コンピューターにファイルをダウンロードしてから、ローカル コンピューターからナレッジ ベースにファイルを追加できます。 

## <a name="faq-urls"></a>FAQ URL

QnA Maker がサポートできる FAQ ページの形式は、基本的な FAQ ページ、リンク付き FAQ ページ、トピックのホーム ページ付き FAQ ページの 3 種類です。

### <a name="plain-faq-pages"></a>基本的な FAQ ページ

これは最も一般的なタイプの FAQ ページです。質問と回答が同じページにあり、質問のすぐ下に回答が記載されます。 

次に示すのは、基本的な FAQ ページの例です。

![基本的な FAQ ページの例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>リンク付き FAQ ページ 

このタイプの FAQ ページでは、質問が 1 か所に集約され、同じページ上の別のセクションまたは別のページのどちらかにある回答にリンクされます。

次に示すのは、同じページ上のセクションへのリンクがある FAQ ページの例です。

 ![セクションへのリンクがある FAQ ページの例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>トピックのホーム ページ付き FAQ ページ

このタイプの FAQ にはトピックのホーム ページがあり、それぞれのトピックは別のページにある関連 QnA へのリンクです。 ここでは、QnA Maker は、リンクされているすべてのページをクロールして、対応する質問と回答を抽出します。

次に示すのは、別のページ内の FAQ セクションへのリンクがトピックのホーム ページにある FAQ ページの例です。 

 ![ディープ リンクがある FAQ ページの例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>サポート URL

QnA Maker では、特定のタスクを実行する方法、特定の問題を診断および解決する方法、特定のプロセスに対するベスト プラクティスについて説明する Web 記事などの半構造化サポート Web ページを処理できます。 抽出が最もうまくいくのは、階層的な見出しのある明確な構造を持ったコンテンツです。

> [!NOTE]
> サポートの記事の抽出は新機能であり、初期段階にあります。 適切に構造化された、複雑なヘッダー/フッターが含まれていないシンプルなページで最適に動作します。

![QnA Maker では、明確な構造が階層的な見出しで表現されている半構造化 Web ページからの抽出がサポートされます](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/ DOC ファイル

QnA Maker では、PDF または DOC ファイルの半構造化コンテンツを処理し、QnA に変換することができます。 正しく抽出できる適切なファイルは、コンテンツが何らかの構造化形式で整理され、適切に定義されたセクションで表現されているファイルです。 セクションはさらにサブセクションまたはサブトピックに分けることができます。 抽出が最もうまくいくのは、階層的な見出しのある明確な構造を持ったドキュメントです。

QnA Maker は、フォント サイズ、フォント スタイル、ナンバリング、色などの視覚的な手がかりに基づいて、ファイル内のセクション、サブセクション、関係を識別します。半構造化 PDF または DOC ファイルには、マニュアル、FAQ、ガイドライン、ポリシー、パンフレット、チラシ、その他多くの種類のファイルがあります。 次に示すのは、これらのファイルの種類の例です。

### <a name="product-manuals"></a>製品マニュアル

一般にマニュアルは、製品に付属するガイダンス資料です。 ユーザーにとっては、製品のセットアップ、使用、保守、トラブルシューティングにあたっての貴重な情報源となります。 QnA Maker は、マニュアルを処理する際、見出しと小見出しを質問として抽出し、質問に続くコンテンツを回答として抽出します。 [こちら](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)の例を参照してください。

次に示すのは、索引ページと階層的なコンテンツを持ったマニュアルの例です

 ![製品マニュアルの例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> 抽出の対象として最も適しているのは、目次やインデックス ページがあり、階層化された見出しから成る明確な構造を持ったマニュアルです。

### <a name="brochures-guidelines-papers-and-other-files"></a>パンフレット、ガイドライン、論文、およびその他のファイル

明確な構造とレイアウトがあれば、他の多くの種類のドキュメントも処理して QA ペアを生成することができます。 チェックの内容は次のとおりですパンフレット、ガイドライン、レポート、ホワイト ペーパー、科学論文、ポリシー、書籍などが含まれます。[こちら](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)の例を参照してください。

次に示すのは、索引のない半構造化ドキュメントの例です。

 ![Azure BLOB ストレージ半構造化ドキュメント](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>構造化 QnA ドキュメント

次に示すように、質問と回答の構造化 DOC ファイルの形式は、質問と回答を 1 行おきに並べる、つまり、1 つの質問を 1 行に記述し、次の行にその回答を記述する形式です。 

```text
Question1

Answer1

Question2

Answer2
```

次に示すのは、構造化 QnA Word 文書の例です。

 ![構造化 QnA ドキュメントの例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>構造化 *TXT*、*TSV*、および *XLS* ファイル

構造化 *.txt*、 *.tsv*、または *.xls* ファイル形式の QnA を QnA Maker にアップロードして、ナレッジ ベースを作成または拡張することもできます。  これらはプレーン テキストでも、RTF または HTML のコンテンツが含まれていても構いません。 

| 質問  | Answer  | メタデータ (1 つのキー: 1 つの値) |
|-----------|---------|-------------------------|
| 質問 1 | 回答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 質問 2 | 回答 2 |      `Key:Value`           |

これより後の列は、ソース ファイルに含まれていても無視されます。

次に示すのは、HTML コンテンツを含む構造化 QnA *.xls* ファイルの例です。

 ![構造化 QnA Excel の例 (ナレッジ ベース)](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>インポートでの構造化データ形式

ナレッジ ベースをインポートすると、既にあるナレッジ ベースの内容が置き換えられます。 インポートでは、データ ソース情報を含んだ .tsv 形式の構造化ファイルが必要となります。 この情報によって、QnA Maker は質問とその回答のペアをグループ化し、その帰属先となるデータ ソースを特定することができます。

| 質問  | Answer  | source| メタデータ (1 つのキー: 1 つの値) |          
|-----------|---------|----|---------------------|
| 質問 1 | 回答 1 | URL 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 質問 2 | 回答 2 | 編集|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>編集という形でナレッジ ベースに追加する

ナレッジ ベースの内容とする既存のコンテンツがない場合は、QnA Maker のナレッジ ベースに編集という形で QnA を追加することができます。 ナレッジ ベースの更新方法については、[こちら](../How-To/edit-knowledge-base.md)を参照してください。

## <a name="formatting-considerations"></a>書式設定の考慮事項

ファイルまたは URL をインポートした後は Markdown に変換され、その形式で保存されます。 変換プロセスでファイルと URL のリンクが正しく変換されない場合は、 **[編集]** ページで質問と回答を編集する必要があります。 

|形式|目的|
|--|--|
|`\n\n`| 改行|
|`\n*`|順序付きリストの行頭文字|

## <a name="editing-your-knowledge-base-locally"></a>ナレッジ ベースのローカルな編集

ナレッジ ベースを作成した後は、ローカル ファイルにエクスポートして再インポートするのではなく、[QnA Maker ポータル](https://qnamaker.ai)でナレッジ ベースのテキストを編集することをお勧めします。 ただし、ローカル環境でナレッジ ベースを編集することが必要になる場合があります。 

**[設定]** ページからナレッジ ベースをエクスポートした後、Microsoft Excel でナレッジ ベースを編集します。 エクスポートした TSV ファイルを別のアプリケーションで編集した場合、アプリケーションが TSV に完全に準拠していないため、構文エラーが発生する可能性があります。 Microsoft Excel の TSV ファイルでは一般に、書式エラーが発生することはありません。 

編集が終わったら、 **[設定]** ページから TSV ファイルを再インポートします。 これにより、現在のナレッジ ベースが、インポートしたナレッジ ベースに完全に置き換えられます。 

## <a name="testing-your-markdown"></a>Markdown のテスト

Markdown の検証については、 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** のチュートリアルをご覧ください。 チュートリアルには、コピー/貼り付けで簡単に検証するための **[Try it]\(試してみる\)** 機能があります。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker サービスの設定](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)
