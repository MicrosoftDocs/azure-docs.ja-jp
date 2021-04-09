---
title: 予測エンドポイントを使用してプログラムで分類器を指定してテストする - Custom Vision
titleSuffix: Azure Cognitive Services
description: API を使用して、Custom Vision Service の分類器によるイメージのテストをプログラムから行う方法を説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934754"
---
# <a name="use-your-model-with-the-prediction-api"></a>Prediction API でモデルを使用する

モデルをトレーニングした後、イメージを Prediction API エンドポイントに送信することで、プログラムからイメージをテストできます。

> [!NOTE]
> このドキュメントでは、C# を使用して Prediction API にイメージを送信する方法を示します。 詳細および例については、[Prediction API リファレンス](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)に関するページを参照してください。

## <a name="publish-your-trained-iteration"></a>トレーニング済みイテレーションを発行する

[Custom Vision の Web ページ](https://customvision.ai)から自分のプロジェクトを選択し、__[Performance]__ \(パフォーマンス) タブを選択します。

Prediction API にイメージを送信するには、まず予測のイテレーションを発行する必要があり、これは __[Publish]__ \(発行\) を選択して、発行されたイテレーションの名前を指定して実行できます。 これにより、モデルが、Custom Vision Azure リソースの Prediction API にアクセスできるようになります。

![[Publish]\(発行\) ボタンが赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/unpublished-iteration.png)

モデルが正常に発行されると、左側のサイドバー内のイテレーションの横に [発行済み] ラベルが表示されます。また、イテレーションの名前がイテレーションの説明内に表示されます。

![発行済みラベルと発行済みイテレーションの名前が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL と予測キーを取得する

モデルが発行されたら、必要な情報を取得するために __[Prediction URL]__ を選択します。 これにより、ダイアログが開き、__Prediction URL__ や __Prediction-Key__ などの Prediction API を使用するための情報が表示されます。

![[Prediction URL] ボタンが赤色の四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration-prediction-url.png)

![イメージ ファイルと Prediction-Key 値を使用するための Prediction URL が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/prediction-api-info.png)


このガイドでは、ローカル イメージを使用します。そのため、**[If you have an image file]\(イメージ ファイルがある場合\)** の下にある URL を一時的な場所にコピーします。 対応する __Prediction-Key__ 値もコピーします。

## <a name="create-the-application"></a>アプリケーションを作成する

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。

1. __Program.cs__ ファイルの本文として次のコードを使用します。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 次の情報を変更します。
   * `namespace` フィールドには、プロジェクトの名前を設定します。
   * プレースホルダー `<Your prediction key>` は、前に取得したキー値に置き換えます。
   * プレースホルダー `<Your prediction URL>` は、前に取得した URL に置き換えます。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行すると、イメージ ファイルのパスをコンソールに入力するように求められます。 これにより、イメージが Prediction API に送信され、予測結果が JSON 形式の文字列として返されます。 応答の例を次に示します。

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>次のステップ

このガイドでは、C# SDK を使用して、カスタム画像分類器/検出器にイメージを送信し、応答をプログラムで受信する方法について説明しました。 次は、C# を使用してエンドツーエンドのシナリオを完了したり、さまざまな言語の SDK を使用して作業を開始したりする方法について説明します。

* [クイック スタート: Custom Vision SDK](quickstarts/image-classification.md)
