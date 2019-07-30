---
title: 例:画像内の顔を識別する - Face API
titleSuffix: Azure Cognitive Services
description: Face API を使用して画像内の顔を識別します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 5806c17b0532f4d18b7ac57fbf70c92ed9d47daa
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827494"
---
# <a name="example-identify-faces-in-images"></a>例:画像内の顔を識別する

このガイドでは、既知のユーザーから事前に作成される PersonGroups オブジェクトを使用して不明な顔を識別する方法を示します。 サンプルは Azure Cognitive Services Face API クライアント ライブラリを使用して C# で記述されています。

## <a name="preparation"></a>準備

このサンプルは次の方法を示します。

- PersonGroup を作成する方法。 この PersonGroup には既知のユーザーの一覧が含まれています。
- 各ユーザーに顔を割り当てる方法。 これらの顔はユーザーを識別するための基準として使用されます。 正面からはっきり写った顔を使用することをお勧めします。 例は写真 ID です。 写真の適切なセットには、ポーズ、服の色、またはヘアー スタイルが異なる同一人物の顔が含まれます。

このサンプルのデモを実行するには、次のものを準備します。

- 個人の顔を含むいくつかの写真。 Anna、Bill、Clare の[サンプルの写真をダウンロードします](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)。
- 一連のテスト写真。 写真には、Anna、Bill、または Clare の顔が含まれている場合と含まれていない場合があります。 それらは識別をテストするために使用されます。 上記のリンクからいくつかのサンプル画像も選択します。

## <a name="step-1-authorize-the-api-call"></a>手順 1:API 呼び出しを承認する

Face API を呼び出すたびに、サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターを通じて渡すか、または要求ヘッダーで指定できます。 クエリ文字列を介してサブスクリプション キーを渡すには、例として [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) の要求 URL を参照してください。
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

その代わりに、HTTP 要求ヘッダー内でサブスクリプション キーを指定します (**ocp-apim-subscription-key: &lt;サブスクリプション キー&gt;** )。
クライアント ライブラリを使用するとき、サブスクリプション キーが FaceClient クラスのコンストラクターを介して渡されます。 例:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
サブスクリプションキー を取得するには、Azure portal から Azure Marketplace にアクセスします。 詳細については、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関する記事を参照してください。

## <a name="step-2-create-the-persongroup"></a>手順 2:PersonGroup を作成する

この手順で、"MyFriends" という名前の PersonGroup には Anna、Bill、および Clare が含まれています。 各ユーザーには、登録されているいくつかの顔があります。 顔は、画像から検出される必要があります。 これらのすべての手順の後に、次の画像のような PersonGroup が作成されます。

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>手順 2.1:PersonGroup のユーザーを定義する
ユーザーは、識別の基本単位です。 ユーザーには、1 つまたは複数の既知の顔を登録することができます。 PersonGroup はユーザーのコレクションです。 各ユーザーは特定の PersonGroup 内で定義されます。 識別は、PersonGroup に対して行われます。 タスクでは、PersonGroup を作成し、Anna、Bill、Clare などのユーザーをその中に作成します。

まず、[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用して新しい PersonGroup を作成します。 対応するクライアント ライブラリ API は、FaceClient クラスの CreatePersonGroupAsync メソッドです。 グループを作成するために指定されるグループ ID は、サブスクリプションごとに一意です。 他の PersonGroup API を使用して、PersonGroup を取得、更新、または削除することもできます。 

グループを定義した後、[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用して、その中にユーザーを定義できます。 クライアント ライブラリのメソッドは、CreatePersonAsync です。 作成された後に、各ユーザーに顔を追加できます。

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 手順 2.2:顔を検出し、それらを適切な人物に登録する
HTTP 要求の本文に画像ファイルを入れ、"POST" web 要求を [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API に送信することによって検出を実行します。 クライアント ライブラリを使用する場合、FaceClient クラスの DetectAsync メソッドを使用して顔の検出が行われます。

検出されたそれぞれの顔について、[PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) を呼び出してそれを正しい人物に追加します。

次のコードでは、画像から顔を検出し、それをユーザーに追加する方法を示します。

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
画像に複数の顔が含まれている場合、最大の顔だけが追加されます。 他の顔をそのユーザーに追加することができます。 "targetFace = left, top, width, height" の形式で、文字列を [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API の targetFace クエリ パラメーターに渡します。 AddPersonFaceAsync メソッドの targetFace オプション パラメーターを使用して他の顔を追加することもできます。 ユーザーに追加された顔ごとに、一意の永続的な顔 ID が付与されます。 この ID は、[PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) および [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で使用できます。

## <a name="step-3-train-the-persongroup"></a>手順 3:PersonGroup をトレーニングする

PersonGroup を使用して識別を実行する前に、PersonGroup をトレーニングする必要があります。 ユーザーを追加または削除した後、あるいはユーザーの登録済みの顔を編集した場合、PersonGroup を再トレーニングする必要があります。 トレーニングは、[PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API によって実行されます。 クライアント ライブラリを使用する場合は、TrainPersonGroupAsync メソッドの呼び出しです。
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
トレーニングは、非同期プロセスです。 また TrainPersonGroupAsync メソッドから返された後にも終了していないことがあります。 トレーニングのステータスのクエリを実行することが必要な場合があります。 [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API またはクライアント ライブラリの GetPersonGroupTrainingStatusAsync メソッドを使用します。 次の例では、PersonGroup のトレーニングの終了を待機中の単純なロジックを示します。
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>手順 4:定義されている PersonGroup に対して顔を識別する

Face API は、識別を実行するときに、グループ内のすべての顔についてテストする顔との類似性を計算します。 テストする顔に最も似たユーザーを返します。 このプロセスには、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API またはクライアント ライブラリの IdentifyAsync メソッドを使用します。

テストする顔は、前の手順を使用して検出する必要があります。 次に、顔 ID が 2 番目の引数として識別 API に渡されます。 複数の顔 ID を一度に識別できます。 結果には、識別されたすべての結果が含まれています。 既定では、識別プロセスは、テストする顔に最も一致する 1 人だけのユーザーを返します。 希望する場合は、識別プロセスが複数の候補を返せるようにする省略可能なパラメーター maxNumOfCandidatesReturned を指定します。

次のコードは識別プロセスを示しています。

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

手順が終了したら、さまざまな顔を識別してみましょう。 顔検出用にアップロードされた画像に従って、Anna、Bill、または Clare の顔を正しく識別できるかどうか確認します。 次の例を参照してください。

![さまざまな顔の識別](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>手順 5:大規模な要求

前述の設計の制限に基づいて、PersonGroup は最大 10,000 人のユーザーを保持できます。
100 万人の規模のシナリオの詳細については、「[How to use the large-scale feature](how-to-use-large-scale.md)」(大規模な機能を使用する方法) を参照してください。

## <a name="summary"></a>まとめ

このガイドでは、PersonGroup を作成し、ユーザーを識別するプロセスについて学びました。 以下の機能について説明し、例を示しました。

- [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API を使用して顔を検出します。
- [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用して PersonGroup を作成します。
- [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用してユーザーを作成します。
- [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API を使用して PersonGroup をトレーニングします。
- [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API を使用して、PersonGroup に対して不明な顔を識別します。

## <a name="related-topics"></a>関連トピック

- [顔認識の概念](../concepts/face-recognition.md)
- [画像内の顔を検出する](HowtoDetectFacesinImage.md)
- [顔を追加する](how-to-add-faces.md)
- [大規模なフィーチャーを使用する](how-to-use-large-scale.md)
