---
title: 翻訳のカスタマイズ - Translator
titleSuffix: Azure Cognitive Services
description: Microsoft Translator Hub を利用し、適切な用語とスタイルを使用して、独自の機械翻訳システムを構築します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898038"
---
# <a name="customize-your-text-translations"></a>テキスト変換をカスタマイズする

カスタム翻訳ツールは、Translator サービスの機能です。ユーザーは、これを使用して、Translator (バージョン 3 のみ) でテキストを翻訳するときに、Microsoft Translator の高度なニューラル機械翻訳をカスタマイズできます。

この機能を [Cognitive Services Speech](../speech-service/index.yml) と組み合わせて使用し、音声の翻訳をカスタマイズすることもできます。

## <a name="custom-translator"></a>Custom Translator

Custom Translator を使用すると、独自のビジネスや業界で使用される用語を理解するニューラル翻訳システムを構築できます。 カスタマイズされた翻訳システムは、既存のアプリケーション、ワークフロー、Web サイトと統合します。

### <a name="how-does-it-work"></a>それはどのように機能しますか?

以前に翻訳したドキュメント (リーフレット、Web ページ、ドキュメントなど) を使用して、ドメイン固有の用語とスタイルが反映された、標準翻訳システムより優れた、翻訳システムを構築します。 ユーザーは、TMX、XLIFF、TXT、DOCX、XLSX ドキュメントをアップロードできます。  

システムは、ドキュメント レベルでは並列化されているが、センテンス レベルではまだ揃っていないデータも受け付けます。 ユーザーが同じコンテンツの複数言語による個別ドキュメントのバージョンにアクセスできる場合、Custom Translator はドキュメント間でセンテンスを自動的に一致させることができます。  また、システムは、一方または両方の言語で単一言語データを使用し、並列トレーニング データを補完して翻訳を向上させることもできます。

その後、カテゴリ パラメーターを使用する Translator の通常の呼び出しで、カスタマイズされたシステムを使用できます。

適切な種類と量のトレーニング データがあれば、Custom Translator を使用することで、5 から 10 の間あるいはそれ以上の BLEU ポイントの翻訳品質を期待できることも珍しくありません。

使用可能なデータに基づくカスタマイズのさまざまなレベルについて詳しくは、「[Custom Translator User Guide](./custom-translator/overview.md)」(Custom Translator ユーザー ガイド) をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Translator を使用してカスタマイズされた言語システムを設定します](./custom-translator/overview.md)
