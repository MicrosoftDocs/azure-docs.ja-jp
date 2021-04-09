---
title: マークダウン形式 - QnA Maker
description: QnA Maker の回答テキストで使用できるマークダウン形式の一覧を次に示します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352271"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker の回答テキストでサポートされるマークダウン形式

QnA Maker では、回答テキストがマークダウンとして格納されます。 マークダウンには、さまざまな決まりごとがあります。 回答テキストを確実に取得して正しく表示するために、このリファレンスをご利用ください。

Markdown の検証については、 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** のチュートリアルをご覧ください。 チュートリアルには、コピー/貼り付けで簡単に検証するための **[Try it]\(試してみる\)** 機能があります。

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>リッチテキスト編集を使用する場合と Markdown を使用する場合の比較

回答の[リッチテキスト編集](How-To/edit-knowledge-base.md#add-an-editorial-qna-set)では、作成者は、書式設定ツールバーを使用して、テキストをすばやく選択し、書式設定することができます。

Markdown は、CI/CD パイプラインの一部として、または[バッチ テスト](./index.yml)のためにインポートされるナレッジ ベースを作成するために、コンテンツを自動生成する必要がある場合に適しています。

## <a name="supported-markdown-format"></a>サポートされるマークダウン形式

QnA Maker の回答テキストで使用できるマークダウン形式の一覧を次に示します。

|目的|Format|マークダウンの例|表示<br>チャット ボットの表示|
|--|--|--|--|
2 つの文の間の改行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![2 つの文の間の改行を書式設定する](./media/qnamaker-concepts-datasources/format-newline.png)|
|h1 から h6 までのヘッダー。`#` の数はどのヘッダーかを表します。 1 `#` は h1 です。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![マークダウン ヘッダーを使用した書式設定](./media/qnamaker-concepts-datasources/format-headers.png)<br>![マークダウン ヘッダー H1 から H5 を使用した書式設定](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜体 |`*text*`|`How do I create a bot with *QnA Maker*?`|![斜体を使用した書式設定](./media/qnamaker-concepts-datasources/format-italics.png)|
|強調 (太字)|`**text**`|`How do I create a bot with **QnA Maker**?`|![太字の強力なマーキングを使用した書式設定](./media/qnamaker-concepts-datasources/format-strong.png)|
|リンクの URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL (ハイパーリンク) の書式設定](./media/qnamaker-concepts-datasources/format-url.png)|
|\* 公開画像の URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公開画像の URL の書式設定 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|取り消し線|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![取り消し線の書式設定](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|太字と斜体|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![太字と斜体の書式設定](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|リンクの太字の URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![太字の URL の書式設定](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|リンクの斜体の URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜体の URL の書式設定](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|マークダウン記号のエスケープ|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![マークダウン記号のエスケープの形式。](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|番号付きリスト|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>上の例では、マークダウンに組み込まれた自動番号付けを使用しています。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>上の例では明示的な番号付けを使用しています。|![番号付きリストの書式設定](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|記号付きリスト|`\n * item1 \n * item2`<br>or<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![記号付きリストの書式設定](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|入れ子になったリスト|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>番号付きリストと記号付きリストを一緒に入れ子にすることができます。 タブ `\t` は、子要素のインデント レベルを示します。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![入れ子になった記号付きリストの書式設定](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![入れ子になった番号付きリストの書式設定](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker ではどのような方法でも画像は処理されません。 画像をレンダリングするのはクライアント アプリケーションの役割です。

ナレッジベースの更新または置換 API を使用してコンテンツを追加する場合で、なおかつ、コンテンツまたはファイルに html タグが含まれている場合は、タグの開始と終了がエンコードされた形式に変換されるようにすることで、ファイル内の HTML を維持できます。

| HTML の維持  | API 要求での表記  | KB での表記 |
|-----------|---------|-------------------------|
| はい | \&lt;br\&gt; | &lt;br&gt; |
| はい | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;header&lt;/h3&gt; |

さらに、CR LF (\r\n) は KB では \n に変換されます。 LF (\n) はそのまま維持されます。 \t や \n のようなエスケープ シーケンスをエスケープする場合は、円記号を使用できます。例: '\\\\r\\\\n' および '\\\\t'

## <a name="next-steps"></a>次のステップ

バッチ テストの[ファイル形式](reference-tsv-format-batch-testing.md)について確認する