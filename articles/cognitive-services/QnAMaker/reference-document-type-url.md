---
title: インポートでサポートされている URL の種類 ‐ QnA Maker
description: QnA ペアのインポートおよび作成で URL の種類がどのように使用されるのかについて理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776699"
---
# <a name="urls-supported-for-importing-documents"></a>ドキュメントのインポートでサポートされている URL

QnA ペアのインポートおよび作成で URL の種類がどのように使用されるのかについて理解します。

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

## <a name="support-urls"></a>サポート URL

QnA Maker では、特定のタスクを実行する方法、特定の問題を診断および解決する方法、特定のプロセスに対するベスト プラクティスについて説明する Web 記事などの半構造化サポート Web ページを処理できます。 抽出が最もうまくいくのは、階層的な見出しのある明確な構造を持ったコンテンツです。

> [!NOTE]
> サポートの記事の抽出は新機能であり、初期段階にあります。 適切に構造化された、複雑なヘッダー/フッターが含まれていないシンプルなページで最適に動作します。

![QnA Maker では、明確な構造が階層的な見出しで表現されている半構造化 Web ページからの抽出がサポートされます](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)