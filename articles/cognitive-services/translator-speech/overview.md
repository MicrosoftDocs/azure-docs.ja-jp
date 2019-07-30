---
title: Translator Speech サービスとは
titleSuffix: Azure Cognitive Services
description: Translator Speech サービス API を使用して、アプリケーションに音声間の翻訳や音声テキスト変換を追加します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ed425cd6545d85cdf3b2f76038b381a0f09045f9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449655"
---
# <a name="what-is-translator-speech-api"></a>Translator Speech API とは

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech API は、対象となる OS や開発言語に関係なく、多言語音声翻訳を必要とするアプリケーション、ツール、ソリューションに、エンドツーエンドのリアルタイム音声翻訳を追加するために使用できます。 この API は、音声間の翻訳と、音声テキスト変換の両方に使用できます。

Translator Text API は Azure のサービスで、機械学習と AI のアルゴリズムをクラウドで提供する [Azure Cognitive Services API コレクション](https://docs.microsoft.com/azure/)の一部です。この API は、開発プロジェクトですぐに利用できます。

Translator Speech API を使用するクライアント アプリケーションは、音声オーディオをサービスにストリーミングして、テキスト ベースと音声ベースの結果のストリームを受け取ります。このストリームには、ソース言語での認識されたテキストと、ターゲット言語での翻訳が含まれます。 テキストでの結果は、受信オーディオ ストリームにディープ ニューラル ネットワークを利用する自動音声認識 (ASR) を適用して生成されます。 ユーザーの意図をより忠実に反映させるため、TrueText と呼ばれる新しい手法によって、生の ASR 出力がさらに改善されます。 たとえば TrueText では、吃音 (うーんといった声や咳) や繰り返しの言葉を取り除き、正しい句読法と、大文字および小文字の区別を復元します。 不適切な表現をマスクしたり除外したりする機能も含まれています。 認識エンジンおよび翻訳エンジンは、会話音声を処理するように特にトレーニングされます。

Translator Speech サービスでは、無音の検出を利用して発話の終わりを特定します。 音声アクティビティがとぎれると、サービスは完了した発話の最終結果をストリーミングで返します。 このサービスは、部分的な結果を返送することもできます。これには、進行している発話の中間での認識結果と翻訳結果が含まれています。

音声間の翻訳の場合、サービスにより、ターゲット言語で話されたテキストから音声を合成する (音声合成) 機能が提供されます。 音声合成オーディオは、クライアントが指定した形式で作成されます。 WAV 形式と MP3 形式を使用できます。

Translator Speech API では、WebSocket プロトコルを使用して、クライアントとサーバーの間に全二重通信チャネルを提供します。

## <a name="about-microsoft-translator"></a>Microsoft Translator について
Microsoft Translator は、クラウドベースの機械翻訳サービスです。 このサービスの中核にあるのは、さまざまな Microsoft 製品やサービスの機能を強化し、世界中の数千の企業によってコンテンツを世界中のユーザーに届けるためにアプリケーションとワークフローで使用されている [Translator Text API](https://www.microsoft.com/en-us/translator/translatorapi.aspx) と Translator Speech API です。

Microsoft Translator サービスの詳細については、[こちら](https://www.microsoft.com/en-us/translator/home.aspx)を参照してください

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator のニューラル機械翻訳 (NMT)
Translator Speech API は、翻訳を提供するために、従来の統計的機械翻訳 (SMT) と、新しいニューラル機械翻訳 (NMT) の両方を使用しています。

統計の機械翻訳は、パフォーマンス向上の観点から安定した状態に達しています。 翻訳品質は、SMT を採用した一般的なシステムでは、もはや大幅には改善されなくなっています。 新しい AI ベースの翻訳技術は、ニューラル ネットワーク (NN) を基盤として開発が活発化しています。

NMT では、生の翻訳品質のスコアリングの観点からだけでなく、SMT の場合よりも音声がより流ちょうで人間らしいため、より優れた翻訳が提供されます。
こうした流ちょうさが得られるのは、NMT では 1 つの文章のコンテキスト全体を使用して言葉を変換することが主な理由です。 SMT で考慮されるのは、各単語の前後にある数語の直接的コンテキストのみです。

NMT モデルは、API のコアにあって、エンドユーザーからは見えません。 それとわかる違いは以下の点のみです。
* 特に中国語、日本語、アラビア語などの言語に対する翻訳品質が向上している
* (Microsoft Translator Text API と共に使用する) 既存のハブ カスタマイズ機能と互換性がない

音声翻訳のサポート対象言語はすべて、NMT によって処理されます。 したがって、音声翻訳に渡されるすべての音声が NMT を使用します。

音声テキスト変換では、言語ペアによっては NMT と SMT の組み合わせが使用される場合があります。 ターゲット言語が NMT によってサポートされている場合、翻訳全体が NMT によって処理されます。 ターゲット言語が NMT によってサポートされていない場合は、2 つの言語間の "ピボット" として英語を使用する NMT と SMT のハイブリッド翻訳となります。

サポートされている言語については、[Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx) を参照してください。

NMT の動作方法の詳細については、[こちら](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)を参照してください

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サインアップ](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [コーディングを始める](quickstarts/csharp.md)

## <a name="see-also"></a>関連項目
- [Cognitive Services のドキュメント ページ](https://docs.microsoft.com/azure/)
- [Cognitive Services の製品ページ](https://azure.microsoft.com/services/cognitive-services/)
- [ソリューションと価格情報](https://www.microsoft.com/en-us/translator/home.aspx)
