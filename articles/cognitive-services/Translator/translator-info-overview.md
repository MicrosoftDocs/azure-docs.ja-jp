---
title: Translator Text API とは - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API をアプリケーション、Web サイト、ツール、またはその他のソリューションに統合して、多言語ユーザー エクスペリエンスを提供できます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 8f27e8e8693fc5d1d3e432b78f2877d71274f146
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978477"
---
# <a name="what-is-the-translator-text-api"></a>Translator Text API とは

Translator Text API は、皆さんのアプリケーションや Web サイト、ツール、ソリューションに簡単に統合することができます。 [60 か国語](languages.md)を超える多言語のユーザー エクスペリエンスを追加し、使用しているハードウェア プラットフォームやオペレーティング システムにかかわらず、テキストからテキストへの言語翻訳を実行できます。

Translator Text API は、機械学習と AI のアルゴリズムをクラウドで提供する Azure の [Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) コレクションの一部です。この API は、開発プロジェクトですぐに利用できます。

## <a name="about-microsoft-translator"></a>Microsoft Translator について

Microsoft Translator は、クラウドベースの機械翻訳サービスです。 その核となるサービス Translator Text API は、Microsoft のさまざまな製品やサービスに活かされているほか、世界中の膨大な数の企業がそのアプリケーションとワークフローに使用し、コンテンツを世界中のユーザーに届けることに成功しています。

Translator Text API によって支えられた音声翻訳は、[Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/) を通じて利用することもできます。 Translator Speech API と Custom Speech Service の機能が融合され、一元化された完全にカスタマイズ可能なサービスとなっています。 Speech Service は、Translator Speech API (2019 年 10 月 15 日に使用停止となる予定) の後継です。

## <a name="language-support"></a>言語のサポート

Microsoft Translator は、翻訳、表記変換、言語検出、辞書の多言語化を実現します。 サポートされる全言語のリストについては、[言語のサポート](language-support.md)に関するページを参照してください。このリストには、プログラムから [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) でアクセスすることもできます。  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator のニューラル機械翻訳

ニューラル機械翻訳 (NMT) は、高品質な AI による機械翻訳の新しい標準です。 2010 年半ばに品質の向上が停滞するようになった従来の統計的機械翻訳 (SMT) は、このテクノロジで置き換えられます。

NMT では、生の翻訳品質のスコアリングの観点からだけでなく、音声がより流ちょうで人間らしいため、SMT より優れた翻訳が提供されます。 こうした流ちょうさが得られるのは、NMT では 1 つの文章のコンテキスト全体を使用して言葉を変換することが主な理由です。 SMT で考慮されるのは、各単語の前後にある数語の直接的コンテキストのみです。

NMT モデルは、API のコアにあって、エンドユーザーからは見えません。 顕著な違いは翻訳品質の向上のみです。特に中国語、日本語、アラビア語などの言語で向上しています。

NMT の動作方法の詳細については、[こちら](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)を参照してください

## <a name="language-customization"></a>言語のカスタマイズ

コア Microsoft Translator サービスの拡張機能である Custom Translator は、Translator Text API と組み合わせて使用することで、ニューラル翻訳システムをカスタマイズし、特定の用語やスタイルの翻訳を改善することができます。

Custom Translator を使用すると、独自のビジネスや業界で使用される用語を処理するニューラル翻訳システムを構築できます。 カスタマイズされた翻訳システムは、通常の Microsoft Translator Text API でカテゴリ パラメーターを使用して、複数の種類のデバイス全体で既存のアプリケーション、ワークフロー、および Web サイトに簡単に統合できます。

[言語カスタマイズ](customization.md)の詳細

## <a name="next-steps"></a>次の手順

- [サインアップ](translator-text-how-to-signup.md)してアクセス キーを入手します。
- [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)には、API に関する技術文書があります。
- [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
