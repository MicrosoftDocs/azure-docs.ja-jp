---
title: Speech Services モデルのトレーニングのための文字起こしガイドライン
titleSuffix: Azure Cognitive Services
description: 音響モデルと言語モデルおよび音声フォントを Speech Services 用にカスタマイズするためにテキストを準備する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 0d7508ed9cf1807fa05c57a1d60c804af7d2244f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897216"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Speech Service を使用するための文字起こしガイドライン

**音声テキスト変換**または**テキスト読み上げ**をカスタマイズするには、音声と共にテキストを提供する必要があります。 テキストの各行は 1 つの発話に対応します。 テキストはできるだけ音声と一致する必要があります。 このテキストは "*トランスクリプト*" と呼ばれ、特定の形式で作成する必要があります。

Speech Services では、テキストの整合性を保つために入力が正規化されます。

この記事では、この両方の種類の正規化について説明します。 このガイドラインは言語によって若干異なります。

## <a name="us-english-en-us"></a>英語 (米国) (en-us)

テキスト データは、ASCII 文字セットのみを使用して、プレーンテキストで行当たり 1 つの発話で作成します。

拡張文字 (Latin-1) や Unicode 句読点文字の使用は避けます。 これらの文字は、ワード プロセッシング プログラムでデータを準備するときや、Web ページからデータを収集するときに誤って含まれる可能性があります。 文字を適切な ASCII 文字に置き換えてください。 例: 

| 使用を避ける文字 | 代替 |
|----- | ----- |
| “Hello world” (最初と最後に二重引用符) | "Hello world" (二重引用符) |
| John’s day (右一重引用符) | John's day (アポストロフィ) |
| it was good—no, it was great! (em ダッシュ) | it was good--no, it was great! (ハイフン) |

### <a name="text-normalization-rules-for-english"></a>英語のテキスト正規化規則

Speech Services では、次の正規化規則を実行します。

* すべてのテキストで小文字を使用する
* 単語内のアポストロフィを除くすべての句読点を削除する
* 金額を含め、数字を読み上げ形式に拡張する

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | i’m double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

テキスト トランスクリプトに次の正規化を適用します。

* 省略形は単語で書き出す。
* 非標準の数値文字列 (一部の日付形式や会計形式など) は単語で書き出す。
* アルファベット以外の文字を含む語句または英数字が混在する語句は、発音どおりに文字に起こす。
* 単語として発音される省略形はそのまま残す ("radar"、"laser"、"RAM"、"NATO" など)。
* 個々の文字として発音される省略形は、スペースで区切られた文字で記述する ("IBM"、"CPU"、"FBI"、"TBD"、"NaN")。

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr.Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>中国語 (zh-cn)

Custom Speech Services にアップロードするテキスト データでは、バイト順マーカーを含む UTF-8 エンコードを使用します。 ファイルは、行当たり 1 つの発話で作成します。

半角句読点文字の使用は避けます。 これらの文字は、ワード プロセッシング プログラムでデータを準備するときや、Web ページからデータを収集するときに誤って含まれる可能性があります。 それらを適切な全角文字に置き換えます。 例: 

| 使用を避ける文字 | 代替 |
|----- | ----- |
| "你好" (最初と最後に二重引用符) | "你好" (二重引用符) |
| 需要什么帮助? (疑問符) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>中国語のテキスト正規化規則

Speech Services では、次の正規化規則を実行します。

* すべての句読点を削除する
* 数字を読み上げ形式に拡張する
* 全角文字を半角文字に変換する
* すべての英単語に大文字を使用する

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

テキストをインポートする前に、次の正規化を適用します。

* 省略形は単語で書き出す (読み上げ形式と同じ)。
* 数値文字列は読み上げ形式で書き出す

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>その他の言語

**音声テキスト変換**サービスにアップロードするテキスト データでは、バイト順マーカーを含む UTF-8 エンコードを使用する必要があります。 ファイルは、行当たり 1 つの発話で作成します。

> [!NOTE]
> 次の例では、ドイツ語を使用しています。 ただし、このガイドラインは、英語 (米国) または中国語以外のすべての言語に適用されます。

### <a name="text-normalization-rules-for-german"></a>ドイツ語のテキスト正規化規則

Speech Services では、次の正規化規則を実行します。

* すべてのテキストで小文字を使用する
* 各種引用符を含むすべての句読点を削除する ("test"、'test'、"test„、«test» は許可される)
* ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬ のいずれかの特殊文字を含む行を破棄する
* 金額 (ドルまたはユーロ) を含め、数字を単語形式に拡張する
* ウムトラウトは、a、o、u の場合にのみ受け入れられ、それ以外は "th" に置き換えられるか、破棄される

次に例をいくつか示します。

| 元のテキスト | 正規化後 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

テキストをインポートする前に、次の正規化を適用します。

* 小数点は、"." ではなく "," にします。
* 時間と分の間の区切り記号は、"." ではなく ":" にします (例: 12:00 Uhr)。
* "ca." などの省略形は 置き換えられません。 完全形を使用することをお勧めします。
* 4 つの主要な算術演算子 (+、-、\*、/) は削除されます。 これらをリテラル形式 ("plus"、"minus"、"mal"、"geteilt") に置き換えることをお勧めします。
* 同じ規則が比較演算子 (=、<、>) に適用されます。 比較演算子は "gleich"、"kleiner als"、"grösser als" で置換することをお勧めします。
* 3/4 などの分数は単語形式で使用します (例: ¾ ではなく、"drei viertel" を使用)。
* € 記号は "ユーロ" という単語形式に置き換えます。

次に例をいくつか示します。

| 元のテキスト | ユーザーによる正規化後 | システムによる正規化後 |
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>次の手順

- [Speech Services 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
