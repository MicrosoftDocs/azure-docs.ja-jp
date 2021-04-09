---
title: Runtime SDK の概要
description: Object Anchors Runtime SDK について理解を深めます。
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 74663f05c5ff995a090c7cd35e4edf46a754da17
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034610"
---
# <a name="runtime-sdk-overview"></a>Runtime SDK の概要

このセクションでは、Object Anchors モデルを使ったオブジェクトの検出に使用される Object Anchors Runtime SDK の概要について説明します。 オブジェクトの表現方法と各種コンポーネントの用途を理解できます。

下で説明するすべての型は、**Microsoft.MixedReality.ObjectAnchors** 名前空間にあります。

## <a name="types"></a>型

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) は、物理オブジェクトのジオメトリを表し、検出と姿勢推定に必要なパラメーターをエンコードします。 これは、[Object Anchors サービス](../quickstarts/get-started-model-conversion.md)を使用して作成する必要があります。 その後、アプリケーションは、Object Anchors API を使用して、生成されたモデル ファイルを読み込み、視覚化のためにそのモデルに埋め込まれているメッシュに対してクエリを実行できます。

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) は、1 つまたは複数のオブジェクトを検索する領域を指定します。 これは、空間グラフ ノード ID と、空間グラフ ノード ID で表される座標系の空間境界によって定義されます。 Object Anchors Runtime SDK では、4 種類の境界 (すなわち、**ビューのフィールド**、**境界ボックス**、**球**、および **場所**) がサポートされています。

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) は、指定したモデルのオブジェクトを検索する方法を **オブジェクト オブザーバー** に伝えます。 次のチューニング可能なパラメーターが用意されています。その既定値は、オブジェクト モデルから取得できます。

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

[MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) プロパティでは、インスタンスを検出済みと見なす値が示されます。

**オブザーバー** は、オブジェクトの候補ごとに、変換されたオブジェクト モデルとシーンの間で重複するサーフェスの比率を計算し、カバレッジの比率が所定のしきい値を超えた場合にのみ、アプリケーションにその候補を報告します。

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) プロパティは、ターゲット オブジェクトがグランド プレーン上にあると予想されているかどうかを示します。

グランド プレーンは、検索領域内で最も低い水平床です。 これにより、可能なオブジェクトの姿勢に対して適切な制約が提供されます。 このフラグをオンにすると、**オブザーバー** によって、限られた空間内の姿勢が推定され、精度が向上する場合があります。 モデルがグランド プレーン上にあると想定されていない場合、このパラメーターは無視されます。

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) プロパティは、オブジェクト インスタンスの上方向と重力の間の予想される最大角度 (度数) を示します。

このパラメーターによって、推定される姿勢の上方向に対して別の制約が提供されます。 たとえば、オブジェクトが真っすぐ立っている場合、このパラメーターは 0 にすることができます。 Object Anchors は、モデルと異なるオブジェクトを検出することは想定していません。 モデルが真っすぐ立っている場合は、横向きに置かれたインスタンスは検出されません。 横向きのレイアウト用に新しいモデルが使用されます。 関節についても同じルールが適用されます。

#### <a name="maxscalechange"></a>MaxScaleChange

[MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) プロパティは、空間マッピングに対するオブジェクトの最大のスケール変更 (0 から 1 以内) を示します。 推定されたスケールは、原点を中心とし、座標軸に沿った、変換されたオブジェクトの頂点に適用されます。 推定されるスケールは、CAD モデルとそれを物理的に表したものとの間の実際のスケールではなく、アプリが物理オブジェクト上の空間マッピングに近いオブジェクト モデルをレンダリングできるようにするいくつかの値である場合があります。

#### <a name="searchareas"></a>SearchAreas

[SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) プロパティは、オブジェクトを検索する空間境界の配列を示します。

**オブザーバー** は、クエリに指定されたすべての検索領域の結合空間内でオブジェクトを検索します。 このリリースでは、待機時間を短縮するために、最も信頼度の高いオブジェクトが最大で 1 つ返されます。

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) は、特定のモデルのインスタンスが HoloLens の座標系に存在する可能性がある仮想的位置を表します。 各インスタンスには、推定された姿勢がどのくらい適切かを示す `SurfaceCoverage` プロパティが付属します。

インスタンスは、`ObjectObserver.DetectAsync` メソッドを呼び出すことによって作成され、有効である間は自動的にバックグラウンドで更新されます。 アプリケーションは、特定のインスタンスの状態変更イベントをリッスンしたり、更新を一時停止または再開するように追跡モードを変更したりできます。 追跡が失われると、インスタンスは自動的に **オブザーバー** から削除されます。

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) は、オブジェクト モデルを読み込み、それらのインスタンスを検出し、HoloLens 座標系での各インスタンスの 6-DoF 姿勢を報告します。

オブジェクト モデルまたはインスタンスはいずれも **オブザーバー** から作成されますが、その有効期間は独立しています。 アプリケーションは、オブザーバーを破棄して、オブジェクト モデルまたはインスタンスを引き続き使用できます。

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) は診断を記録し、データをアーカイブに書き込みます。

診断アーカイブには、シーン ポイント クラウド、オブザーバーの状態、およびモデルに関する情報が含まれています。 この情報は、発生する可能性のあるランタイムの問題を特定するのに役立ちます。 詳細については、[FAQ](../faq.md) をご覧ください。

## <a name="runtime-sdk-usage-and-details"></a>Runtime SDK の使用方法と詳細

このセクションでは、Runtime SDK の使用方法の基本について説明します。 これにより、サンプル アプリケーションを参照して Object Anchors が全体的にどのように使用されるかを確認するための十分なコンテキストを得ることができます。

### <a name="initialization"></a>初期化

アプリケーションは、`ObjectObserver.IsSupported()` API を呼び出して、Object Anchors を使用する前に、それがデバイスでサポートされているかどうかを判断する必要があります。 `ObjectObserver.IsSupported()` API から `false` が返された場合は、アプリケーションで **spatialPerception** 機能が有効になっていることを確認するか、最新の HoloLends OS にアップグレードしてください。

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

次に、アプリケーションによってオブジェクト オブザーバーが作成され、[Object Anchors のモデル変換サービス](../quickstarts/get-started-model-conversion.md)によって生成される必要なモデルが読み込まれます。

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

アプリケーションによって、空間内でそのモデルのインスタンスを検出するためのクエリが作成されます。

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

既定で、検出された各インスタンスは、**オブザーバー** によって自動的に追跡されます。 これらのインスタンスは、追跡モードを変更したり、状態変更イベントをリッスンしたりして、必要に応じて操作できます。

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

状態変更イベントでは、最新の状態を照会したり、追跡が失われた場合にインスタンスを破棄したりできます。

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

また、アプリケーションは、必要に応じて、オフラインのデバッグ用に 1 つまたは複数の診断セッションを記録することもできます。

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

最後に、すべてのオブジェクトを破棄してリソースを解放します。

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
