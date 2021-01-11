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
ms.date: 08/27/2020
ms.author: aahi
ms.openlocfilehash: a3c538f3a9e7a2d8d71fff38fb927dbcdf725732
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000959"
---
# <a name="what-is-the-text-analytics-api"></a>Text Analytics API とは

Text Analytics API は、未加工のテキストに対して高度な自然言語処理を実行できるクラウドベースのサービスであり、主要な機能として感情分析、キー フレーズ抽出、言語検出、名前付きエンティティの認識の 4 つを備えています。

この API は、機械学習と AI のアルゴリズムを開発プロジェクトで利用できるようクラウドに集めた [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) に含まれます。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

テキスト分析にはさまざまな意味がありますが、Cognitive Services の場合、Text Analytics API には次のように 4 種類の分析が用意されています。 これらの機能は [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) または[クライアント ライブラリ](quickstarts/text-analytics-sdk.md)で使用できます。

## <a name="sentiment-analysis"></a>感情分析
[感情分析](how-tos/text-analytics-how-to-sentiment-analysis.md)を使用すると、肯定的または否定的な感情の手がかりを探して未加工のテキストを分析することで、ブランドまたはトピックに対して顧客がどう思っているのかを突き止めることができます。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。<br /> 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](text-analytics-supported-languages.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。

## <a name="key-phrase-extraction"></a>キー フレーズ抽出
[キー フレーズを自動的に抽出](how-tos/text-analytics-how-to-keyword-extraction.md)し、重要な点を迅速に特定します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。

## <a name="language-detection"></a>言語検出
さまざまな言語、異形、方言、一部の地方言語や文化言語で、[入力テキストの記述言語を検出する](how-tos/text-analytics-how-to-language-detection.md)ほか、要求で送信されたドキュメントごとに 1 つの言語コードを報告することができます。 言語コードは、評価値の強度を示すスコアと組みになります。

## <a name="named-entity-recognition"></a>名前付きエンティティの認識
[テキスト内のエンティティを識別して分類します](how-tos/text-analytics-how-to-entity-linking.md) (人、場所、組織、日付/時刻、数量、パーセンテージ、通貨など)。 既知のエンティティも識別され、Web 上の詳細ページにリンクされます。

## <a name="use-containers"></a>コンテナーの使用

キー フレーズの抽出、言語の検出、および感情の分析をローカルに行うには、標準化された Docker コンテナーをデータの近くにインストールして、[Text Analytics コンテナーを使用](how-tos/text-analytics-how-to-install-containers.md)します。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローは単純です。ユーザーが分析のためにデータを送信し、コードで出力を処理します。 アナライザーはそのままの状態で利用されます。構成やカスタマイズの追加はありません。

1. Text Analytics 用の [Azure リソースを作成](../cognitive-services-apis-create-account.md)します。 その後、対象の要求を認証するために生成された[キーを取得](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)します。

2. 未加工の非構造化テキストとしてデータを含む[要求を JSON で表します](how-tos/text-analytics-how-to-call-api.md#json-schema)。

3. 任意のリソース (感情分析、キー フレーズ抽出、言語検出、名前付きエンティティの認識) を付加し、新規登録時に確立されたエンドポイントに要求を投稿します。

4. 応答をローカルでストリームまたは保存します。 要求に基づき、結果は感情スコア、抽出されたキー フレーズのコレクション、または言語コードになります。

ID をベースに出力は 1 つの JSON ドキュメントとして返されます。投稿したテキスト ドキュメントごとに結果が返されます。 その後、結果を分析、視覚化、または分類し、実用的な情報を得ることができます。

データはユーザーのアカウントに保存されません。 Text Analytics API で実行された操作はステートレスです。つまり、入力したテキストが処理され、結果がすぐに返されます。

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>多様なプログラミング経験レベル向けの Text Analytics

プログラミングの経験があまりなくても、Text Analytics API を自分のプロセスで使い始めることができます。 これらのチュートリアルを使用して、経験レベルに合わせてさまざまな方法でテキストを分析するための API の使用方法を学習してください。 

* 最小限のプログラミングが必要:
    * [Text Analytics と Power Automate を使用して Excel 内の情報を抽出する](tutorials/extract-excel-information.md)
    * [Text Analytics API と MS Flow を使用して、Yammer グループ内のコメントのセンチメントを特定する](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI と Text Analytics API を統合して顧客からのフィードバックを分析する](tutorials/tutorial-power-bi-key-phrases.md)
* プログラミング エクスペリエンスが推奨される:
    * [Azure Databricks を使用した、ストリーミング データに対する感情分析](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [テキストの翻訳、センチメントの分析、音声の合成を行う Flask アプリを作成する](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>サポートされている言語

このセクションは見つけやすいように別の記事に移されました。 このコンテンツについては、[Text Analytics API でサポートされている言語](text-analytics-supported-languages.md)に関するページを参照してください。

<a name="data-limits"></a>

## <a name="data-limits"></a>データ制限

Text Analytics API のエンドポイントはすべて、未加工のテキスト データを受け取ります。 詳細については、[データの制限](concepts/data-limits.md)に関する記事を参照してください。

## <a name="unicode-encoding"></a>Unicode エンコーディング

Text Analytics API では、テキストの表現と文字数の計算に Unicode エンコーディングが使用されます。 要求は UTF-8 と UTF-16 の両方で提出できます。文字数に測定可能な違いはありません。 Unicode コードポイントは文字の長さのヒューリスティックとして使用され、テキスト分析データを制限するという目的では同等と見なされます。 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) を利用して文字数を取得する場合、データ サイズの測定に使用しているものと同じ方法を使用することになります。

## <a name="next-steps"></a>次のステップ

+ Text Analytics 用の [Azure リソースを作成](../cognitive-services-apis-create-account.md)して、対象のアプリケーションのキーとエンドポイントを取得します。

+ [クイックスタート](quickstarts/text-analytics-sdk.md)を使用して、API 呼び出しの送信を開始します。 テキストの送信方法、分析の選択方法、最小コードで結果を表示する方法について学習してください。

+ 新しいリリースおよび機能については、[Text Analytics API の新機能](whats-new.md)に関するページを参照してください。

+ Azure Databricks を使用するこちらの[感情分析のチュートリアル](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)では、少し詳しく説明されています。

+ [外部およびコミュニティ コンテンツに関するページ](text-analytics-resource-external-community.md)には、他のツールやテクノロジと共に Text Analytics API を使用する方法に関するブログ投稿や他の動画の一覧が掲載されています。
