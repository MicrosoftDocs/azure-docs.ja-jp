---
title: コグニティブ検索のドキュメント リソース - Azure Search
description: Azure Search でのコグニティブ検索ワークロードに関する記事、チュートリアル、サンプル、およびブログ記事の注釈付きの一覧です。
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 609b5d990cffce10733f6fc82e6b1032ad0f06bb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314603"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>コグニティブ検索ワークロードに関するドキュメント リソース

パブリック プレビューになったコグニティブ検索は、Azure Search における新しい強化層で、テキスト以外のソースや区別されていないテキスト内の潜在的な情報を検索するインデックスを作成して、それを Azure Search でフルテキスト検索可能なコンテンツに変換します。

次の記事は、コグニティブ検索に関する完全なドキュメントです。

## <a name="getting-started"></a>使用の開始
+ [コグニティブ検索とは](cognitive-search-concept-intro.md)
+ [クイック スタート:ポータルでコグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
+ [チュートリアル:コグニティブ検索 API について学習する](cognitive-search-tutorial-blob.md)
+ [例: カスタム スキルの作成](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>使用方法に関するガイダンス
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットで注釈を参照する方法](cognitive-search-concept-annotations-syntax.md)
+ [インデックスにフィールドをマップする方法](cognitive-search-output-field-mapping.md)
+ [画像の情報を処理し、抽出する方法](cognitive-search-concept-image-scenarios.md)
+ [Azure Search インデックスを再構築する方法](search-howto-reindex.md)
+ [カスタム スキルのインターフェイスを定義する方法](cognitive-search-custom-skill-interface.md)
+ [トラブルシューティングのヒント](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>リファレンス

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [プレビュー REST API](search-api-2017-11-11-preview.md)
  + [スキルセットの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [インデクサーの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>関連項目

+ [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure Search のインデクサー](search-indexer-overview.md)
+ [Azure Search とは](search-what-is-azure-search.md)
