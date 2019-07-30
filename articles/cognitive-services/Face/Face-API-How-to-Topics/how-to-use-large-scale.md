---
title: 例:大規模機能を使用する方法 - Face API
titleSuffix: Azure Cognitive Services
description: Face API で大規模機能を使用します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449037"
---
# <a name="example-use-the-large-scale-feature"></a>例:大規模なフィーチャーを使用する

このガイドは、既存の PersonGroup オブジェクトと FaceList オブジェクトからそれぞれ LargePersonGroup オブジェクトと LargeFaceList オブジェクトにスケールアップする方法に関する高度な記事です。 このガイドではこの移行プロセスについて説明します。 ここでは、PersonGroup オブジェクトと FaceList オブジェクト、[トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)操作、および顔認識機能に関する基本的な知識があることを前提としています。 これらの内容の詳細については、[顔認識](../concepts/face-recognition.md)に関する概念ガイドを参照してください。

LargePersonGroup と LargeFaceList は、まとめて大規模操作と呼びます。 LargePersonGroup には、最大 100 万人、その 1 人あたり最大 248 個の顔を保持できます。 LargeFaceList には、最大 100 万人の顔を保持できます。 大規模操作は、従来の PersonGroup と FaceList に似ていますが、新しいアーキテクチャなのでいくつかの違いがあります。 

サンプルは Azure Cognitive Services Face API クライアント ライブラリを使用して C# で記述されています。

> [!NOTE]
> Identification と FindSimilar に対して大規模な顔検索パフォーマンスを有効にするには、トレーニング操作を導入して LargeFaceList と LargePersonGroup を事前に処理します。 トレーニング時間は実際の容量に応じて変わり、数秒から約 30 分かかります。 以前にトレーニング操作に成功している場合、トレーニング期間中に Identification と FindSimilar を実行できます。 欠点は、大規模なトレーニングへの新しい移行後処理が完了するまで、新しく追加された人物や顔が結果に表示されないことです。

## <a name="step-1-initialize-the-client-object"></a>手順 1:クライアント オブジェクトを初期化する

Face API クライアント ライブラリを使用する場合、サブスクリプション キーとサブスクリプション エンドポイントは、FaceClient クラスのコンストラクターを介して渡されます。 例:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

サブスクリプションキーを対応するエンドポイントで取得するには、Azure portal から Azure Marketplace にアクセスします。
詳細については、[サブスクリプション](https://azure.microsoft.com/services/cognitive-services/directory/vision/)に関する記事を参照してください。

## <a name="step-2-code-migration"></a>手順 2:コードの移行

このセクションでは、PersonGroup または FaceList の実装を LargePersonGroup または LargeFaceList に移行する方法に焦点を当てています。 LargePersonGroup または LargeFaceList は、設計と内部実装の点で PersonGroup または FaceList とは異なりますが、API インターフェイスは下位互換性のために似ています。

データ移行はサポートされていません。 代わりに LargePersonGroup または LargeFaceList を再作成します。

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>PersonGroup を LargePersonGroup に移行する

PersonGroup から LargePersonGroup への移行は簡単です。 これらはまったく同じグループ レベルの操作を共有します。

PersonGroup または Person 関連の実装では、API パスまたは SDK クラス/モジュールを LargePersonGroup と LargePersonGroup Person に変更するだけで済みます。

すべての顔と人を PersonGroup から新しい LargePersonGroup に追加します。 詳細については、「[顔を追加する方法](how-to-add-faces.md)」を参照してください。

### <a name="migrate-a-facelist-to-a-largefacelist"></a>FaceList を LargeFaceList に移行する

| FaceList の API | LargeFaceList の API |
|:---:|:---:|
| Create | Create |
| 削除 | 削除 |
| 取得 | 取得 |
| List | List |
| アップデート | アップデート |
| - | トレーニング |
| - | トレーニング状態の取得 |

上の表は、FaceList と LargeFaceList の間のリストレベル操作の比較です。 ご覧のように、FaceList と比較すると、LargeFaceList にはトレーニングとトレーニング状態の取得という新しい操作が追加されています。 LargeFaceList のトレーニングは、[FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 操作の前提条件です。 FaceList にはトレーニングは必要ありません。 次のスニペットは、LargeFaceList のトレーニングを待機するヘルパー関数です。

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

以前は、顔を追加した FaceList と FindSimilar の一般的な使用方法は、次のようなものでした。

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

LargeFaceList に移行すると、次のようになります。

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

前述のとおり、データ管理と FindSimilar 部分はほぼ同じです。 唯一の例外は、FindSimilar が動作する前に、LargeFaceList で新しい事前処理のトレーニング操作が完了する必要があることです。

## <a name="step-3-train-suggestions"></a>手順 3:候補をトレーニングする

トレーニング操作によって [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) と [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) は高速になりますが、特に大規模になるとトレーニング時間が長くなります。 さまざまな規模での推定トレーニング時間を次の表に示します。

| 規模 (顔または人の数) | 推定トレーニング時間 |
|:---:|:---:|
| 1,000 | 1 ～ 2 秒 |
| 10,000 | 5 ～ 10 秒 |
| 100,000 | 1 ～ 2 分 |
| 1,000,000 | 10 ～ 30 分 |

大規模機能をより有効に活用するには、以下の戦略をお勧めします。

### <a name="step-31-customize-time-interval"></a>手順 3.1: 時間間隔をカスタマイズする

`TrainLargeFaceList()` でわかるように、無限のトレーニング状態チェック プロセスが遅延する時間間隔 (ミリ秒) があります。 顔が多い LargeFaceList の場合、間隔を広げると、呼び出し回数とコストが減ります。 時間間隔は、LargeFaceList の予想容量に従ってカスタマイズします。

同じ戦略が LargePersonGroup にも適用されます。 たとえば、100 万人の人物が含まれる、LargePersonGroup をトレーニングする場合、`timeIntervalInMilliseconds` は 60,000 (1 分間隔) になります。

### <a name="step-32-small-scale-buffer"></a>手順 3.2: 小規模なバッファー

LargePersonGroup または LargeFaceList の Persons または Faces は、トレーニングが完了した後にのみ検索できます。 動的なシナリオでは、新しい人または顔が絶えず追加され、すぐに検索できるようにする必要がありますが、望ましい時間よりもトレーニング時間が長くなる可能性があります。 

この問題を軽減するには、新しく追加されたエントリに対してのみ、追加の小規模な LargePersonGroup または LargeFaceList をバッファーとして利用します。 このバッファーはサイズが小さいため、トレーニングにかかる時間が短くなります。 この一時的なバッファーに対する即時検索機能が機能するはずです。 より長い間隔でマスター トレーニングを実行することで、このバッファーとマスター LargePersonGroup または LargeFaceList に対するトレーニングを組み合わせて使用します。 たとえば、毎日深夜に実行します。

ワークフローの例:

1. マスター LargePersonGroup または LargeFaceList (マスター コレクション) を作成します。 バッファー LargePersonGroup または LargeFaceList (バッファー コレクション) を作成します。 バッファー コレクションは、新しく追加された人または顔の専用です。
1. マスター コレクションとバッファー コレクションの両方に新しい人または顔を追加します。
1. 新しく追加されたエントリが有効になるように、短時間の間隔でバッファー コレクションのみをトレーニングします。
1. マスター コレクションとバッファー コレクションの両方に対して Identification または FindSimilar を呼び出します。 結果をマージします。
1. バッファー コレクションのサイズが増えてしきい値に達するか、システム アイドル時間になったら、新しいバッファー コレクションを作成します。 マスター コレクションに対するトレーニングをトリガーします。
1. マスター コレクションに対するトレーニングの完了後に古いバッファー コレクションを削除します。

### <a name="step-33-standalone-training"></a>手順 3.3: スタンドアロン トレーニング

比較的長い待機時間を許容できる場合は、新しいデータを追加した直後にトレーニング操作をトリガーする必要はありません。 代わりに、トレイン操作をメイン ロジックから分割して、定期的にトリガーすることができます。 この戦略は、許容できる待機時間がある動的なシナリオに適しています。 これは、トレーニングの頻度をさらに減らすために、静的なシナリオに適用することができます。

`TrainLargeFaceList` と似た `TrainLargePersonGroup` 関数があるとします。 `System.Timers` の [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) クラスを呼び出して LargePersonGroup に対してスタンドアロン トレーニングを実行する一般的な実装を次に示します。

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

データ管理と識別に関連する実装の詳細については、「[顔を追加する方法](how-to-add-faces.md)」と「[画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)」を参照してください。

## <a name="summary"></a>まとめ

このガイドでは、データではなく、既存の PersonGroup または FaceList コードを、LargePersonGroup または LargeFaceList に移行する方法を学びました。

- LargePaceGroup と LargeFaceList は、LargeFaceList によるトレーニング操作が必要であることを除けば、PersonGroup または FaceList と同様に機能します。
- 大規模なデータセットの動的なデータ更新には、適切なトレーニング戦略を実行してください。

## <a name="next-steps"></a>次の手順

ハウツー ガイドに従って、PersonGroup に顔を追加する方法、または PersonGroup で識別操作を実行する方法を学習します。

- [顔を追加する](how-to-add-faces.md)
- [画像内の顔を識別する](HowtoIdentifyFacesinImage.md)