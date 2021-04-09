---
title: サーバー側のパフォーマンス クエリ
description: API 呼び出しを使用してサーバー側のパフォーマンス クエリを実行する方法
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594097"
---
# <a name="server-side-performance-queries"></a>サーバー側のパフォーマンス クエリ

サーバー上のレンダリング パフォーマンスが優れていることは、安定したフレーム レートと優れたユーザー エクスペリエンスのために不可欠です。 サーバー上のパフォーマンス特性を注意深く監視し、必要に応じて最適化することが重要です。 パフォーマンス データは、専用の API 関数を使用してクエリできます。

レンダリングのパフォーマンスに最も強く影響するのは、モデルの入力データです。 「[モデルの変換を構成する](../../how-tos/conversion/configure-model-conversion.md)」で説明されているように、入力データを調整できます。

クライアント側のアプリケーションのパフォーマンスもボトルネックになる可能性があります。 クライアント側のパフォーマンスを詳細に分析するために、[:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md)を実行することをお勧めします。

## <a name="clientserver-timeline"></a>クライアント/サーバーのタイムライン

さまざまな待機時間の値について詳しく説明する前に、タイムライン上のクライアントとサーバーとの同期ポイントを確認することをお勧めします。

![パイプラインのタイムライン](./media/server-client-timeline.png)

この図は、次の方法を示しています。

* クライアントによって (16.6 ミリ秒ごとに) 60 Hz の固定フレーム レートで *姿勢推定* が開始されます
* その後、サーバーがその姿勢に基づいてレンダリングを開始します
* サーバーがエンコードされた動画の画像を送信します
* クライアントがその画像をデコードし、その画像に対して CPU と GPU で何らかの処理を実行した後、画像を表示します

## <a name="frame-statistics-queries"></a>フレーム統計情報クエリ

フレームの統計データは、待機時間などの最後のフレームに関する何らかの概要レベルの情報を提供します。 `FrameStatistics` 構造体で提供されるデータはクライアント側で測定されるため、API は同期呼び出しになります。

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

取得した `FrameStatistics` オブジェクトには、次のメンバーが格納されています。

| メンバー | 説明 |
|:-|:-|
| LatencyPoseToReceive | クライアント デバイスでのカメラの姿勢推定から、この姿勢のサーバー フレームがクライアント アプリケーションで完全に利用可能になるまでの待機時間。 この値には、ネットワークのラウンドトリップ、サーバーのレンダリング時間、動画のデコード、およびジッターの補正が含まれます。 **上の図の間隔 1** をご覧ください。|
| LatencyReceiveToPresent | 受信したリモート フレームが使用可能になってから、クライアント アプリが CPU に対して PresentFrame を呼び出すまでの待機時間。 **上の図の間隔 2** をご覧ください。|
| LatencyPresentToDisplay  | CPU 上にフレームが表示されてから、ディスプレイが点灯するまで待機時間。 この値には、クライアントの GPU 時間、OS で実行されたすべてのフレーム バッファリング、ハードウェアの再投影、およびデバイスに依存するディスプレイのスキャンアウト時間が含まれます。 **上の図の間隔 3** をご覧ください。|
| TimeSinceLastPresent | それ以降に CPU で実行される PresentFrame への呼び出しの間隔。 表示時間 (60 Hz のクライアントデバイスで 16.6 ミリ秒など) より大きい値は、クライアント アプリケーションが時間内に CPU ワークロードを完了しないことが原因で発生した問題を示しています。|
| VideoFramesReceived | 最後の 1 秒間にサーバーから受信されたフレームの数。 |
| VideoFrameReusedCount | デバイスで複数回使用された、最後の 1 秒間に受信されたフレームの数。 0 以外の値は、ネットワークのジッターまたはサーバーでの過剰なレンダリング時間が原因でフレームを再利用および再投影する必要があったことを示しています。 |
| VideoFramesSkipped | デコードされたが、新しいフレームが到着したためにディスプレイに表示されなかった、最後の 1 秒間に受信されたフレームの数。 0 以外の値は、ネットワークのジッターが原因で複数のフレームが遅延した後、クライアント デバイスにまとめてバーストとして到着したことを示しています。 |
| VideoFramesDiscarded | **VideoFramesSkipped** とよく似ていますが、破棄される理由は、フレームの到着が保留中のどの姿勢とも関連付けることができないほど遅延したことです。 この破棄の問題が発生した場合は、ネットワークに何らかの重大な競合があります。|
| VideoFrameMinDelta | 最後の 1 秒間に到着した 2 つの連続するフレーム間の最小時間。 VideoFrameMaxDelta と共に、この範囲はネットワークまたはビデオ コーデックによって発生したジッターを示しています。 |
| VideoFrameMaxDelta | 最後の 1 秒間に到着した 2 つの連続するフレーム間の最大時間。 VideoFrameMinDelta と共に、この範囲はネットワークまたはビデオ コーデックによって発生したジッターを示しています。 |

すべての待機時間値の合計は通常、60 Hz で使用可能なフレーム時間よりもかなり大きくなります。 これは問題ありません。図に示すように、複数のフレームが並列で処理され、新しいフレーム要求が目的のフレーム レートで開始されるからです。 ただし、待機時間が長すぎると、[遅延段階の再投影](../../overview/features/late-stage-reprojection.md)の品質に影響して、全体的なエクスペリエンスが低下する可能性があります。

`VideoFramesReceived`、`VideoFrameReusedCount`、および `VideoFramesDiscarded` を使用して、ネットワークとサーバーのパフォーマンスを測定できます。 低 `VideoFramesReceived` 値と高 `VideoFrameReusedCount` 値の組み合わせは、ネットワークの輻輳やサーバーのパフォーマンスの低下を示している可能性があります。 `VideoFramesDiscarded` 値が高い場合もネットワークの輻輳を示しています。

最後に、`TimeSinceLastPresent`、`VideoFrameMinDelta`、および `VideoFrameMaxDelta` は、受信するビデオ フレームとローカルで存在する呼び出しの分散についての概念を提供します。 高分散は、フレーム レートが不安定であることを意味します。

上記のどの値も、純粋なネットワーク待機時間 (図の赤い矢印) を明確に示すものではありません。サーバーがレンダリングでビジー状態になっている正確な時間をラウンドトリップ値 `LatencyPoseToReceive` から差し引く必要があるからです。 全体の待機時間のサーバー側の部分は、クライアントで使用できない情報です。 ただし、次の段落では、この値がサーバーからの追加の入力によってどのように近似され、`NetworkLatency` 値によって公開されるかについて説明します。

## <a name="performance-assessment-queries"></a>パフォーマンス評価クエリ

*パフォーマンス評価クエリ* では、サーバー上の CPU および GPU ワークロードに関する詳細な情報を提供します。 データはサーバーから要求されるため、パフォーマンスのスナップショットのクエリは通常の非同期パターンに従います。

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

`FrameStatistics` オブジェクトとは対照的に、`PerformanceAssessment` オブジェクトにはサーバー側の情報が含まれています。

| メンバー | 説明 |
|:-|:-|
| TimeCPU | サーバーのフレームあたりの平均 CPU 時間 (ミリ秒) |
| TimeGPU | サーバーのフレームあたりの平均 GPU 時間 (ミリ秒) |
| UtilizationCPU | サーバーの合計 CPU 使用率 (%) |
| UtilizationGPU | サーバーの合計 GPU 使用率 (%) |
| MemoryCPU | サーバーのメイン メモリの合計使用率 (%) |
| MemoryGPU | サーバー GPU の専用ビデオ メモリの合計使用率 (%) |
| NetworkLatency | ラウンドトリップ ネットワークのおおよその平均待機時間 (ミリ秒)。 上の図では、この値は赤い矢印の合計に相当します。 この値は、`FrameStatistics` の `LatencyPoseToReceive` 値からサーバーでの実際のレンダリング時間を差し引くことによって計算されます。 この概算値は正確ではありませんが、クライアントで計算された待機時間の値から分離されたネットワーク待機時間があることを示しています。 |
| PolygonsRendered | 1 つのフレームにレンダリングされる三角形の数。 この数には、後でレンダリング中にカリングされる三角形も含まれています。 つまり、この数はカメラの位置によって変わることはありませんが、三角形のカリング率に応じてパフォーマンスは大幅に変わる可能性があります。|

値の評価に役立つように、各部分には **優良**、**良**、**平均**、**不良** などの品質分類が付いています。
この評価メトリックでは、サーバーの正常性が大まかに示されますが、それを絶対的なものであると見なさないでください。 たとえば、GPU 時間のスコアが "平均" であるとします。 それが平均と見なされるのは、フレーム時間の全体的な割り当て量の上限に近づいているためです。 ただし、この場合は、複雑なモデルをレンダリングしているので、考えようによってはそれでも良い値です。

## <a name="statistics-debug-output"></a>統計情報のデバッグ出力

クラス `ServiceStatistics` は、フレーム統計とパフォーマンス評価の両方のクエリをラップし、統計情報を集計された値として、または事前に構築された文字列として返すための便利な機能を提供する C# クラスです。 次のコードは、クライアント アプリケーションでサーバー側の統計情報を表示する最も簡単な方法です。

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

上記のコードにより、テキスト ラベルに次のテキストが入力されます。

![ArrServiceStats の文字列出力](./media/arr-service-stats.png)

`GetStatsString` API によってすべての値の文字列を書式設定できますが、それぞれの単一値を `ServiceStatistics` インスタンスからプログラムでクエリすることもできます。

また、メンバーには、時間の経過と共に値を集計するいくつかのバリエーションもあります。 サフィックス `*Avg`、`*Max`、または `*Total` の付いたメンバーをご覧ください。 メンバー `FramesUsedForAverage` は、こうした集計に使用されていたフレームの数を示します。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.QueryServerPerformanceAssessmentAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection::QueryServerPerformanceAssessmentAsync()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>次のステップ

* [パフォーマンス トレースを作成する](../../how-tos/performance-tracing.md)
* [モデルの変換を構成する](../../how-tos/conversion/configure-model-conversion.md)