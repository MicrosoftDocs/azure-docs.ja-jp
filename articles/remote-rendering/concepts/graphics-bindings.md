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
ms.openlocfilehash: f769036ac9e5a6945e7ecad30e021d377cabd358
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020271"
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
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

上記の呼び出しは、Azure Remote Rendering を初期化して Holographic API に組み込むために必要です。 Holographic API が呼び出される前と他の Remote Rendering API がアクセスされる前に、この関数を呼び出す必要があります。 同様に、Holographic API がこれ以上呼び出されなくなった後に、対応する初期化解除関数 `RemoteManagerStatic.ShutdownRemoteRendering();` を呼び出す必要があります。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">グラフィックスのバインドへのアクセス

クライアントがセットアップされると、`AzureSession.GraphicsBinding` ゲッターを使用して基本的なグラフィックスのバインドにアクセスできます。 例として、最後のフレーム統計を次のように取得できます。

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
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
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


ここで、上記の `ptr` は、API の座標が表されるワールド空間座標系を定義するネイティブな `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` オブジェクトへのポインターである必要があります。

#### <a name="render-remote-image"></a>リモート画像をレンダリングする

各フレームの開始時に、リモート フレームをバック バッファー内にレンダリングする必要があります。 これを行うには `BlitRemoteFrame` を呼び出します。これにより、現在バインドされているレンダー ターゲットに色と深度の両方の情報が入力されます。 したがって、バック バッファーをレンダー ターゲットとしてバインドした後に、この操作を行うことが重要です。

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>シミュレーション

`GraphicsApiType.SimD3D11` はシミュレーションのバインドであり、選択した場合は `GraphicsBindingSimD3d11` グラフィックスのバインドを作成します。 このインターフェイスは、デスクトップ アプリケーションなどで頭部の移動をシミュレートするために使用され、モノスコピック画像をレンダリングします。
設定は少し複雑で、次のように機能します。

#### <a name="create-proxy-render-target"></a>プロキシ レンダー ターゲットを作成する

リモート コンテンツとローカル コンテンツは、`GraphicsBindingSimD3d11.Update` 関数によって提供されるプロキシ カメラ データを使用して、'プロキシ' という画面外の色/深度のレンダー ターゲットにレンダリングする必要があります。 プロキシは、バック バッファーの解像度と一致している必要があります。 セッションの準備ができたら、接続する前に `GraphicsBindingSimD3d11.InitSimulation` を呼び出す必要があります。

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

初期化関数には、ネイティブな d3d デバイスへのポインターと、プロキシ レンダー ターゲットの色と深度テクスチャへのポインターが渡される必要があります。 初期化されたら、`AzureSession.ConnectToRuntime` と `DisconnectFromRuntime` を何度も呼び出すことができますが、別のセッションに切り替えるときは、最初に古いセッションで `GraphicsBindingSimD3d11.DeinitSimulation` を呼び出してから、別のセッションで `GraphicsBindingSimD3d11.InitSimulation` を呼び出す必要があります。

#### <a name="render-loop-update"></a>ループの更新をレンダリングする

ループの更新のレンダリングは、次の複数の手順で構成されています。

1. 各フレームで、レンダリングが行われる前に、レンダリング対象のサーバーに送信される現在のカメラ変換を使用して `GraphicsBindingSimD3d11.Update` が呼び出されます。 同時に、返されたプロキシ変換がプロキシ カメラに適用されて、プロキシ レンダー ターゲットにレンダリングできるようになります。
返されたプロキシ更新 `SimulationUpdate.frameId` が null の場合、リモート データはまだありません。 この場合、プロキシ レンダー ターゲットにレンダリングするのではなく、現在のカメラ データを使用してローカル コンテンツをバック バッファーに直接レンダリングする必要があり、次の 2 つの手順はスキップされます。
1. アプリケーションでプロキシ レンダー ターゲットがバインドされ、`GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` が呼び出されます。 これにより、リモートの色と深度の情報がプロキシ レンダー ターゲットに入力されます。 プロキシ カメラ変換を使用して、すべてのローカル コンテンツをプロキシにレンダリングできるようになりました。
1. 次に、バック バッファーをレンダー ターゲットとしてバインドし、バック バッファーが表示できるようになった時点で `GraphicsBindingSimD3d11.ReprojectProxy` を呼び出す必要があります。

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
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
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
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

## <a name="next-steps"></a>次のステップ

* [チュートリアル:リモートでレンダリングされたモデルの表示](../tutorials/unity/view-remote-models/view-remote-models.md)
