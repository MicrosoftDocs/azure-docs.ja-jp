---
title: Custom Translator とは
titleSuffix: Azure Cognitive Services
description: Custom Translator は、統計的機械翻訳 (SMT) に対して Microsoft Translator Hub が提供するのと同様の機能を提供しますが、ニューラル機械翻訳 (NMT) システム専用です。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: e002a5fbe38596d6507eec81ba3dd2a05c25153a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975807"
---
# <a name="what-is-custom-translator"></a>Custom Translator とは

[Custom Translator](https://portal.customtranslator.azure.ai) は、翻訳会社、アプリ開発者、および言語サービス プロバイダーがカスタマイズされたニューラル機械翻訳 (NMT) システムを構築できるようにする Microsoft Translator サービスの一機能です。 カスタマイズされた翻訳システムは、既存のアプリケーション、ワークフロー、および Web サイトにシームレスに統合されます。 [Custom Translator](https://portal.customtranslator.azure.ai/) は、統計的機械翻訳 (SMT) に対して [Microsoft Translator Hub](https://hub.microsofttranslator.com/) が提供するのと同様の機能を提供しますが、ニューラル機械翻訳 (NMT) システム専用です。

[Custom Translator](https://portal.customtranslator.azure.ai) を使用して構築された翻訳システムは、[安全性](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)、高パフォーマンス、高いスケーラビリティを備えた、同じクラウドベースの Microsoft Translator [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) を介して利用できます。この API は、毎日何十億件もの翻訳に利用されています。

Custom Translator は、36 を超える言語をサポートし、NMT で利用可能な言語に直接マップされています。 完全な一覧については、[Microsoft Translator の言語](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)に関するページを参照してください。

## <a name="features"></a>機能

Custom Translator は、カスタム翻訳システムを構築し、その後そのシステムにアクセスするための各種機能を提供します。

|機能  |説明  |
|---------|---------|
|[ニューラル機械翻訳テクノロジを活用](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Custom Translator によって提供されるニューラル機械翻訳 (NMT) を活用して翻訳を改善します。       |
|[ビジネス用語を理解するシステムを構築](what-are-parallel-documents.md)     |  お客様のビジネスや業界で使用されている用語が反映されている対訳ドキュメントを使用して、翻訳システムをカスタマイズおよび構築します。       |
|[辞書を使用してモデルを構築](what-is-dictionary.md)     |   トレーニング データ セットがない場合は、辞書データのみを使用してモデルをトレーニングすることができます。       |
|[他の人と共同作業を行う](how-to-manage-settings.md#share-your-workspace)     |   他の人と作業を共有して、チームと共同作業を行います。     |
|[カスタム翻訳モデルにアクセス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Microsoft Translator Text API V3 を介して、既存のアプリケーション/プログラムからいつでもカスタム翻訳モデルにアクセスできます。       |

## <a name="get-better-translations"></a>より良い翻訳を得る

Microsoft Translator では、2016 年に[ニューラル機械翻訳 (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) がリリースされました。 NMT は、業界標準の[統計的機械翻訳 (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) テクノロジに比べて、翻訳品質の大幅な進歩をもたらしました。 NMT では、翻訳する前に文章全体のコンテキストをより適切に捉えることができるため、人間にとって違和感が少なく、より高品質で流暢な翻訳が提供されます。 [Custom Translator](https://portal.customtranslator.azure.ai) は、ユーザーのカスタム モデルに NMT を提供して、より高い翻訳品質を実現します。

過去に翻訳されたドキュメントを使用して、翻訳システムを構築することができます。 これらのドキュメントには分野に固有の用語とスタイルが含まれているため、標準的な翻訳システムよりも優れたものとなります。 ユーザーは、ALIGN、PDF、LCL、HTML、HTM、XLF、TMX、XLIFF、TXT、DOCX、および XLSX ドキュメントをアップロードできます。

Custom Translator では、データの収集と準備をより効果的に行うことができるように、ドキュメント レベルで対になっているデータも受け付けます。 複数の言語で書かれ、それぞれ別個のドキュメントとして保存されている同じコンテンツがユーザーの手元にある場合、Custom Translator は複数のドキュメント全体で文どうしを自動的に一致させることができます。

適切な種類と量のトレーニング データが提供されていれば、Custom Translator を使用することで [BLEU スコア](what-is-bleu-score.md)が 5 から 10 ポイント増加することは珍しくありません。

## <a name="be-productive-and-cost-effective"></a>生産性とコスト効果を高める

[Custom Translator](https://portal.customtranslator.azure.ai) では、カスタム システムのトレーニングとデプロイにプログラミング スキルは必要ありません。

ユーザーは、セキュリティで保護された [Custom Translator](https://portal.customtranslator.azure.ai) ポータルで直感的なユーザー インターフェイスを使用して、トレーニング データをアップロードし、システムのトレーニングとテスト、運用環境へのデプロイを行うことができます。 システムは、数時間以内に大規模に使用できるようになります (実際の時間はトレーニング データのサイズによって変わります)。

[Custom Translator](https://portal.customtranslator.azure.ai) は、プログラムを使って、(現在プレビュー段階の) [専用 API](https://custom-api.cognitive.microsofttranslator.com/swagger/) を介してアクセスすることもできます。 ユーザーは、この API を使用して、自分のアプリや Web サービスを通じて定期的なトレーニングの作成と更新を管理できます。

カスタム モデルを使用してコンテンツを翻訳するコストは、ユーザーの Translator Text API の価格レベルに基づきます。 価格レベルの詳細については、Cognitive Services の [Translator Text API の価格に関する Web ページ](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)を参照してください。

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>あらゆるアプリとサービスでいつでもどこでも安全に翻訳する

標準の REST テクノロジを使用して、Microsoft Translator Text API を経由すれば、カスタム システムにシームレスにアクセスできるほか、それをあらゆるデバイス上のすべての製品またはビジネス ワークフローに統合できます。

## <a name="next-steps"></a>次の手順

- [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)を確認します。

- [クイック スタート](quickstart-build-deploy-custom-model.md)で、Custom Translator を使用して翻訳モデルを構築する方法を学習します。
