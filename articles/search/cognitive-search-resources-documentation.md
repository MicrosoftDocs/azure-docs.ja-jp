---
title: AI エンリッチメントのドキュメント リンク
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の AI エンリッチメント ワークロードに関する記事、チュートリアル、サンプル、およびブログ記事の注釈付きの一覧です。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935349"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search の AI エンリッチメントのドキュメント リソース

AI エンリッチメントは、インデクサーベースのインデクス作成のアドオンであり、テキスト以外のソースや区別されていないテキスト内の潜在的な情報を検索するインデックスを作成して、それを Azure Cognitive Search でフルテキスト検索可能なコンテンツに変換します。 

組み込み処理の場合、Cognitive Services の事前トレーニングされた AI モデルは、分析を実行するために内部的に呼び出されます。 Azure Machine Learning、Azure Functions、またはその他の方法を使用して、カスタム モデルを統合することもできます。

次に、AI エンリッチメントのドキュメントをまとめた一覧を示します。

## <a name="concepts"></a>概念

+ [AI エンリッチメント](cognitive-search-concept-intro.md)
+ [スキルセット](cognitive-search-working-with-skillsets.md)
+ [デバッグ セッション](cognitive-search-debug-session.md)
+ [ナレッジ ストア](knowledge-store-concept-intro.md)
+ [プロジェクション](knowledge-store-projection-overview.md)
+ [インクリメンタル エンリッチメント (キャッシュされたエンリッチされたドキュメントの再利用)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>ハンズオン チュートリアル

+ [クイック スタート: Azure portal でコグニティブ スキルセットを作成する](cognitive-search-quickstart-blob.md)
+ [チュートリアル:AI によるインデックスのエンリッチ](cognitive-search-tutorial-blob.md)
+ [チュートリアル:デバッグ セッションを使用してスキルセットに対する診断、修復、および変更のコミットを行う](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>ナレッジ ストア

+ [クイック スタート: Azure portal でナレッジ ストアを作成する](knowledge-store-create-portal.md)
+ [REST と Postman を使用してナレッジ ストアを作成する](knowledge-store-create-rest.md)
+ [Storage Explorer でナレッジ ストアを表示する](knowledge-store-view-storage-explorer.md)
+ [Power BI を使用してナレッジ ストアに接続する](knowledge-store-connect-power-bi.md)
+ [プロジェクションの例 (エンリッチメントを整形してエクスポートする方法)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>カスタム スキル (上級)

+ [カスタム スキルのインターフェイスを定義する方法](cognitive-search-custom-skill-interface.md)
+ [例:Azure Functions (および Bing Entity Search API) を使用してカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)
+ [例:Python を使用してカスタム スキルを作成する](cognitive-search-custom-skill-python.md)
+ [例:Form Recognizer を使用してカスタム スキルを作成する](cognitive-search-custom-skill-form.md) 
+ [例:Azure Machine Learning を使用してカスタム スキルを作成する](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>使用方法に関するガイダンス

+ [Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)
+ [スキルセットを定義する](cognitive-search-defining-skillset.md)
+ [スキルセットで注釈を参照する](cognitive-search-concept-annotations-syntax.md)
+ [インデックスにフィールドをマップする](cognitive-search-output-field-mapping.md)
+ [画像の情報を処理し、抽出する](cognitive-search-concept-image-scenarios.md)
+ [インクリメンタル エンリッチメントのためのキャッシュの構成](search-howto-incremental-index.md)
+ [Azure Cognitive Search インデックスを再構築する方法](search-howto-reindex.md)
+ [設計のヒント](cognitive-search-concept-troubleshooting.md)
+ [一般的なエラーと警告](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>スキルのリファレンス

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ カスタム スキル
  + [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [非推奨のスキル](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API

+ [REST API](/rest/api/searchservice/)
  + [スキルセットの作成 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
  + [インデクサーの作成 (api-version=2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search REST API](/rest/api/searchservice/)
+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [Azure Cognitive Search とは](search-what-is-azure-search.md)