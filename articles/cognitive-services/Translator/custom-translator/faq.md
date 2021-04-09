---
title: よく寄せられる質問 - カスタム翻訳ツール
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Custom Translator についてよく寄せられる質問とその回答を示します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 001314817b0c18a8023258d01bcfb02eaaffe79b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895834"
---
# <a name="custom-translator-frequently-asked-questions"></a>カスタム翻訳ツールに関してよく寄せられる質問

この記事では、[Custom Translator](https://portal.customtranslator.azure.ai) についてよく寄せられる質問とその回答を示します。

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>現在、Custom Translator にはどのような制限がありますか。

ファイル サイズ、モデルのトレーニング、モデル デプロイについては、制約と制限があります。 Custom Translator でモデルを構築するためのトレーニングを設定するときは、これらの制約に留意してください。

- 送信されるファイルのサイズは 100 MB 未満である必要があります。
- モノリンガル データはサポートされていません。

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>トレーニングを実行した翻訳システムのデプロイはいつ要求すればよいですか。

プロジェクトに最適な翻訳システムを作成するために、複数のトレーニングを実行することがあります。 BLEU スコアやテスト結果に満足できない場合は、トレーニング データを増やすか、より慎重にフィルター処理されたデータを使用してみることをお勧めします。 翻訳する素材の用語やスタイルを完全に表すには、チューニング セットとテスト セットの設計を厳密かつ慎重に行う必要があります。 トレーニング データの作成時はより自由に取り組み、さまざまなオプションを使用して実験を行うことができます。 システムのテスト結果の翻訳に満足し、トレーニング済みシステムを改善するためにトレーニングに追加できるデータがもう存在せず、API を介してトレーニング済みモデルにアクセスしたい場合は、システムのデプロイを要求してください。

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>1 つのプロジェクトでデプロイできるトレーニング済みシステムの数はいくつですか。

1 つのプロジェクトでデプロイできるトレーニング済みシステムは 1 つのみです。 プロジェクトに適した翻訳システムを作成するために複数のトレーニングが必要な場合があります。最適な結果を達成できるトレーニングのデプロイを要求することをお勧めします。 翻訳の品質がデプロイに適していると判断する前に、BLEU スコア (高い方が良い) と、レビュー担当者への相談で、トレーニングの品質を判断することができます。

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>トレーニングはどのくらいでデプロイされますか。

一般的に、デプロイにかかる時間は 1 時間未満です。

## <a name="how-do-you-access-a-deployed-system"></a>デプロイされたシステムにアクセスする方法を教えてください。

デプロイされたシステムには、Microsoft Translator Text API V3 を介して CategoryID を指定することでアクセスできます。 Translator Text API の詳細については、[API リファレンス](../reference/v3-0-reference.md) Web ページを参照してください。

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>データに文が既にアラインされている場合に、アラインと文の分割をスキップする方法を教えてください。

Custom Translator では、TMX ファイルと拡張子が `.align` のテキスト ファイルの場合に、文のアラインと文の区切りがスキップされます。 `.align` ファイルは、完全にアラインされ、それ以上の処理が不要なファイルに対するカスタム トランスレーターの文分割およびアライン処理をスキップするためのオプションです。 完全にアラインされているファイルにのみ、`.align` の拡張子を使用することをお勧めします。

抽出された文の数が同じベース名の 2 つのファイルと一致しない場合、Custom Translator では `.align` ファイルに対して文のアライン機能が実行されます。

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>TMX をアップロードしようとすると、"ドキュメントの処理に失敗しました" と表示されます。


TMX が <https://www.gala-global.org/tmx-14b> の TMX 1.4b 仕様に準拠していることを確認してください。