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
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 2d96a04b1287033999dd5f026dd7d8d017259eb4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859048"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>例:大規模機能を使用する方法

このガイドは、既存の PersonGroup と FaceList からそれぞれ LargePersonGroup と LargeFaceList にスケールアップするコードの移行に関する高度な記事です。
このガイドでは、PersonGroup と FaceList の基本的な使用方法を理解していることを前提として移行プロセスについて説明します。
基本的な操作を理解するには、「[画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)」など、他のチュートリアルを参照してください。

Face API は、大規模シナリオ (LargePersonGroup と LargeFaceList) を有効にするために、最近 2 つの機能をリリースしました。これらはまとめて大規模操作と呼ばれます。
LargePersonGroup には、最大 1,000,000 人、その 1 人あたり最大 248 個の顔を保持できます。LargeFaceList には、最大 1,000,000 人の顔を保持できます。

大規模操作は、従来の PersonGroup と FaceList に似ていますが、新しいアーキテクチャなのでいくつかの重要な違いがあります。
このガイドでは、PersonGroup と FaceList の基本的な使用方法を理解していることを前提として移行プロセスについて説明します。
サンプルは Face API クライアント ライブラリを使用して C# で記述されています。

Identification と FindSimilar に対して大規模な顔検索パフォーマンスを有効にするには、トレーニング操作を導入して LargeFaceList と LargePersonGroup を事前に処理する必要があります。
トレーニング時間は実際の容量によって変わり、数秒から約 30 分かかります。
以前にトレーニングに成功している場合、トレーニング期間中でも Identification と FindSimilar を実行できます。
ただし、欠点は、大規模なトレーニングへの新しい移行後処理が完了するまで、新しく追加された人物/顔が結果に表示されないことです。

## <a name="concepts"></a>概念

このガイドの次の概念についてよくわからない場合は、[用語集](../Glossary.md)で定義を参照してください。

- LargePersonGroup: 上限が 1,000,000 の Persons のコレクション。
- LargeFaceList: 上限が 1,000,000 の Faces のコレクション。
- トレーニング:Identification/FindSimilar のパフォーマンスを確保するための前処理。
- 識別: PersonGroup または LargePersonGroup から 1 つまたは複数の顔を識別することです。
- FindSimilar: FaceList または LargeFaceList から似た顔を検索します。

## <a name="step-1-authorize-the-api-call"></a>手順 1:API 呼び出しを承認する

Face API クライアント ライブラリを使用する場合、サブスクリプション キーとサブスクリプション エンドポイントは、FaceServiceClient クラスのコンストラクターを介して渡されます。 例: 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

対応するエンドポイントがあるサブスクリプション キーは、Azure portal の [Marketplace] ページから取得できます。
[サブスクリプション](https://azure.microsoft.com/services/cognitive-services/directory/vision/)に関するページを参照してください。

## <a name="step-2-code-migration-in-action"></a>手順 2:コード移行を実施する

このセクションでは、PersonGroup/FaceList の実装を LargePersonGroup/LargeFaceList に移行することのみに焦点を当てています。
LargePersonGroup/LargeFaceList は PersonGroup/FaceList と設計と内部実装の点で異なりますが、API インターフェイスは下位互換性のために似ています。

データの移行はサポートされていないので、代わりに LargePersonGroup/LargeFaceList を再作成する必要があります。

## <a name="step-21-migrate-persongroup-to-largepersongroup"></a>手順 2.1:PersonGroup を LargePersonGroup に移行する

PersonGroup から LargePersonGroup への移行は、まったく同じグループレベルの操作を共有するため、スムーズです。

PersonGroup/Person 関連の実装では、API パスまたは SDK クラス/モジュールを LargePersonGroup と LargePersonGroup Person に変更するだけで済みます。

データ移行に関しては、「[How to Add Faces](how-to-add-faces.md)」(顔を追加する方法) を参照してください。

## <a name="step-22-migrate-facelist-to-largefacelist"></a>手順 2.2:FaceList を LargeFaceList に移行する

| FaceList の API | LargeFaceList の API |
|:---:|:---:|
| Create | Create |
| 削除 | 削除 |
| 取得 | 取得 |
| List | List |
| アップデート | アップデート |
| - | トレーニング |
| - | トレーニング状態の取得 |

上の表は、FaceList と LargeFaceList の間のリストレベル操作の比較です。
ご覧のように、FaceList と比較すると、LargeFaceList にはトレーニングとトレーニング状態の取得という新しい操作が追加されています。
LargeFaceList をトレーニングすることは、[FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 操作の前提条件ですが、FaceList に必要なトレーニングはありません。
次のスニペットは、LargeFaceList のトレーニングを待機するヘルパー関数です。

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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

顔を追加した FaceList と FindSimilar の以前の一般的な使用方法を次に示します。

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

LargeFaceList に移行すると、次のようになります。

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

上に示したように、データ管理と FindSimilar 部分はほぼ同じです。
唯一の例外は、FindSimilar が動作する前に、LargeFaceList で新しい事前処理のトレーニング操作が完了する必要があることです。

## <a name="step-3-train-suggestions"></a>手順 3:候補をトレーニングする

トレーニング操作によって [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) と [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) は高速になりますが、特に大規模になるとトレーニング時間が長くなります。
さまざまな規模での推定トレーニング時間を次の表に示します。

| 規模 (顔または人の数) | 推定トレーニング時間 |
|:---:|:---:|
| 1,000 | 1 から 2 秒 |
| 10,000 | 5 から 10 秒 |
| 100,000 | 1 から 2 分 |
| 1,000,000 | 10 から 30 分 |

大規模機能をより有効に活用するには、いくつかの戦略を考慮することをお勧めします。

## <a name="step-31-customize-time-interval"></a>手順 3.1: 時間間隔をカスタマイズする

`TrainLargeFaceList()` でわかるように、無限のトレーニング状態チェック プロセスが遅延する `timeIntervalInMilliseconds` があります。
顔が多い LargeFaceList の場合、間隔を広げると、呼び出し回数とコストが減ります。
時間間隔は、LargeFaceList の予想容量に従ってカスタマイズすることをお勧めします。

同じ戦略が LargePersonGroup にも適用されます。
たとえば、1,000,000 人の LargePersonGroup をトレーニングする場合、`timeIntervalInMilliseconds` は 60,000 (つまり 1 分間隔) になる可能性があります。

## <a name="step-32-small-scale-buffer"></a>手順 3.2 小規模なバッファー

LargePersonGroup/LargeFaceList の Persons/Faces は、トレーニングが完了した後にのみ検索できます。
動的なシナリオでは、新しい人/顔が絶えず追加され、すぐに検索できるようにする必要がありますが、望ましい時間よりもトレーニング時間が長くなる可能性があります。
この問題を軽減するには、新しく追加されたエントリに対してのみ、追加の小規模な LargePersonGroup/LargeFaceList をバッファーとして利用します。
このバッファーはサイズがはるかに小さいので、トレーニングにかかる時間が短く、この一時的なバッファーに対する即時検索が機能するはずです。
より長い間隔で (たとえば毎日深夜に) マスター トレーニングを実行して、このバッファーとマスター LargePersonGroup/LargeFaceList に対するトレーニングを組み合わせて使用します。

ワークフローの例:
1. マスター LargePersonGroup/LargeFaceList (マスター コレクション) とバッファー LargePersonGroup/LargeFaceList (バッファー コレクション) を作成します。 バッファー コレクションは、新しく追加された Persons/Faces の専用です。
1. マスター コレクションとバッファー コレクションの両方に新しい Persons/Faces を追加します。
1. 新しく追加されたエントリが有効になるように、短時間の間隔でバッファー コレクションのみをトレーニングします。
1. マスター コレクションとバッファー コレクションの両方に対して Identification/FindSimilar を呼び出し、結果をマージします。
1. バッファー コレクションのサイズが増えてしきい値に達するか、システム アイドル時間になったら、新しいバッファー コレクションを作成し、マスター収集に対するトレーニングをトリガーします。
1. マスター コレクションに対するトレーニングの完了後に古いバッファー コレクションを削除します。

## <a name="step-33-standalone-training"></a>手順 3.3 スタンドアロン トレーニング

比較的長い待機時間を許容できる場合は、新しいデータを追加した直後にトレーニング操作をトリガーする必要はありません。
代わりに、トレイン操作をメイン ロジックから分割して、定期的にトリガーすることができます。
この戦略は、許容できる待機時間がある動的なシナリオに適しています。また、静的シナリオに適用してトレーニング頻度をさらに減らすことができます。

`TrainLargeFaceList` と似た `TrainLargePersonGroup` 関数があるとします。
[`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) クラス 
(`System.Timers` 内) を呼び出すことで LargePersonGroup に対してスタンドアロン トレーニングを実行する一般的な実装を次に示します。

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

データ管理と識別に関連する実装の詳細については、「[How to Add Faces](how-to-add-faces.md)」(顔を追加する方法) と「[画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)」を参照してください。

## <a name="summary"></a>まとめ

このガイドでは、(データではなく) 既存の PersonGroup/FaceList コードを LargePersonGroup/LargeFaceList に移行する方法を学びました。

- LargePaceGroup と LargeFaceList は、LargeFaceList によるトレーニング操作が必要であることを除けば、PersonGroup/FaceList と同様に機能します。
- 大規模なデータセットの動的なデータ更新には、適切なトレーニング戦略を実行してください。

## <a name="related-topics"></a>関連トピック

- [画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)
- [顔を追加する方法](how-to-add-faces.md)
