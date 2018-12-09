---
title: Azure 上の Custom Speech Service における文字起こしのガイドライン | Microsoft Docs
description: Cognitive Services の Custom Speech Service 向けにデータを準備する方法について説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 6c64b89c2eb933e9bcc24bdc3097e7a0b2aab368
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342468"
---
# <a name="transcription-guidelines"></a>トランスクリプションのガイドライン

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

音響モデルと言語モデルのカスタマイズでテキスト データを最適に使用できるようにするために、次に示す文字起こしのガイドラインに従う必要があります。 これらのガイドラインは言語に固有です。

## <a name="text-normalization"></a>テキスト正規化

音響モデルまたは言語モデルのカスタマイズでテキスト データを最適に使用するためには、テキスト データが正規化されている必要があります。これは、システムが読み取ることのできる、標準の、あいまいでない形式に変換されていることを意味します。 このセクションでは、データがインポートされたときに Custom Speech Service によって実行されるテキスト正規化と、ユーザーがデータのインポート前に実行する必要があるテキスト正規化について説明します。

## <a name="inverse-text-normalization"></a>逆テキスト正規化

フォーマットされていない "生の" テキストを元のフォーマットされたテキスト (大文字/小文字の設定や句読点のあるテキスト) に変換するプロセスは、逆テキスト正規化 (ITN) と呼ばれます。 ITN は、Microsoft Cognitive Services Speech API から返された結果に対して実行されます。 Custom Speech Service を使用してデプロイされたカスタム エンドポイントでは、Microsoft Cognitive Services Speech API と同じ ITN が使用されます。 しかし、このサービスでは現在カスタム ITN がサポートされていないため、カスタム言語モデルによって導入された新しい用語は認識結果内でフォーマットされません。

## <a name="transcription-guidelines-for-en-us"></a>en-US 用の文字起こしのガイドライン

このサービスにアップロードされるテキスト データは、印刷可能な ASCII 文字セットのみを使用してプレーンテキストで作成されている必要があります。 ファイルの各行には、1 つの発話のみを表すテキストが含まれている必要があります。

Unicode 句読点文字の使用を避けることが重要です。 ワード プロセッシング プログラムでデータを準備するときや、Web ページからデータをスクラップするときに誤って使用してしまうことがあります。 これらの文字を適切な ASCII 文字に置き換えてください。 例: 

| 避けるべき Unicode | 置き換える ASCII |
|----- | ----- |
| "Hello world" (開始二重引用符と終了二重引用符) | "Hello world" (二重引用符) |
| John’s day (右一重引用符) | John's day (アポストロフィ) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service によって実行されるテキスト正規化

このサービスは、言語データ セット、または音響データ セットの文字起こしとしてインポートされたデータに対して、次のテキスト正規化を実行します。 次のバックアップがあります

*   すべてのテキストを小文字にする
*   単語内のアポストロフィを除くすべての句読点を削除する
*   金額を含め、数字を読み上げ形式に拡張する

次に例を示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | i’m double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>ユーザーが実行する必要があるテキスト正規化

データを最適に使用できるようにするために、データをインポートする前に、次の正規化規則をデータに適用する必要があります。

*   省略形は単語で書き出して読み上げ形式を反映させる
*   非標準の数値文字列は単語で書き出す
*   アルファベット以外の文字を含む語句または英数字が混在する語句は、発音どおりに文字に起こす
*   一般的な頭字語は、文字間にピリオドまたはスペースを入れずにそのまま 1 つのエンティティとすることができる。しかし、それ以外の頭字語はすべて個別の文字で書き出し、各文字間をスペース 1 つで区切る

次に例を示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr.Strangelove | Doctor Strangelove |
| James Bond 007 | james bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>zh-CN 用の文字起こしのガイドライン

Custom Speech Service にアップロードするテキスト データでは、**UTF-8 エンコード (BOM を含む)** を使用します。 ファイルの各行には、1 つの発話のみを表すテキストが含まれている必要があります。

半角句読点文字の使用は避けることが重要です。 ワード プロセッシング プログラムでデータを準備するときや、Web ページからデータをスクラップするときに誤って使用してしまうことがあります。 これらの文字を適切な全角文字に置き換えてください。 例: 

| 避けるべき Unicode | 置き換える ASCII |
|----- | ----- |
| "你好" (開始二重引用符と終了二重引用符) | "你好" (二重引用符) |
| 需要什么帮助? (疑問符) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service によって実行されるテキスト正規化

この音声サービスは、言語データ セット、または音響データ セットの文字起こしとしてインポートされたデータに対して、次のテキスト正規化を実行します。 次のバックアップがあります

*   すべての句読点を削除する
*   数字を読み上げ形式に拡張する
*   全角文字を半角文字に変換する
*   すべての英単語を大文字にする

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>ユーザーが実行する必要があるテキスト正規化

データを最適に使用できるようにするために、データをインポートする _前_ に、次の正規化規則をデータに適用する必要があります。

*   省略形は単語で書き出して読み上げ形式を反映させる
*   このサービスはすべての数量に対応しているわけではありません。 数値文字列を読み上げ形式で書き出すと、信頼性が向上します。

次に例を示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>de-DE 用の文字起こしのガイドライン

Custom Speech Service にアップロードするテキスト データでは、**UTF-8 エンコード (BOM を含む)** のみを使用します。 ファイルの各行には、1 つの発話のみを表すテキストが含まれている必要があります。

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service によって実行されるテキスト正規化

このサービスは、言語データ セット、または音響データ セットの文字起こしとしてインポートされたデータに対して、次のテキスト正規化を実行します。 次のバックアップがあります

*   すべてのテキストを小文字にする
*   英語またはドイツ語の引用符を含むすべての句読点を削除する ("test"、'test'、"test„、または «test» は許可される)
*   特殊文字 (^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬) を含むすべての行を破棄する
*   金額 (ドルまたはユーロ) を含め、数字を単語形式に拡張する
*   ウムトラウトは、a、o、u の場合にのみ受け入れられ、それ以外は "th" に置き換えられるか、破棄される

次に例を示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |


### <a name="text-normalization-required-by-users"></a>ユーザーが実行する必要があるテキスト正規化

データを最適に使用できるようにするために、データをインポートする前に、次の正規化規則をデータに適用する必要があります。

*   小数点は、"." ではなく "," にします  (例: 2.3% ではなく 2,3%)。
*   時間と分の間の区切り記号は、"." ではなく ":" にします (例: 12:00 Uhr)。
*   "ca."、"bzw" などの省略形は 置換されません。 正確に発音されるように、完全な形式を使用することをお勧めします。
*   5 つの主要な算術演算子 (+、-、\*、/) は削除されます。
 これらをリテラル形式 (plus、minus、mal、geteilt) で置き換えることをお勧めします。
*   比較演算子にも同じことが適用されます。=、<、> は、gleich、kleiner als、grösserals にそれぞれ置き換えます。
*   3/4 などの分数は単語形式で使用します (例: ¾ ではなく、"drei viertel" を使用)。
*   € 記号は "ユーロ" という単語形式に置き換えます。


次に例を示します。

| 元のテキスト | ユーザーによる正規化後 | システムによる正規化後
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>次の手順
* [カスタム Speech-to-Text エンドポイントの使用方法](cognitive-services-custom-speech-create-endpoint.md)
* [カスタム音響モデル](cognitive-services-custom-speech-create-acoustic-model.md)での精度の向上
* [カスタム言語モデル](cognitive-services-custom-speech-create-language-model.md)での精度の向上
