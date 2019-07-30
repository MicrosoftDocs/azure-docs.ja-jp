---
title: Web Language Model API の概要
titleSuffix: Azure Cognitive Services
description: Microsoft Cognitive Services の Web Language Model API には、自然言語処理のための最新ツールが用意されています。
services: cognitive-services
author: piyushbehre
manager: nitinme
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: piyush
ROBOTS: NOINDEX
ms.openlocfilehash: 066c7f8dbd6f64ba40ec539b636922069ee8b925
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849904"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Web Language Model API とは (プレビュー)

> [!IMPORTANT]
> Web Language Model プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

Microsoft Web Language Model API は、自然言語処理のための最新ツールを提供する REST ベースのクラウド サービスです。 アプリケーションでこの API を使用すると、Bing が en-US マーケットで収集した Web 規模のコーパスでトレーニングした言語モデルを通じて、ビッグ データの力を活用できます。

このように平滑化された N-gram のバックオフ言語モデルは、5 段階までのマルコフ連鎖をサポートし、次のコーパスでトレーニングされます。

- Web ページの本文テキスト
- Web ページのタイトル テキスト
- Web ページのアンカー テキスト
- Web 検索クエリ テキスト

Web Language Model API では、次の 4 つの検索操作がサポートされています。

1. 一連の単語の結合 (log10) 確率。
2. 与えられた一連の単語の前に 1 つの単語が登場する条件付き (log10) 確率。
3. 与えられた一連の単語に続く可能性が最も高い単語 (補完) の一覧。
4. スペースを含まない文字列の単語分割。

## <a name="getting-started"></a>Getting Started (概要)

1. サービスをサブスクライブします。
2. [SDK](https://www.github.com/microsoft/cognitive-weblm-windows) をダウンロードします。
3. SDK のサンプル コードを実行します。
4. エンドポイントの完全な説明は、[API リファレンス](https://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51)を参照してください。さまざまな言語のコード スニペットも含まれています。

## <a name="underlying-technology"></a>基礎となるテクノロジ

次の論文で、これらの言語モデルの開発について詳細に説明しています。このサービスを使用する研究発表では、この論文を引用する必要があります。

- [An Overview of Microsoft Web N-gram Corpus and Applications (Microsoft Web n-gram コーパスおよびアプリケーションの概要)](https://research.microsoft.com/apps/pubs/default.aspx?id=130762)、NAACL HLT 2010

[ここ](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0)をクリックすると、現在これを引用している論文の一覧を確認できます。
