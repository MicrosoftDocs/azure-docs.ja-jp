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
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 013467cb64220b525d429c901c48028bf65b3852
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864929"
---
# <a name="example-how-to-identify-faces-in-images"></a>例:画像内の顔を識別する方法

このガイドでは、既知のユーザーから事前に作成される PersonGroups を使用して不明な顔を識別する方法を示します。 サンプルは Face API クライアント ライブラリを使用して C# で記述されています。

## <a name="concepts"></a>概念

このガイドの、次の概念についてよく知らない場合は、いつでも[用語集](../Glossary.md)で定義を検索してください、

- 顔 - 検出
- 顔 - 識別
- PersonGroup

## <a name="preparation"></a>準備

このサンプルでは、次の項目について説明します。

- PersonGroup を作成する方法 - この PersonGroup には既知のユーザーの一覧が含まれています。
- 各ユーザーに顔を割り当てる方法 - これらの顔はユーザーを識別するための基準として使用されます。 写真 ID と同じように、正面を向いたクリアな顔を使用することをお勧めします。 写真の適切なセットには、異なるポーズ、服の色、またはヘアー スタイルが同じ個人の顔を含める必要があります。

このサンプルのデモを実行するには、一連の画像を準備する必要があります。

- 個人の顔を含むいくつかの写真。 [ここをクリックして、Anna、Bill、Clare のサンプルの写真をダウンロードします](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)。
- テストの識別に使用される Anna、Bill、または Clare の顔が含まれていなくてもかまわない一連のテスト用の写真。 上記のリンクからいくつかのサンプル画像を選択することもできます。

## <a name="step-1-authorize-the-api-call"></a>手順 1:API 呼び出しを承認する

Face API を呼び出すたびに、サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターを通じて渡すか、または要求ヘッダーで指定できます。 クエリ文字列を介してサブスクリプション キーを渡すには、例として[画面 - 検出](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)の要求 URL を参照してください。
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

その代わりに、HTTP 要求ヘッダー内でサブスクリプション キーを指定することもできます (**ocp-apim-subscription-key:&lt;サブスクリプション キー&gt;**)。クライアント ライブラリを使用するとき、サブスクリプション キーが FaceServiceClient クラスのコンストラクターを介して渡されます。 例: 
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
サブスクリプション キーは、Azure Poral の [Marketplace] ページから入手できます。 [サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。

## <a name="step-2-create-the-persongroup"></a>手順 2:PersonGroup を作成する

この手順では、"MyFriends" という名前の PersonGroup を作成しました。これには、3 人が含まれています (Anna、Bill、および Clare)。 各ユーザーには、登録されているいくつかの顔があります。 顔は、画像から検出される必要があります。 これらのすべての手順の後に、次の画像のような PersonGroup が作成されます。

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 PersonGroup のユーザーを定義する
ユーザーは、識別の基本単位です。 ユーザーには、1 つまたは複数の既知の顔を登録することができます。 ただし、PersonGroup はユーザーのコレクションであり、各ユーザーは特定 PersonGroup 内で定義されます。 識別は、PersonGroup に対して行われます。 そのため、タスクでは、PersonGroup を作成し、Anna、Bill、Clare などのユーザーをその中に作成します。

最初に、新しい PersonGroup を作成する必要があります。 これは、[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用して実行されます。 対応するクライアント ライブラリ API は、FaceServiceClient クラスの CreatePersonGroupAsync メソッドです。 グループを作成するために指定されるグループ ID は各サブスクリプションに固有です。他の PersonGroups API を使用して PersonGroup を取得、更新、または削除することもできます。 グループを定義したら、[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用して、その中にユーザーを定義できます。 クライアント ライブラリのメソッドは、CreatePersonAsync です。 作成された後に、各ユーザーに顔を追加できます。

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 顔を検出し、それらを適切な人物に登録する
HTTP 要求の本文に画像ファイルを入れ、"POST" web 要求を [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API に送信することによって検出を実行します。 クライアント ライブラリを使用する場合、FaceServiceClient クラスの DetectAsync メソッドを使用して顔の検出が実行されます。

検出された各顔について、[PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) を呼び出してそれを正しい人物に追加することができます。

次のコードでは、画像から顔を検出し、それをユーザーに追加する方法を示します。
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
画像に複数の顔が含まれている場合、最大の顔だけが追加されることに注意してください。 ユーザーに他の顔を追加するには、"targetFace = left, top, width, height" の形式の文字列を [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API の targetFace クエリ パラメーターに渡します。または、AddPersonFaceAsync メソッドの targetFace オプション パラメーターを使用して他の顔を追加します。 ユーザーに追加される各顔には、固有で永続的な顔 ID が指定されます。これを [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) および [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で使用できます。

## <a name="step-3-train-the-persongroup"></a>手順 3:PersonGroup をトレーニングする

PersonGroup を使用して識別を実行する前に、PersonGroup をトレーニングする必要があります。 さらに、ユーザーが追加または削除された後、またはユーザーの登録済みの顔が編集された場合、それを保持する必要があります。 トレーニングは、[PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API によって実行されます。 クライアント ライブラリを使用する場合は、TrainPersonGroupAsync メソッドを呼び出すだけです。
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
トレーニングは、非同期プロセスです。 また TrainPersonGroupAsync メソッドから返された後にも終了していないことがあります。 場合によっては、[PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API またはクライアント ライブラリの GetPersonGroupTrainingStatusAsync メソッドを使用してトレーニングのステータスのクエリを実行する必要があります。 次の例では、PersonGroup のトレーニングの終了を待機中の単純なロジックを示します。
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>手順 4:定義されている PersonGroup に対して顔を識別する

識別を実行するときには、Face API は、グループ内のすべての顔の間でテストする顔の類似性を計算し、そのテストする顔と最も類似しているユーザーを返します。 これには、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API またはクライアント ライブラリの IdentifyAsync メソッドを使用します。

テスト対象の顔は、前の手順を使用して検出される必要があり、顔 ID は、Identify API に、2 番目の引数として渡されます。 一度に複数の顔 ID を識別することができ、結果にすべての ID が含まれます。 既定では、識別は、テストする顔に最も一致する 1 人だけのユーザーを返します。 希望する場合は、複数の候補を返して識別できるようにする省略可能なパラメーター maxNumOfCandidatesReturned を指定できます。

次のコードは識別のプロセスを示しています。

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
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
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

手順が完了したら、別の顔を識別し、顔の検出にアップロードされた画像に従って、Anna、Bill、または Clare の顔が正しく識別されたかどうかを確認できます。 次の例を参照してください。

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>手順 5:大規模な要求を行う

既知のように、PersonGroup は前述の設計の制限のために、最大 10,000 人のユーザーを保持できます。
100 万人の規模のシナリオの詳細については、「[How to use the large-scale feature](how-to-use-large-scale.md)」(大規模な機能を使用する方法) を参照してください。

## <a name="summary"></a>まとめ

このガイドでは、PersonGroup を作成し、ユーザーを識別するプロセスについて学びました。 これまでに説明してデモを示した機能の参照先は以下のとおりです。

- [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API を使用して顔を検出する
- PersonGroup の作成に [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API を使用
- Person の作成に [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API を使用
- PersonGroup のトレーニングに [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API を使用
- [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API を使用して PersonGroup に対して不明な顔を識別する

## <a name="related-topics"></a>関連トピック

- [画像内の顔を検出する方法](HowtoDetectFacesinImage.md)
- [顔を追加する方法](how-to-add-faces.md)
- [大規模なフィーチャーを使用する方法](how-to-use-large-scale.md)
