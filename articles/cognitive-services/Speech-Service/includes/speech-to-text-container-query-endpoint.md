---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321043"
---
コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Speech SDK](../index.yml) を介してアクセスします。 既定では、Speech SDK は、オンラインの音声サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。

> [!TIP]
> Speech SDK とコンテナーを使用する場合、Azure Speech リソースの[サブスクリプション キーまたは認証ベアラー トークン](../rest-speech-to-text.md#authentication)を提供する必要はありません。

次の例を参照してください。

# <a name="c"></a>[C#](#tab/csharp)

この Azure クラウド初期化呼び出しを使用する方法を

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

次のようにコンテナー [ホスト](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)を使用する呼び出しに変更します。

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

この Azure クラウド初期化呼び出しを使用する方法を

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

---
