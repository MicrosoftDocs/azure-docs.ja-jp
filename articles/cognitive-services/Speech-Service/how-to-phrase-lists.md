---
title: フレーズ リスト - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換の認識結果を向上させるために、`PhraseListGrammar` オブジェクトを使用して音声サービスにフレーズ リストを提供する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: a71b0ba459d9363f6f479fd88ef10c8209eaf47c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918725"
---
# <a name="phrase-lists-for-speech-to-text"></a>音声テキスト変換用のフレーズ リスト

音声サービスにフレーズのリストを提供することにより、音声認識の精度を向上させることができます。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。

例として、"Move to" という指示があり、会話で考えられる目的地として "Ward" がある場合、"Move to Ward" というエントリーを追加できます。 フレーズを追加すると、音声認識の実行時に、"Move toward" ではなく "Move to Ward" が認識される可能性が高くなります。

フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に、音声にフレーズ全体に対する完全一致が個別のフレーズとして含まれる場合、フレーズ リスト内のエントリが使用されます。 フレーズに対する完全一致が見つからない場合、認識はサポートされません。

>[!Note]
> 現時点では、フレーズ リストの音声テキスト変換では英語のみサポートされています。

## <a name="how-to-use-phrase-lists"></a>フレーズ リストの使用方法

次の例では、`PhraseListGrammar` オブジェクトを使用してフレーズ リストを作成する方法を示します。

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 音声サービスで音声の照合に使用されるフレーズ リストの最大数は 1024 フレーズです。

clear() を呼び出すことで、`PhraseListGrammar` に関連付けられたフレーズを消去することもできます。

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> `PhraseListGrammar` オブジェクトへの変更は、次の認識時、または音声サービスへの再接続後に有効になります。

## <a name="next-steps"></a>次のステップ

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
