---
title: 翻訳のカスタマイズ - Translator Text API
titleSuffix: Azure Cognitive Services
description: Microsoft Translator Hub を利用し、適切な用語とスタイルを使用して、独自の機械翻訳システムを構築します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257622"
---
# <a name="customize-your-text-translations"></a>テキスト変換をカスタマイズする

Microsoft Translator サービスの機能である Microsoft カスタム トランスレーターを使用すると、ユーザーは Translator Text API (バージョン 3 のみ) を使用してテキストを翻訳するときに、Microsoft Translator の高度なニューラル機械翻訳をカスタマイズすることができます。

この機能を [Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/) と組み合わせて使用し、音声の翻訳をカスタマイズすることもできます。

## <a name="custom-translator"></a>Custom Translator

Custom Translator を使用すると、独自のビジネスや業界で使用される用語を理解するニューラル翻訳システムを構築できます。 カスタマイズされた翻訳システムは、既存のアプリケーション、ワークフロー、Web サイトと統合します。

### <a name="how-does-it-work"></a>それはどのように機能しますか?

以前に翻訳したドキュメント (リーフレット、Web ページ、ドキュメントなど) を使用して、ドメイン固有の用語とスタイルが反映された、標準翻訳システムより優れた、翻訳システムを構築します。 ユーザーは、TMX、XLIFF、TXT、DOCX、XLSX ドキュメントをアップロードできます。  

システムは、ドキュメント レベルでは並列化されているが、センテンス レベルではまだ揃っていないデータも受け付けます。 ユーザーが同じコンテンツの複数言語による個別ドキュメントのバージョンにアクセスできる場合、Custom Translator はドキュメント間でセンテンスを自動的に一致させることができます。  また、システムは、一方または両方の言語で単一言語データを使用し、並列トレーニング データを補完して翻訳を向上させることもできます。

カスタマイズされたシステムは、カテゴリ パラメーターを使用する Microsoft Translator Text API の通常の呼び出しで利用できます。

適切な種類と量のトレーニング データがあれば、Custom Translator を使用することで、5 から 10 の間あるいはそれ以上の BLEU ポイントの翻訳品質を期待できることも珍しくありません。

使用可能なデータに基づくカスタマイズのさまざまなレベルについて詳しくは、「[Custom Translator User Guide](https://aka.ms/CustomTranslatorDocs)」(Custom Translator ユーザー ガイド) をご覧ください。


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> 従来の Microsoft Translator Hub は、2019 年 5 月 17 日をもって廃止されます。 [重要な移行情報と日付を確認してください](https://www.microsoft.com/translator/business/hub/)。  

## <a name="custom-translator-versus-hub"></a>Custom Translator と Hub

|   | **ハブ** | **Custom Translator**|
|:-----|:----:|:----:|
|カスタマイズ機能の状態   | 一般公開  | 一般公開 |
| Text API バージョン  | V2 のみ   | V3 のみ |
| SMT のカスタマイズ | はい   | いいえ |
| NMT のカスタマイズ | いいえ    | はい |
| 新しい統合 Speech Services のカスタマイズ | いいえ    | はい |
| [トレースなし](https://www.aka.ms/notrace) | はい  | はい |

## <a name="collaborative-translations-framework"></a>コラボレーション翻訳フレームワーク

> [!NOTE]
> 2018 年 2 月 1 日の時点で、AddTranslation() と AddTranslationArray() は Translator Text API V2.0 では使用できなくなっています。 これらのメソッドは失敗し、何も書き込まれません。 Translator Text API V3.0 はこれらのメソッドをサポートしません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Translator を使用してカスタマイズされた言語システムを設定します](https://aka.ms/CustomTranslatorDocs)
