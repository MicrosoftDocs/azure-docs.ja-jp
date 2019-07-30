---
title: フレーズ リスト - Speech Services
titlesuffix: Azure Cognitive Services
description: 音声テキスト変換の認識結果を向上させるために、`PhraseListGrammar` オブジェクトを使用して Speech Services にフレーズ リストを提供する方法について説明します。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 158819d481c0ce245539d783f194b159b2b9edb6
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606517"
---
# <a name="phrase-lists-for-speech-to-text"></a>音声テキスト変換用のフレーズ リスト

Speech Services にフレーズ リストを提供することにより、音声認識の精度を向上させることができます。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。

例として、"Move to" という指示があり、会話で考えられる目的地として "Ward" がある場合、"Move to Ward" というエントリーを追加できます。 フレーズを追加すると、音声認識の実行時に、"Move toward" ではなく "Move to Ward" が認識される可能性が高くなります。

フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に音声に完全一致が含まれる場合、フレーズ リスト内のエントリーが使用されます。 前述の例の場合、フレーズ リストに "Move to Ward" が含まれており、"Move toward slowly" というフレーズがキャプチャされた場合、認識結果は "Move to Ward slowly" となります。

## <a name="how-to-use-phrase-lists"></a>フレーズ リストの使用方法

次の例では、`PhraseListGrammar` オブジェクトを使用してフレーズ リストを作成する方法を示します。

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Speech Service で音声の照合に使用されるフレーズ リストの最大数は 1024 フレーズです。

clear() を呼び出すことで、`PhraseListGrammar` に関連付けられたフレーズを消去することもできます。

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar` オブジェクトへの変更は、次の認識時、または Speech Services への再接続後に有効になります。

## <a name="next-steps"></a>次の手順

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
