---
title: Text Analytics API とは - 機能 -
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API を使用して、感情分析、キー フレーズ抽出、言語検出、およびエンティティ認識を行います。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 89a1fa396ff8bc5b954e91e7b08e6d731a81559a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657649"
---
# <a name="what-is-text-analytics-api"></a>Text Analytics API とは

Text Analytics API は、未加工のテキストに対して高度な自然言語処理を実行できるクラウドベースのサービスであり、主要な機能として感情分析、キー フレーズ抽出、言語検出、エンティティ認識の 4 つを備えています。

この API は、機械学習と AI のアルゴリズムを開発プロジェクトで利用できるようクラウドに集めた [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) に含まれます。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

テキスト分析にはさまざまな意味がありますが、Cognitive Services の場合、Text Analytics API には次のように 4 種類の分析が用意されています。

## <a name="sentiment-analysis"></a>感情分析
[感情分析](how-tos/text-analytics-how-to-sentiment-analysis.md)を使用すると、肯定的または否定的な感情の手がかりを探して未加工のテキストを分析することで、ブランドまたはトピックに対して顧客がどう思っているのかを突き止めることができます。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。<br /> 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](text-analytics-supported-languages.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) API または [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK を使用することができます。

## <a name="key-phrase-extraction"></a>キー フレーズ抽出
[キー フレーズを自動的に抽出](how-tos/text-analytics-how-to-keyword-extraction.md)し、重要な点を迅速に特定します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 ここで [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) API または [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK を使用することができます。

## <a name="language-detection"></a>言語検出
さまざまな言語、異形、方言、一部の地方言語や文化言語で、[入力テキストの記述言語を検出する](how-tos/text-analytics-how-to-language-detection.md)ほか、要求で送信されたドキュメントごとに 1 つの言語コードを報告することができます。 言語コードは、評価値の強度を示すスコアと組みになります。 [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) API または [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK を使用することができます。

## <a name="named-entity-recognition"></a>名前付きエンティティの認識
[テキスト内のエンティティを識別して分類します](how-tos/text-analytics-how-to-entity-linking.md) (人、場所、組織、日付/時刻、数量、パーセンテージ、通貨など)。 既知のエンティティも識別され、Web 上の詳細ページにリンクされます。 [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) API を使用できます。

## <a name="use-containers"></a>コンテナーの使用

キー フレーズの抽出、言語の検出、および感情の分析をローカルに行うには、標準化された Docker コンテナーをデータの近くにインストールして、[Text Analytics コンテナーを使用](how-tos/text-analytics-how-to-install-containers.md)します。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローは単純です。ユーザーが分析のためにデータを送信し、コードで出力を処理します。 アナライザーはそのままの状態で利用されます。構成やカスタマイズの追加はありません。

1. [新規登録](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)して[アクセス キー](how-tos/text-analytics-how-to-access-key.md)を入手します。 要求のたびにこのキーを渡す必要があります。

2. 未加工の非構造化テキストとしてデータを含む[要求を JSON で表します](how-tos/text-analytics-how-to-call-api.md#json-schema)。

3. 任意のリソース (感情分析、キー フレーズ抽出、言語検出、エンティティ識別) を付加し、新規登録時に確立されたエンドポイントに要求を投稿します。

4. 応答をローカルでストリームまたは保存します。 要求に基づき、結果は感情スコア、抽出されたキー フレーズのコレクション、または言語コードになります。

ID をベースに出力は 1 つの JSON ドキュメントとして返されます。投稿したテキスト ドキュメントごとに結果が返されます。 その後、結果を分析、視覚化、または分類し、実用的な情報を得ることができます。

データはユーザーのアカウントに保存されません。 Text Analytics API で実行された操作はステートレスです。つまり、入力したテキストが処理され、結果がすぐに返されます。

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>多様なプログラミング経験レベル向けの Text Analytics

プログラミングの経験があまりなくても、Text Analytics API を自分のプロセスで使い始めることができます。 これらのチュートリアルを使用して、経験レベルに合わせてさまざまな方法でテキストを分析するための API の使用方法を学習してください。 

* 最小限のプログラミングが必要:
    * [Text Analytics API と MS Flow を使用して、Yammer グループ内のコメントのセンチメントを特定する](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI と Text Analytics API を統合して顧客からのフィードバックを分析する](tutorials/tutorial-power-bi-key-phrases.md)
* プログラミング エクスペリエンスが推奨される:
    * [Azure Databricks を使用した、ストリーミング データに対する感情分析](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [テキストの翻訳、センチメントの分析、音声の合成を行う Flask アプリを作成する](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>サポートされている言語

このセクションは見つけやすいように別の記事に移されました。 このコンテンツについては、「[Supported languages in Text Analytics API](text-analytics-supported-languages.md)」 (Text Analytics API でサポートされている言語) を参照してください。

<a name="data-limits"></a>

## <a name="data-limits"></a>データ制限

Text Analytics API のエンドポイントはすべて、未加工のテキスト データを受け取ります。 現在の上限はドキュメントあたり 5,120 文字です。それより大きなドキュメントを分析する必要がある場合は小さなまとまりに分割できます。 分割ではなく、上限を増やす必要がある場合は[お問い合わせください](https://azure.microsoft.com/overview/sales-number/)。要件について説明いたします。

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) で測定される 5,120 文字。 |
| 要求全体の最大サイズ | 1 MB |
| 1 件の要求での最大ドキュメント数 | 1,000 ドキュメント |

レート制限は価格レベルによって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

要求数は、Text Analytics の機能ごとに個別に測定されます。 たとえば、各機能に対して、利用中の価格レベルで最大数の要求を同時に送信できます。      

## <a name="unicode-encoding"></a>Unicode エンコーディング

Text Analytics API では、テキストの表現と文字数の計算に Unicode エンコーディングが使用されます。 要求は UTF-8 と UTF-16 の両方で提出できます。文字数に測定可能な違いはありません。 Unicode コードポイントは文字の長さのヒューリスティックとして使用され、テキスト分析データを制限するという目的では同等と見なされます。 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) を利用して文字数を取得する場合、データ サイズの測定に使用しているものと同じ方法を使用することになります。

## <a name="next-steps"></a>次の手順

+ [新規登録](how-tos/text-analytics-how-to-signup.md)してアクセス キーを入手し、[API の呼び出し](how-tos/text-analytics-how-to-call-api.md)手順を確認します。

+ [こちらのクイック スタート](quickstarts/csharp.md)は C# で記述された REST API 呼び出しのチュートリアルです。 テキストの送信方法、分析の選択方法、最小コードで結果を表示する方法について学習してください。 好みに応じて、代わりに [Python クイック スタート](quickstarts/python.md)から始めることもできます。

+ Azure Databricks を使用するこちらの[感情分析のチュートリアル](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)では、少し詳しく説明されています。

+ [外部/コミュニティ コンテンツ ページ](text-analytics-resource-external-community.md)には、他のツールやテクノロジと共に Text Analytics API を使用する方法に関するブログ投稿や他の動画の一覧が掲載されています。
