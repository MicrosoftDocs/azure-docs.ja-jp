---
title: Speech SDK でメモリ使用量を追跡する方法 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Service SDK では、音声翻訳に加え、音声からテキストへの変換とテキストから音声への変換を行うためのさまざまなプログラミング言語がサポートされています。 この記事では、SDK に組み込まれているメモリ管理ツールについて説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934142"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Speech SDK でメモリ使用量を追跡する方法

Speech SDK は、一連の相互運用性レイヤーを通じて複数のプログラミング言語に射影されるネイティブ コード ベースに基づいています。 各言語固有の射影には、オブジェクトのライフサイクルを管理するための慣用的に適切な機能が含まれています。 さらに、Speech SDK には、オブジェクトのログ記録とオブジェクトの制限を使用してリソースの使用状況を追跡するためのメモリ管理ツールが含まれています。 

## <a name="how-to-read-object-logs"></a>オブジェクトのログを読み取る方法

[Speech SDK のログ記録が有効になっている](how-to-use-logging.md)場合は、オブジェクトの履歴を監視できるように、追跡タグが出力されます。 次のタグが含まれます。 

* `TrackHandle` または `StopTracking` 
* オブジェクトの型
* オブジェクトの型が追跡されるオブジェクトの現在の数と、現在追跡中の数。

サンプル ログを次に示します。 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>警告しきい値を設定する

警告しきい値を作成するオプションがあり、そのしきい値を超えた場合 (ログ記録が有効になっていることを前提として)、警告メッセージがログに記録されます。 警告メッセージには、存在するすべてのオブジェクトのダンプがカウントと共に含まれます。 この情報を使用して、問題の理解を深めることができます。 

警告しきい値を有効にするには、それを `SpeechConfig` オブジェクトに指定する必要があります。 このオブジェクトは、新しいレコグナイザーが作成されるときにチェックされます。 次の例では、`config` と呼ばれる `SpeechConfig` のインスタンスが作成されていることを前提としています。

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> このプロパティの既定値は 10,000 です。

## <a name="set-an-error-threshold"></a>エラーしきい値を設定する 

Speech SDK を使用して、特定の時点で許可されるオブジェクトの最大数を設定できます。 この設定が有効な場合、最大数に達したときに、新しいレコグナイザー オブジェクトを作成しようとすると失敗します。 既存のオブジェクトは引き続き機能します。

エラーの例を次に示します。

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

エラーしきい値を有効にするには、それを `SpeechConfig` オブジェクトに指定する必要があります。 このオブジェクトは、新しいレコグナイザーが作成されるときにチェックされます。 次の例では、`config` と呼ばれる `SpeechConfig` のインスタンスが作成されていることを前提としています。

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> このプロパティの既定値は、`size_t` データ型のプラットフォーム固有の最大値です。 一般的な認識では、7 から 10 個の内部オブジェクトが使用されます。

## <a name="next-steps"></a>次のステップ

* [Speech SDK についてさらに詳しく学習する](speech-sdk.md)