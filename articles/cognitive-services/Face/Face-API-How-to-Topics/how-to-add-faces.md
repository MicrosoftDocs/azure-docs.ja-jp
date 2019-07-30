---
title: 例:顔を PersonGroup に追加する方法 - Face API
titleSuffix: Azure Cognitive Services
description: Face API を使用して画像に顔を追加します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0415dcae08c188c1758150c4b8b0df4dee014ce6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448597"
---
# <a name="add-faces-to-a-persongroup"></a>顔を PersonGroup に追加する

このガイドでは、PersonGroup オブジェクトに膨大な数の人と顔を追加する方法を説明します。 この同じ戦略は、LargePersonGroup、FaceList、および LargeFaceList の各オブジェクトにも適用されます。 このサンプルは Azure Cognitive Services Face API .NET クライアント ライブラリを使用して C# で記述されています。

## <a name="step-1-initialization"></a>手順 1:初期化する

以下のコードは、いくつかの変数を宣言し、顔の追加要求をスケジュール設定するヘルパー関数を実装します。

- `PersonCount` は、人の合計です。
- `CallLimitPerSecond` は、サブスクリプション レベルに基づいた 1 秒あたりの最大呼び出し回数です。
- `_timeStampQueue` は、要求のタイムスタンプを記録するキューです。
- `await WaitCallLimitPerSecondAsync()` は、次の要求の送信が有効になるまで待機します。

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>手順 2:API 呼び出しを承認する

クライアント ライブラリを使用するときは、サブスクリプション キーを **FaceClient** クラスのコンストラクターに渡す必要があります。 例:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

サブスクリプションキー を取得するには、Azure portal から Azure Marketplace にアクセスします。 詳細については、[サブスクリプション](https://www.microsoft.com/cognitive-services/sign-up)に関する記事を参照してください。

## <a name="step-3-create-the-persongroup"></a>手順 3:PersonGroup を作成する

人を保存するために "MyPersonGroup" という名前の PersonGroup を作成します。
要求時間が `_timeStampQueue` にエンキューされ、全体的な検証が保証されます。

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>手順 4:PersonGroup に対して人を作成する

複数の人を同時に作成します。また、`await WaitCallLimitPerSecondAsync()` を適用して、呼び出し制限を超えないようにします。

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>手順 5:顔を人に追加する

さまざまな人に追加された顔は、同時に処理されます。 特定の 1 人について追加された顔は、順次処理されます。
ここでも、`await WaitCallLimitPerSecondAsync()` を呼び出して、要求の頻度が制限範囲内になるようにします。

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>まとめ

このガイドでは、膨大な数の人と顔を含む PersonGroup を作成するプロセスについて学びました。 次のことを覚えておいてください。

- この方法は FaceList と LargePersonGroup にも適用されます。
- LargePersonGroup 内のさまざまな FaceList または Person への顔の追加や削除は、同時に処理されます。
- LargePersonGroup 内の特定の 1 つの FaceList または Person への顔の追加や削除は、順次処理されます。
- わかりやすくするため、潜在的な例外を処理する方法はこのガイドでは説明していません。 堅牢性をさらに拡張するには、適切な再試行ポリシーを適用します。

以下の機能について説明し、例を示しました。

- [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用して PersonGroup を作成します。
- [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用してユーザーを作成します。
- [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API を使用して、人に顔を追加します。

## <a name="related-topics"></a>関連トピック

- [画像内の顔を識別する](HowtoIdentifyFacesinImage.md)
- [画像内の顔を検出する](HowtoDetectFacesinImage.md)
- [大規模なフィーチャーを使用する](how-to-use-large-scale.md)
