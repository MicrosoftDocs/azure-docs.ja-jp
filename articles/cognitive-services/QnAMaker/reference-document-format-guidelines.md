---
title: インポート ドキュメント形式のガイドライン - QnA Maker
description: コンテンツに対して最善の結果が得られるように、ドキュメントをインポートするときはこれらのガイドラインを使用してください。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549543"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>インポートされたドキュメントおよび URL の形式のガイドライン

コンテンツに最適な結果を得るには、以下の書式設定ガイドラインを確認してください。

## <a name="formatting-considerations"></a>書式設定の考慮事項

ファイルまたは URL をインポートすると、QnA Maker によってコンテンツは[マークダウン形式](https://en.wikipedia.org/wiki/Markdown)に変換され、保存されます。 変換プロセスによって、テキストに `\n\n` などの新しい行が追加されます。 マークダウン形式の知識があると、変換されたコンテンツを理解し、ナレッジ ベース コンテンツを管理するために役立ちます。

ナレッジ ベースのコンテンツを直接追加または編集する場合は、**マークダウンの書式設定** を使用してリッチ テキスト コンテンツを作成するか、既に回答に含まれているマークダウン形式コンテンツを変更します。 QnA Maker は多くのマークダウン形式をサポートしているため、コンテンツにリッチ テキスト機能を取り込むことができます。 ただし、チャット ボットなどのクライアント アプリケーションは、同じマークダウン形式のセットをサポートしていない場合があります。 クライアント アプリケーションの回答の表示をテストすることが重要です。

[コンテンツの種類と例](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)の完全な一覧を参照してください。

## <a name="basic-document-formatting"></a>基本的なドキュメントの書式設定

QnA Maker では、次のような視覚的な手がかりに基づいて、ファイル内のセクション、サブセクション、関係を識別します。

* フォント サイズ
* フォント スタイル
* 番号付け
* 色

> [!NOTE]
> 現在、アップロードされたドキュメントからの画像の抽出はサポートされていません。

### <a name="product-manuals"></a>製品マニュアル

一般にマニュアルは、製品に付属するガイダンス資料です。 ユーザーにとっては、製品のセットアップ、使用、保守、トラブルシューティングにあたっての貴重な情報源となります。 QnA Maker は、マニュアルを処理する際、見出しと小見出しを質問として抽出し、質問に続くコンテンツを回答として抽出します。 [こちら](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)の例を参照してください。

次に示すのは、索引ページと階層的なコンテンツを持ったマニュアルの例です

 ![製品マニュアルの例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 抽出の対象として最も適しているのは、目次やインデックス ページがあり、階層化された見出しから成る明確な構造を持ったマニュアルです。

### <a name="brochures-guidelines-papers-and-other-files"></a>パンフレット、ガイドライン、論文、およびその他のファイル

明確な構造とレイアウトがあれば、他の多くの種類のドキュメントも処理して QA ペアを生成することができます。 これには以下が含まれます。パンフレット、ガイドライン、レポート、ホワイト ペーパー、科学論文、ポリシー、書籍などが含まれます。[こちら](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)の例を参照してください。

次に示すのは、索引のない半構造化ドキュメントの例です。

 ![Azure BLOB ストレージ半構造化ドキュメント](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>構造化 QnA ドキュメント

次に示すように、質問と回答の構造化 DOC ファイルの形式は、質問と回答を 1 行おきに並べる、つまり、1 つの質問を 1 行に記述し、次の行にその回答を記述する形式です。

```text
Question1

Answer1

Question2

Answer2
```

次に示すのは、構造化 QnA Word 文書の例です。

 ![構造化 QnA ドキュメントの例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>構造化 *TXT*、*TSV*、および *XLS* ファイル

構造化 *.txt*、 *.tsv*、または *.xls* ファイル形式の QnA を QnA Maker にアップロードして、ナレッジ ベースを作成または拡張することもできます。  これらはプレーン テキストでも、RTF または HTML のコンテンツが含まれていても構いません。

| Question  | Answer  | メタデータ (1 つのキー: 1 つの値) |
|-----------|---------|-------------------------|
| 質問 1 | 回答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 質問 2 | 回答 2 |      `Key:Value`           |

これより後の列は、ソース ファイルに含まれていても無視されます。

#### <a name="example-of-structured-excel-file"></a>構造化された Excel ファイルの例

次に示すのは、HTML コンテンツを含む構造化 QnA *.xls* ファイルの例です。

 ![構造化 QnA Excel の例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel ファイルにおける 1 つの回答に対する代替質問の例

以下は構造化されている QnA *.xls* ファイルの例です。1 つの回答に対して代替質問がいくつかあります。

 ![Excel ファイルにおける 1 つの回答に対する代替質問の例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

ファイルがインポートされた後、次に示すように、質問と回答の組み合わせがナレッジ ベースに入ります。

 ![ナレッジ ベースにインポートされた、1 つの質問に対する代替質問のスクリーンショット](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>インポートでの構造化データ形式

ナレッジ ベースをインポートすると、既にあるナレッジ ベースの内容が置き換えられます。 インポートでは、データ ソース情報を含んだ .tsv 形式の構造化ファイルが必要となります。 この情報によって、QnA Maker は質問とその回答のペアをグループ化し、その帰属先となるデータ ソースを特定することができます。

| Question  | Answer  | source| メタデータ (1 つのキー: 1 つの値) |
|-----------|---------|----|---------------------|
| 質問 1 | 回答 1 | URL 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 質問 2 | 回答 2 | 編集|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>複数ターンのドキュメントの書式設定

* 階層を示すには、見出しとサブ見出しを使用する。 たとえば、親 QnA を示すために h1、プロンプトとして使用する必要がある QnA を示すために h2 を使用できます。 後続の階層を示すには、小さい見出しサイズを使用する。 スタイル、色、その他のメカニズムを、ドキュメント内の構造を暗黙に示すために使用しないでください。QnA Maker では複数ターンのプロンプトが抽出されることはありません。
* 見出しの最初の文字は大文字にしなくてはなりません。
* 見出しの末尾に疑問符 `?` を付けないでください。

**サンプル ドキュメント**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>FAQ URL

QnA Maker がサポートできる FAQ ページの形式は、

* 基本的な FAQ ページ
* リンク付き FAQ ページ
* トピック ホームページがある FAQ ページ

### <a name="plain-faq-pages"></a>基本的な FAQ ページ

これは最も一般的なタイプの FAQ ページです。質問と回答が同じページにあり、質問のすぐ下に回答が記載されます。

次に示すのは、基本的な FAQ ページの例です。

![基本的な FAQ ページの例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>リンク付き FAQ ページ

このタイプの FAQ ページでは、質問が 1 か所に集約され、同じページ上の別のセクションまたは別のページのどちらかにある回答にリンクされます。

次に示すのは、同じページ上のセクションへのリンクがある FAQ ページの例です。

 ![セクションへのリンクがある FAQ ページの例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>親トピック ページの子回答ページへのリンク

この種類の FAQ にはトピック ページがあり、各トピックが別ページの対応する質問と回答のセットにリンクされています。 QnA Maker は、リンクされているすべてのページをクロールして、対応する質問と回答を抽出します。

次に、別ページの FAQ セクションへのリンクがあるトピック ページの例を示します。

 ![ディープ リンクがある FAQ ページの例 (ナレッジ ベース)](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>サポート URL

QnA Maker では、特定のタスクを実行する方法、特定の問題を診断および解決する方法、特定のプロセスに対するベスト プラクティスについて説明する Web 記事などの半構造化サポート Web ページを処理できます。 抽出が最もうまくいくのは、階層的な見出しのある明確な構造を持ったコンテンツです。

> [!NOTE]
> サポートの記事の抽出は新機能であり、初期段階にあります。 適切に構造化された、複雑なヘッダー/フッターが含まれていないシンプルなページで最適に動作します。

![QnA Maker では、明確な構造が階層的な見出しで表現されている半構造化 Web ページからの抽出がサポートされます](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>ナレッジ ベースのインポートとエクスポート

エクスポートされたナレッジ ベースからの **TSV ファイルと XLS ファイル** は、QnA Maker ポータルの **[設定]** ページからファイルをインポートすることによってのみ使用できます。 ナレッジベースの作成中に、または **[設定]** ページの **[+ ファイルの追加]** 機能または **[+ URL の追加]** 機能から、それらをデータ ソースとして使用することはできません。 

これらの **TSV および XLS ファイル** を通じてナレッジ ベースをインポートする場合、QnA ペアは、エクスポートされたナレッジ ベースでの QnA の抽出元のソースではなく、編集ソースに追加されます。 


## <a name="next-steps"></a>次のステップ

[コンテンツの種類と例](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)の完全な一覧を参照してください
