---
title: ほぼリアルタイムでビデオを分析する - Computer Vision
titleSuffix: Azure Cognitive Services
description: ライブ ビデオ ストリームから取得したフレームに対する分析を、Computer Vision API を使用してほぼリアルタイムで実行します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: f4410d9cab5677327d2950dfdc1a093140f31708
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102273"
---
# <a name="analyze-videos-in-near-real-time"></a>ほぼリアルタイムでビデオを分析する

この記事では、ライブ ビデオ ストリームから取得したフレームに対する分析を、Computer Vision API を使用してほぼリアルタイムで実行する方法を紹介します。 そのような分析の基本的な要素は次のとおりです。

- ビデオ ソースからフレームを取得する。
- 分析対象のフレームを選択する。
- それらのフレームを API に送信する。
- API 呼び出しから返される各分析結果を使用する。

この記事のサンプルは C# で書かれています。 このコードにアクセスするには、GitHub の[ビデオ フレーム分析のサンプル](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ページに移動してください。

## <a name="approaches-to-running-near-real-time-analysis"></a>ほぼリアルタイムで分析を実行するためのアプローチ

ビデオ ストリームに対してほぼリアルタイムで分析を実行するという課題は、さまざまな方法を使用して解決できます。 この記事では、レベルの低いものから高いものの順に 3 つの方法を取り上げます。

### <a name="design-an-infinite-loop"></a>無限ループを設計する

ほぼリアルタイムの分析向けの最も単純な設計は、無限ループです。 このループの繰り返すたびにフレームを取り込み、分析し、結果を使用します。

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

軽量のクライアント側アルゴリズムで分析が構成されている場合は、このアプローチが適していると考えられます。 ただし、分析がクラウドで行われるときは、それに伴う待ち時間のために、API 呼び出しに数秒かかることがあります。 その間は画像のキャプチャは行われず、スレッドは基本的に何もしません。 最大フレーム レートは、API 呼び出しの待ち時間によって制限されます。

### <a name="allow-the-api-calls-to-run-in-parallel"></a>API 呼び出しを並列実行できるようにする

単純なシングルスレッドのループは、軽量のクライアント側アルゴリズムには適していますが、クラウドの API 呼び出しに伴う待ち時間には十分に対処できません。 この問題の解決策は、実行時間の長い API 呼び出しを、フレームの取り込みと並列に実行できるようにすることです。 C# では、タスクベースの並列化を使用して実現できます。 たとえば、次のコードを実行できます。

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

このアプローチでは、それぞれの分析を個別のタスクで起動します。 それらのタスクをバックグラウンドで実行しながら、新しいフレームを取得し続けることができます。 このアプローチであれば、API 呼び出しから制御が戻るまで待機する間、メイン スレッドがブロックされることはありません。 ただし、このアプローチは多少のデメリットを伴う場合があります。
* 単純なバージョンで得られているいくつかの保証は失われます。 つまり、複数の API 呼び出しが並列に行われると、その結果が間違った順序で返されることがあります。 
* これにより、複数のスレッドが同時に ConsumeResult() 関数に入る可能性も生じます。関数がスレッドセーフでない場合は、それが危険である可能性があります。 
* 最後に、この単純なコードは作成されるタスクを追跡しないので、例外は自動的に表示されなくなります。 したがって、"consumer" スレッドを追加する必要があります。これは、分析タスクを追跡し、例外を発生させ、実行時間の長いタスクを中止し、正しい順序で 1 つずつ結果が使用されるようにします。

### <a name="design-a-producer-consumer-system"></a>プロデューサー/コンシューマー システムを設計する

最後のアプローチは、"プロデューサー/コンシューマー" システムの設計です。この場合、前述した無限ループに似たプロデューサー スレッドを作成します。 ただし、プロデューサーは、利用できる状態になった分析結果をすぐに使用するのではなく、単にタスクをキュー入れて追跡します。

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

また、コンシューマー スレッドも作成します。これはタスクをキューから取り出し、タスクが完了するのを待ち、結果を表示するか、スローされた例外を発生させます。 キューの使用により、システムの最大フレーム レートを制限せずに一度に 1 つずつ正しい順序で結果が使用されることを保証できます。

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>ソリューションを実装する

### <a name="get-started-quickly"></a>すぐに使い始められる

お使いのアプリをできるだけ迅速に稼働させることができるように、前のセクションで説明したシステムを既に実装してあります。 使いやすく、それでいて多くのシナリオに対応できるだけの柔軟性が考慮されています。 このコードにアクセスするには、GitHub の[ビデオ フレーム分析のサンプル](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ページに移動してください。

このライブラリには、`FrameGrabber` クラスが含まれています。Web カメラからのビデオ フレームを処理するために、前述したプロデューサー/コンシューマー システムがこのクラスに実装されています。 ユーザーは、正確なフォームの API 呼び出しを指定でき、クラスは、新しいフレームが獲得されたとき、または新しい分析結果が利用可能になったときに呼び出しコードに知らせるためにイベントを使用します。

いくつかの可能性を示すため、このライブラリを使用する 2 つのサンプル アプリを用意しました。 

1 つ目のサンプル アプリは単純なコンソール アプリで、既定の Web カメラからフレームを取得し、それらを顔検出のために Face API に送信します。 次のコードは、このアプリの簡略化バージョンを再現したものです。

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

2 つ目のサンプル アプリはさらに少し興味深いものです。 ビデオ フレームに対してどの API を呼び出すかの選択が可能になっています。 左側には、ライブ ビデオのプレビューが表示されます。 右側には、対応するフレームに対する最新の API 結果がオーバーレイされます。

ほとんどのモードで、左のライブ ビデオと右に表示された分析との間に視覚的な遅延が生じます。 この遅延は、API 呼び出しにかかる時間です。 これに該当しないのは、"EmotionsWithClientFaceDetect" モードになっているときで、このモードでは、Azure Cognitive Services に画像を送信する前に、OpenCV を使用してクライアント コンピューターでローカルに顔検出が実行されます。 

このアプローチを使用することで、検出した顔をすぐに表示できます。 その後 API 呼び出しから制御が戻ったときに感情を更新できます。 これは "ハイブリッド" アプローチの可能性を示しています。 つまり、クライアント上で単純な処理をいくつか実行してから、必要に応じて Cognitive Services APIs を使用し、より高度な分析によってこの処理を強化することができます。

![タグ付きの画像が表示された LiveCameraSample アプリ](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>サンプルをコードベースに統合する

このサンプルの使用を開始するには、次の手順を実行します。

1. Vision API の API キーを[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)から取得します。 ビデオ フレーム分析の場合、適用可能な API は次のとおりです。
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub リポジトリを複製します。

3. Visual Studio 2015 以降でサンプルを開き、サンプル アプリケーションをビルドして実行します。
    - BasicConsoleSample の場合、Face API キーは、[BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 内に直接ハードコードされています。
    - LiveCameraSample の場合、アプリの**設定**ウィンドウにキーを入力します。 これらのキーは、セッションを移動してもユーザー データとして残されます。

サンプルを統合する準備ができたら、自分のプロジェクトから VideoFrameAnalyzer ライブラリを参照します。

VideoFrameAnalyzer の画像、音声、ビデオ、またはテキストの解釈機能は、Azure Cognitive Services を使用しています。 Microsoft は、開発者が (このアプリから) アップロードする画像、音声、ビデオ、およびその他のデータを受け取り、サービス向上の目的でそれらを使用する場合があります。 アプリによって Azure Cognitive Services に送信されるデータの所有者の保護にご協力をお願いします。

## <a name="summary"></a>まとめ

このガイドでは、Face API と Computer Vision API を使用してライブ ビデオ ストリームでほぼリアルタイムの分析を実行する方法を学習しました。 また、サンプル コードを実際に使ってみる方法についても学習しました。 無料の API キーを使用して、アプリのビルドを開始するには、[Azure Cognitive Services のサインアップ ページ](https://azure.microsoft.com/try/cognitive-services/)にアクセスしてください。

フィードバックや提案は、[GitHub リポジトリ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)からお気軽にお寄せください。 API に関するより幅広いフィードバックについては、[UserVoice サイト](https://cognitive.uservoice.com/)にアクセスしてください。

