---
title: Text Analytics API を使用したテキスト マイニングと分析 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Text Analytics API を使用したテキスト マイニングについて説明します。 感情分析、言語検出、およびその他の形式の自然言語処理に使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 02/09/2021
ms.author: aahi
keywords: テキスト マイニング、感情分析、テキスト分析
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: cb7bdeb379ee753424d9c975a8c030b0f7890552
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722281"
---
# <a name="what-is-the-text-analytics-api"></a>Text Analytics API とは

Text Analytics API は、テキスト マイニングとテキスト分析のための自然言語処理 (NLP) 機能を提供するクラウドベースのサービスであり、感情分析、オピニオン マイニング、キー フレーズ抽出、言語検出、名前付きエンティティの認識などを備えています。

この API は、機械学習と AI のアルゴリズムを開発プロジェクトで利用できるようクラウドに集めた [Azure Cognitive Services](../index.yml) に含まれます。 これらの機能は、REST API [バージョン 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/) または[バージョン 3.1-preview](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/)、あるいは[クライアント ライブラリ](quickstarts/client-libraries-rest-api.md)で使用できます。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

## <a name="sentiment-analysis"></a>センチメント分析

[感情分析](how-tos/text-analytics-how-to-sentiment-analysis.md)を使用して、テキストのマイニングにより肯定的または否定的な感情の手がかりを探すことで、ブランドまたはトピックに対して人々がどのように考えているかを突き止めます。 

この機能は、文章とドキュメントレベルでサービスによって検出された最も高い信頼度スコアに基づいて、センチメント ラベル ("negative"、"neutral"、"positive" など) を提供します。 また、この機能は、positive、neutral、negative (肯定的、中立的、否定的) のセンチメントに関し、各ドキュメントとその中の文章に対して 0 と 1 の間の信頼度スコアを返します。 また、[コンテナーを使用して](how-tos/text-analytics-how-to-install-containers.md)オンプレミスでサービスを実行することもできます。

バージョン 3.1 プレビュー以降では、オピニオン マイニングは感情分析の 1 つの機能となっています。 この機能は、自然言語処理 (NLP) ではアスペクトベースの感情分析とも呼ばれます。テキストに含まれるアスペクト (製品やサービスの属性など) に関連した意見について、より粒度の細かい情報が得られます。

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

[キー フレーズ抽出](how-tos/text-analytics-how-to-keyword-extraction.md)を使用して、テキスト内の主要な概念をすばやく特定します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストでは、キー フレーズ抽出は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。

## <a name="language-detection"></a>言語検出

言語検出を使用すると、さまざまな言語、異形、方言、一部の地方言語や文化言語で、[入力テキストの記述言語を検出する](how-tos/text-analytics-how-to-language-detection.md)ほか、要求で送信されたドキュメントごとに 1 つの言語コードを報告することができます。 言語コードは信頼度スコアとペアになっています。

## <a name="named-entity-recognition"></a>名前付きエンティティの認識

名前付きエンティティの認識 (NER) は、テキスト内のエンティティを、人、場所、組織、数量として[識別して分類](how-tos/text-analytics-how-to-entity-linking.md)できます。既知のエンティティも認識され、Web 上の詳細情報にリンクされます。

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

[Text Analytics コンテナーを使用](how-tos/text-analytics-how-to-install-containers.md)して、API 機能をオンプレミスにデプロイします。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにより近いところでサービスを使用できます。 Text Analytics には、次のコンテナーが用意されています。

* 感情分析
* キー フレーズ抽出 (プレビュー)
* 言語検出 (プレビュー)
* Text Analytics for Health (プレビュー)

## <a name="asynchronous-operations"></a>非同期操作

`/analyze` エンドポイントを使用すると、NER やキー フレーズ抽出などの Text Analytics API の選択した機能を[非同期に](how-tos/text-analytics-how-to-call-api.md)使用できます。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローは単純です。ユーザーが分析のためにデータを送信し、コードで出力を処理します。 アナライザーはそのままの状態で利用されます。構成やカスタマイズの追加はありません。

1. Text Analytics 用の [Azure リソースを作成](how-tos/text-analytics-how-to-call-api.md)します。 その後、対象の要求を認証するために生成された[キーを取得](how-tos/text-analytics-how-to-call-api.md)します。

2. 未加工の非構造化テキストとしてデータを含む[要求を JSON で表します](how-tos/text-analytics-how-to-call-api.md#json-schema)。

3. 任意のリソース (感情分析、キー フレーズ抽出、言語検出、名前付きエンティティの認識) を付加し、新規登録時に確立されたエンドポイントに要求を投稿します。

4. 応答をローカルでストリームまたは保存します。 要求に基づき、結果は感情スコア、抽出されたキー フレーズのコレクション、または言語コードになります。

ID をベースに出力は 1 つの JSON ドキュメントとして返されます。投稿したテキスト ドキュメントごとに結果が返されます。 その後、結果を分析、視覚化、または分類し、実用的な情報を得ることができます。

データはユーザーのアカウントに保存されません。 Text Analytics API で実行された操作はステートレスです。つまり、入力したテキストが処理され、結果がすぐに返されます。

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>多様なプログラミング経験レベル向けの Text Analytics

プログラミングの経験があまりなくても、Text Analytics API を自分のプロセスで使い始めることができます。 これらのチュートリアルを使用して、経験レベルに合わせてさまざまな方法でテキストを分析するための API の使用方法を学習してください。 

* 最小限のプログラミングが必要:
    * [Text Analytics と Power Automate を使用して Excel 内の情報を抽出する](tutorials/extract-excel-information.md)
    * [Text Analytics API と MS Flow を使用して、Yammer グループ内のコメントのセンチメントを特定する](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Power BI と Text Analytics API を統合して顧客からのフィードバックを分析する](tutorials/tutorial-power-bi-key-phrases.md)
* プログラミング エクスペリエンスが推奨される:
    * [Azure Databricks を使用した、ストリーミング データに対する感情分析](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [テキストの翻訳、センチメントの分析、音声の合成を行う Flask アプリを作成する](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>サポートされている言語

このセクションは見つけやすいように別の記事に移されました。 このコンテンツについては、[Text Analytics API でサポートされている言語](./language-support.md)に関するページを参照してください。

<a name="data-limits"></a>

## <a name="data-limits"></a>データ制限

Text Analytics API のエンドポイントはすべて、未加工のテキスト データを受け取ります。 詳細については、[データの制限](concepts/data-limits.md)に関する記事を参照してください。

## <a name="unicode-encoding"></a>Unicode エンコーディング

Text Analytics API では、テキストの表現と文字数の計算に Unicode エンコーディングが使用されます。 要求は UTF-8 と UTF-16 の両方で提出できます。文字数に測定可能な違いはありません。 Unicode コードポイントは文字の長さのヒューリスティックとして使用され、テキスト分析データを制限するという目的では同等と見なされます。 [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) を利用して文字数を取得する場合、データ サイズの測定に使用しているものと同じ方法を使用することになります。

## <a name="next-steps"></a>次のステップ

+ Text Analytics 用の [Azure リソースを作成](../cognitive-services-apis-create-account.md)して、対象のアプリケーションのキーとエンドポイントを取得します。

+ [クイックスタート](quickstarts/client-libraries-rest-api.md)を使用して、API 呼び出しの送信を開始します。 テキストの送信方法、分析の選択方法、最小コードで結果を表示する方法について学習してください。

+ 新しいリリースおよび機能については、[Text Analytics API の新機能](whats-new.md)に関するページを参照してください。

+ Azure Databricks を使用するこちらの[感情分析のチュートリアル](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services)では、少し詳しく説明されています。

+ [外部およびコミュニティ コンテンツに関するページ](text-analytics-resource-external-community.md)には、他のツールやテクノロジと共に Text Analytics API を使用する方法に関するブログ投稿や他の動画の一覧が掲載されています。
