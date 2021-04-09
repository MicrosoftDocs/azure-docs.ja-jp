---
title: グラフィックスのバインド
description: グラフィックスのバインドの設定とユース ケース
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593910"
---
# <a name="graphics-binding"></a>グラフィックスのバインド

カスタム アプリケーションで Azure Remote Rendering を使用できるようにするには、それをアプリケーションのレンダリング パイプラインに統合する必要があります。 この統合は、グラフィックスのバインドで行われます。

グラフィックスのバインドを設定すると、レンダリングされた画像に影響を与えるさまざまな関数にアクセスできるようになります。 これらの関数は、常に使用できる一般的な関数と、選択した `Microsoft.Azure.RemoteRendering.GraphicsApiType` にのみ関係する特定の関数の 2 つのカテゴリに分類できます。

## <a name="graphics-binding-in-unity"></a>Unity でのグラフィックスのバインド

Unity では、`RemoteManagerUnity.InitializeManager` に渡される `RemoteUnityClientInit` 構造体によってバインド全体が処理されます。 グラフィックス モードを設定するには、`GraphicsApiType` フィールドを、選択したバインドに設定する必要があります。 このフィールドは、XRDevice が存在するかどうかに応じて自動的に設定されます。 この動作は、次の動作で手動でオーバーライドできます。

* **HoloLens 2**: [Windows Mixed Reality](#windows-mixed-reality) のグラフィックス バインドが常に使用されます。
* **フラットな UWP デスクトップ アプリ**:[シミュレーション](#simulation)が常に使用されます。
* **Unity エディター**:WMR VR ヘッドセットが接続されていない限り、[シミュレーション](#simulation)が常に使用されます。この場合、ARR は無効になり、アプリケーションの ARR 関連以外の部分をデバッグできます。 [Holographic Remoting](../how-tos/unity/holographic-remoting.md) もご覧ください。

Unity の他の唯一の関連部分は[基本的なバインド](#access)にアクセスしているため、以下の他のすべてのセクションはスキップできます。

## <a name="graphics-binding-setup-in-custom-applications"></a>カスタム アプリケーションでのグラフィックスのバインドの設定

グラフィックスのバインドを選択するには、次の 2 つの手順を実行します。最初に、プログラムの初期化時に、グラフィックスのバインドを静的に初期化する必要があります。

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

上記の呼び出しは、Azure Remote Rendering を初期化して Holographic API に組み込むために必要です。 Holographic API が呼び出される前と他の Remote Rendering API がアクセスされる前に、この関数を呼び出す必要があります。 同様に、Holographic API がこれ以上呼び出されなくなった後に、対応する初期化解除関数 `RemoteManagerStatic.ShutdownRemoteRendering();` を呼び出す必要があります。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">グラフィックスのバインドへのアクセス

クライアントがセットアップされると、`RenderingSession.GraphicsBinding` ゲッターを使用して基本的なグラフィックスのバインドにアクセスできます。 例として、最後のフレーム統計を次のように取得できます。

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>グラフィックス API

現在、選択できるグラフィックス API は、`WmrD3D11` と `SimD3D11` の 2 つあります。 3 つ目として `Headless` がありますが、クライアント側でまだサポートされていません。

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` は、HoloLens 2 で実行される既定のバインドです。 これによって、`GraphicsBindingWmrD3d11` バインドが作成されます。 このモードでは、Azure Remote Rendering が Holographic API に直接フックされます。

派生したグラフィックスのバインドにアクセスするには、ベースの `GraphicsBinding` をキャストする必要があります。
WMR のバインドを使用するには、次の 2 つの操作が必要です。

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>使用されている座標系を Remote Rendering に通知する

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

ここで、上記の `ptr` は、API の座標が表されるワールド空間座標系を定義するネイティブな `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` オブジェクトへのポインターである必要があります。

#### <a name="render-remote-image"></a>リモート画像をレンダリングする

各フレームの開始時に、リモート フレームをバック バッファー内にレンダリングする必要があります。 これを行うには `BlitRemoteFrame` を呼び出します。これにより、現在バインドされているレンダー ターゲットに両眼の色と深度の情報が入力されます。 したがって、バック バッファーをレンダー ターゲットとしてバインドした後に、この操作を行うことが重要です。

> [!WARNING]
> リモート イメージがバック バッファーに転送された後、ローカル コンテンツは、たとえば **SV_RenderTargetArrayIndex** を使用するなど、シングルパス ステレオ レンダリング手法を利用してレンダリングされる必要があります。 別個のパスでそれぞれの眼をレンダリングするなど、他のステレオ レンダリング手法を使用すると、結果的にパフォーマンスが大幅に低下したり、グラフィックが乱れたりするため、使用を避けてください。

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>シミュレーション

`GraphicsApiType.SimD3D11` はシミュレーションのバインドであり、選択した場合は `GraphicsBindingSimD3d11` グラフィックスのバインドを作成します。 このインターフェイスは、デスクトップ アプリケーションなどで頭部の移動をシミュレートするために使用され、モノスコピック画像をレンダリングします。

シミュレーションのバインドを実装するには、[カメラ](../overview/features/camera.md)のページで説明されているように、ローカル カメラとリモート フレームの違いを理解することが重要です。

2 つのカメラが必要です。

* **ローカル カメラ**:このカメラは、アプリケーション ロジックによって駆動する現在のカメラ位置を表します。
* **プロキシ カメラ**:このカメラは、サーバーから送信された現在の "*リモート フレーム*" と一致します。 クライアントがフレームを要求してからその到着までに待機時間があるため、"*リモート フレーム*" は常にローカル カメラの動きより少し遅れます。

ここでの基本的なアプローチは、プロキシ カメラを使用してリモート画像とローカル コンテンツの両方をオフスクリーン ターゲットにレンダリングすることです。 次に、プロキシ画像がローカル カメラ領域に再投影されます。これについては、「[Late Stage Reprojection](../overview/features/late-stage-reprojection.md)」で詳しく説明します。

`GraphicsApiType.SimD3D11` ではステレオスコピック レンダリングもサポートされています。これは、以下の `InitSimulation` 設定の呼び出し中に有効にする必要があります。 設定は少し複雑で、次のように機能します。

#### <a name="create-proxy-render-target"></a>プロキシ レンダー ターゲットを作成する

リモート コンテンツとローカル コンテンツは、`GraphicsBindingSimD3d11.Update` 関数によって提供されるプロキシ カメラ データを使用して、'プロキシ' という画面外の色/深度のレンダー ターゲットにレンダリングする必要があります。

プロキシはバック バッファーの解像度と一致する必要があります。また、*DXGI_FORMAT_R8G8B8A8_UNORM* または *DXGI_FORMAT_B8G8R8A8_UNORM* 形式である必要があります。 ステレオスコピック レンダリングの場合、カラー プロキシ テクスチャと、深度の両方が使用されている場合は、深度プロキシ テクスチャに 1 つではなく 2 つの配列レイヤーが必要です。 セッションの準備ができたら、接続する前に `GraphicsBindingSimD3d11.InitSimulation` を呼び出す必要があります。

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

初期化関数には、ネイティブな d3d デバイスへのポインターと、プロキシ レンダー ターゲットの色と深度テクスチャへのポインターが渡される必要があります。 初期化されたら、`RenderingSession.ConnectAsync` と `Disconnect` を何度も呼び出すことができますが、別のセッションに切り替えるときは、最初に古いセッションで `GraphicsBindingSimD3d11.DeinitSimulation` を呼び出してから、別のセッションで `GraphicsBindingSimD3d11.InitSimulation` を呼び出す必要があります。

#### <a name="render-loop-update"></a>ループの更新をレンダリングする

ループの更新のレンダリングは、次の複数の手順で構成されています。

1. 各フレームで、レンダリングが行われる前に、レンダリング対象のサーバーに送信される現在のカメラ変換を使用して `GraphicsBindingSimD3d11.Update` が呼び出されます。 同時に、返されたプロキシ変換がプロキシ カメラに適用されて、プロキシ レンダー ターゲットにレンダリングできるようになります。
返されたプロキシ更新 `SimulationUpdate.frameId` が null の場合、リモート データはまだありません。 この場合、プロキシ レンダー ターゲットにレンダリングするのではなく、現在のカメラ データを使用してローカル コンテンツをバック バッファーに直接レンダリングする必要があり、次の 2 つの手順はスキップされます。
1. アプリケーションでプロキシ レンダー ターゲットがバインドされ、`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` が呼び出されます。 これにより、リモートの色と深度の情報がプロキシ レンダー ターゲットに入力されます。 プロキシ カメラ変換を使用して、すべてのローカル コンテンツをプロキシにレンダリングできるようになりました。
1. 次に、バック バッファーをレンダー ターゲットとしてバインドし、バック バッファーが表示できるようになった時点で `GraphicsBindingSimD3d11.ReprojectProxy` を呼び出す必要があります。

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>シミュレーション更新の構造

各フレーム (前のセクションの「**ループの更新をレンダリングする**」) にはローカル カメラに対応するカメラ パラメーターの範囲を入力する必要があります。これで、次に使用可能なフレームのカメラに対応するカメラのパラメーターのセットが返されます。 これらの 2 つのセットは、それぞれ `SimulationUpdateParameters` と `SimulationUpdateResult` の構造体でキャプチャされます。

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

構造体メンバーには次の意味があります。

| メンバー | 説明 |
|--------|-------------|
| FrameId | 連続フレーム識別子。 SimulationUpdateParameters の入力に必要であり、新しいフレームごとに継続的にインクリメントする必要があります。 フレーム データをまだ使用できない場合、SimulationUpdateResult は 0 になります。 |
| ViewTransform | フレームのカメラ ビューの変換行列の左右ステレオのペア。 モノスコピック レンダリングの場合、`Left` メンバーのみが有効です。 |
| FieldOfView | [OpenXR 視野規則](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles)におけるフレーム カメラの視野の左右ステレオ ペア。 モノスコピック レンダリングの場合、`Left` メンバーのみが有効です。 |
| NearPlaneDistance | 現在のリモート フレームの射影行列に使用される前方面の距離。 |
| FarPlaneDistance | 現在のリモート フレームの射影行列に使用される後方面の距離。 |

ステレオペア `ViewTransform` と `FieldOfView` を使用すると、ステレオスコピック レンダリングが有効な場合に、両方のアイカメラ値を設定できます。 そうしないと、`Right` メンバーは無視されます。 ご覧のとおり、射影行列が指定されていない場合、カメラの変換のみが平面の 4x4 変換行列として渡されます。 実際の行列は、指定された視野と、[CameraSettings API](../overview/features/camera.md) で設定された現在の前方面と後方面を使用して、Azure Remote Rendering によって内部的に計算されます。

実行時に [CameraSettings](../overview/features/camera.md) で前方面と後方面を必要に応じて変更することができ、サービスによってこれらの設定が非同期に適用されるので、各 SimulationUpdateResult には、対応するフレームのレンダリング時に使用される特定の前方面と後方面も保持されます。 リモート フレーム レンダリングに一致するようにローカル オブジェクトをレンダリングするために、これらの方面値を使用して射影行列を適合させることができます。

最後に、**シミュレーション更新** の呼び出しには、OpenXR 規則の視野が必要ですが、標準化とアルゴリズムの安全上の理由から、次の構造体の設定例に示す変換関数を使用できます。

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

これらの変換関数を使用すると、ローカル レンダリングのニーズに応じて、視野の仕様と平面の 4x4 パースペクティブ射影行列をすばやく切り替えることができます。 これらの変換関数には検証ロジックが含まれており、入力射影行列または入力視野が無効な場合は、有効な結果が設定されず、エラーが返されます。

## <a name="api-documentation"></a>API のドキュメント

* [C# RemoteManagerStatic.StartupRemoteRendering()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding クラス](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11 クラス](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11 クラス](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C++ RemoteRenderingInitialization 構造体](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding クラス](/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11 クラス](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11 クラス](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>次の手順

* [カメラ](../overview/features/camera.md)
* [Late Stage Reprojection](../overview/features/late-stage-reprojection.md)
* [チュートリアル:リモートでレンダリングされたモデルの表示](../tutorials/unity/view-remote-models/view-remote-models.md)