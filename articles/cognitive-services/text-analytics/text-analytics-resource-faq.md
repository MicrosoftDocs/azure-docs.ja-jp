---
title: Text Analytics API に関してよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API に関連する概念、コード、およびシナリオについてよく寄せられる質問に対する回答を示します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: aahi
ms.openlocfilehash: 9a4e179767cc38169cd794f4cd629604bdcdaab0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97955044"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Text Analytics API に関してよく寄せられる質問 (FAQ)

 Azure Cognitive Services の Text Analytics API に関連する概念、コード、およびシナリオについてよく寄せられる質問に対する回答を示します。

## <a name="can-text-analytics-identify-sarcasm"></a>テキスト分析では、皮肉を理解できますか。

分析は、気分を検出するためのものではなく、正/負の感情を検出するためのものです。

感情分析は常にある程度不正確になりますが、コンテンツに隠された、言外の意味がないとき、このモデルは最も役立ちます。 皮肉、当てこすり、ユーモア、同様のニュアンスのコンテンツは、意図を伝えるにあたり、文化的背景や規範に依存します。 この種類のコンテンツは、分析するのが最も困難なコンテンツです。 通常、アナライザーが与えた点数と人間の主観的な評価では、コンテンツに微妙な意味が含まれるときに最も違いが現れます。

## <a name="can-i-add-my-own-training-data-or-models"></a>自分のトレーニング データやモデルを追加できますか。

いいえ。モデルは事前トレーニングされています。 アップロードしたデータに対して行える操作は、スコア付け、キー フレーズ抽出、言語検出に限られます。 カスタム モデルはホストしていません。 カスタムの機械学習モデルを作成し、ホストする場合、[Microsoft R Server の機械学習機能](/r-server/r/concept-what-is-the-microsoftml-package)を検討してください。

## <a name="can-i-request-additional-languages"></a>追加言語を要望できますか。

感情分析とキー フレーズ抽出は、[一部の言語](./language-support.md)でのみご利用いただけます。 自然言語処理は複雑であり、新しい機能を公開する前に十分な試験が必要になります。 そのため、成熟により多くの時間を要する機能に誰も依存しないように予告サポートは避けています。 

次にどの言語に取り組むべきか、優先順位を付けるために、[User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics) で投票にご協力ください。 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>キー フレーズ抽出で特定の単語が返されるのはなぜですか。

キー フレーズ抽出では、重要でない単語や独立した形容詞が除かれます。 "spectacular views" (すばらしい眺め) や "foggy weather" (霧の多い天気) のような形容詞と名詞の組み合わせはまとめて返されます。

一般的に、出力は文章の名詞と目的語で構成されます。 出力は重要な順に一覧表示されます。最初のフレーズが最も重要となります。 重要度は、特定の概念が言及される回数かテキストにおけるその要素と他の要素の関係性で計測されます。

## <a name="why-does-output-vary-given-identical-inputs"></a>同じものを入力して出力が変わるのはなぜですか。

大きな変更が行われる場合、モデルとアルゴリズムの改良が告知されます。あるいは、重大な更新でなければ、告知されずにサービスに組み込まれます。 ある程度の時間が経過すると、同じテキストを入力したのに、感情スコアやキー フレーズ出力が異なることがあります。 クラウドで管理されている機械学習リソースを利用している結果であり、異常ではありません。

## <a name="service-availability-and-redundancy"></a>サービスの可用性と冗長性

### <a name="is-text-analytics-service-zone-resilient"></a>Text Analytics サービスにゾーン回復性はありますか。

はい。 Text Analytics サービスには、ゾーン回復性が既定で備わっています。

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Text Analytics サービスにゾーン回復性を構成するにはどうすればよいですか。

ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Text Analytics リソースのゾーン回復性は、既定で使用できるようになっており、サービス自体によって管理されます。

## <a name="next-steps"></a>次のステップ

不足している機能について質問がある場合は、 [UserVoice Web サイト](https://cognitive.uservoice.com/forums/555922-text-analytics)でご要望またはご投票ください。

## <a name="see-also"></a>関連項目

 * [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
