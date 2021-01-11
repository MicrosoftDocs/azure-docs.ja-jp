---
title: Remote Rendering を C++/DirectX11 ホログラフィック アプリに統合する
description: Visual Studio プロジェクトのウィザードを使用して作成された単純な C++/DirectX11 ホログラフィック アプリに Remote Rendering を統合する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9457323b2642d0e7c5a623c13ec8440520bd5b8b
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891778"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>チュートリアル:Remote Rendering を HoloLens Holographic アプリに統合する

このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
>
> * Visual Studio を使用して、HoloLens にデプロイできるホログラフィック アプリを作成する
> * ローカルでのレンダリングとリモートでレンダリングされるコンテンツとを組み合わせるために必要なコード スニペットとプロジェクト設定を追加する

このチュートリアルでは、ネイティブな `Holographic App` サンプルに必要なビットを追加して、ローカルでのレンダリングと Azure Remote Rendering を組み合わせる方法について主に取り上げます。 このアプリの状態のフィードバックは、Visual Studio 内のデバッグ出力パネルを通じてのみ得られます。そのため、このサンプルは Visual Studio 内から開始することをお勧めします。 動的なテキスト パネルをゼロから作成するには多くのコーディングが必要になるため、適切なアプリ内フィードバックの追加は、このサンプルの対象範囲外になります。 最初は、[GitHub の Remoting Player サンプル プロジェクト](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content)に含まれているクラス `StatusDisplay` から開始することをお勧めします。 実際、このチュートリアルの既定のバージョンでは、このクラスのローカル コピーを使用しています。

> [!TIP]
> [ARR サンプルのリポジトリ](https://github.com/Azure/azure-remote-rendering)には、このチュートリアルの結果が、使用できる状態の Visual Studio プロジェクトとして含まれています。 これには、UI クラス `StatusDisplay` を通じた適切なエラーおよび状態レポートによる強化も施されています。 チュートリアル内では、ARR 固有の追加はすべて `#ifdef USE_REMOTE_RENDERING` / `#endif` によってスコープ指定されています。そのため、Remote Rendering の追加を特定するのは容易です。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

* アカウント情報 (アカウント ID、アカウント キー、サブスクリプション ID)。 アカウントをお持ちでない場合は、[アカウントを作成](../../../how-tos/create-an-account.md)してください。
* Windows SDK 10.0.18362.0 [(ダウンロード)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。
* 最新バージョンの Visual Studio 2019 [(ダウンロード)](https://visualstudio.microsoft.com/vs/older-downloads/)。
* [Visual Studio tools for Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools)。 具体的には、次の "*ワークロード*" のインストールが必須です。
  * **C++ によるデスクトップ開発**
  * **ユニバーサル Windows プラットフォーム (UWP) の開発**
* Visual Studio 用の Windows Mixed Reality アプリ テンプレート [(ダウンロード)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX)。

## <a name="create-a-new-holographic-app-sample"></a>新しいホログラフィック アプリ サンプルの作成

最初の手順では、Remote Rendering 統合の基礎となるストック サンプルを作成します。 Visual Studio を開いて [新しいプロジェクトの作成] を選択し、"ホログラフィック DirectX 11 アプリ (ユニバーサル Windows) (C++/WinRT)" を検索します。

![新しいプロジェクトの作成](media/new-project-wizard.png)

目的のプロジェクト名を入力し、パスを選択して [作成] ボタンを選択します。
新しいプロジェクトで、構成を **"デバッグ/ARM64"** に切り替えます。 これで、コンパイルして接続済み HoloLens 2 デバイスにデプロイできるようになりました。 これを HoloLens で実行すると、回転するキューブが目前に表示されます。

## <a name="add-remote-rendering-dependencies-through-nuget"></a>NuGet を通じた Remote Rendering 依存関係の追加

Remote Rendering 機能を追加する最初の手順は、クライアント側の依存関係の追加です。 関連する依存関係は、NuGet パッケージとして入手できます。
ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューから **[NuGet パッケージの管理]** を選択します。

表示されるダイアログで、 **"Microsoft.Azure.RemoteRendering.Cpp"** という名前の NuGet パッケージを参照します。

![NuGet パッケージの参照](media/add-nuget.png)

パッケージを選択してプロジェクトに追加し、[インストール] ボタンを押します。

NuGet パッケージによって、Remote Rendering の依存関係がプロジェクトに追加されます。 具体的な内容は次のとおりです。
* クライアント ライブラリ (RemoteRenderingClient.lib) に対してリンクします。
* .dll 依存関係を設定します。
* 正しいパスをインクルード ディレクトリに設定します。

## <a name="project-preparation"></a>プロジェクトの準備

既存のプロジェクトにわずかに変更を加える必要があります。 小さな変更ではありますが、これらを行わないと Remote Rendering は機能しません。

### <a name="enable-multithread-protection-on-directx-device"></a>DirectX デバイスでマルチスレッド保護を有効にする
`DirectX11` デバイスでは、マルチスレッド保護が有効になっている必要があります。 これを変更するには、"Common" フォルダーの DeviceResources.cpp ファイルを開いて、次のコードを関数 `DeviceResources::CreateDeviceResources()` の末尾に挿入します。

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>アプリ マニフェストでネットワーク機能を有効にする
デプロイしたアプリに対してネットワーク機能を明示的に有効にする必要があります。 これを構成していないと、接続クエリは最終的にタイムアウトになります。 有効にするには、ソリューション エクスプローラーで `package.appxmanifest` 項目をダブルクリックします。 次の UI で、 **[機能]** タブに移動して以下を選択します。
* インターネット (クライアントとサーバー)
* インターネット (クライアント)

![ネットワーク機能](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Remote Rendering の統合

プロジェクトの準備ができたところで、コーディングを開始できます。 アプリケーションへのエントリ ポイントは、クラス `HolographicAppMain` (HolographicAppMain.h/cpp ファイル) にすることをお勧めします。ここには、初期化、初期化解除、レンダリングに必要なすべてのフックが含まれているためです。

### <a name="includes"></a>Includes

まず、必要なインクルードを追加します。 HolographicAppMain.h ファイルに次のインクルードを追加します。

```cpp
#include <AzureRemoteRendering.h>
```

また、これらの追加の `include` ディレクティブを HolographicAppMain.cpp ファイルに追加します。

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

コードをわかりやすくするために、HolographicAppMain.h ファイルの冒頭 (`include` ディレクティブの後ろ) で次の名前空間ショートカットを定義します。

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

このショートカットは便利なので、すべての場所で完全な名前空間を記述する必要はありませんが、ARR 固有のデータ構造を認識することができます。 もちろん、`using namespace...` ディレクティブを使用することもできます。

### <a name="remote-rendering-initialization"></a>Remote Rendering の初期化
 
アプリケーションの有効期間中に、セッションなどのためにいくつかのオブジェクトを保持する必要があります。 この有効期間はアプリケーションの `HolographicAppMain` オブジェクトの有効期間と一致するため、オブジェクトをメンバーとしてクラス `HolographicAppMain` に追加します。 次の手順では、次のクラス メンバーを HolographicAppMain.h ファイルに追加します。

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

実際の実装を行うのに適した場所は、クラス `HolographicAppMain` のコンストラクターです。 ここで 3 種類の初期化を行う必要があります。
1. Remote Rendering システムの 1 回限りの初期化
1. フロントエンドの作成
1. セッションの作成

これらすべてをコンストラクター内で順番に実行します。 ただし、実際のユース ケースでは、これらの手順を別々に実行することが適切な場合があります。

HolographicAppMain.cpp ファイルのコンストラクター本体の冒頭に次のコードを追加します。

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // create a new session
            RR::RenderingSessionCreationParams init;
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->GetStatus() == RR::Result::Success)
                    {
                        SetNewSession(handler->GetResult());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

このコードでは、メンバー関数 `SetNewSession` と `SetNewState` が呼び出されます。これらは、次の段落で、残りのステート マシン コード部分と共に実装されます。

資格情報はサンプル内にハードコーディングされており、所定の場所に入力する必要があることにご注意ください ([アカウント ID、アカウント キー](../../../how-tos/create-an-account.md#retrieve-the-account-information)、[ドメイン](../../../reference/regions.md))。

初期化解除は対称的に、デストラクター本体の末尾で逆の順番で実行します。

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>ステート マシン

Remote Rendering では、セッションの作成とモデルの読み込みを行う主要な関数は、非同期関数です。 これに対応するには、基本的に以下の状態が自動で切り替わる単純なステート マシンが必要です。

"*初期化 -> セッションの作成 -> セッションの開始 -> モデルの読み込み (および進行状況)* "

それに応じて、次の手順では、ちょっとしたステート マシン処理をクラスに追加します。 アプリケーションが取れるさまざまな状態に対して、独自の列挙型 `AppConnectionStatus` を宣言します。 これは `RR::ConnectionStatus` と類似していますが、失敗した接続用の追加の状態を備えています。

次のメンバーと関数をクラス宣言に追加します。

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::AzureSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

.cpp ファイルの実装側で、これらの関数本体を追加します。

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = async->GetStatus();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::AzureSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Actions();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>フレームごとの更新

シミュレーション ティックごとにクライアントを更新し、追加の状態の更新を実行する必要があります。 関数 `HolographicAppMain::Update` には、フレームごとの更新に適したフックが用意されています。

#### <a name="state-machine-update"></a>ステート マシンの更新

セッションの状態をポーリングし、それが `Ready` 状態に遷移したかどうかを確認する必要があります。 正常に接続した場合は、最後に `StartModelLoading` を介してモデルの読み込みを開始します。

関数 `HolographicAppMain::Update` の本体に次のコードを追加します。

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                if (auto propAsync = m_session->GetPropertiesAsync())
                {
                    m_sessionPropertiesAsync = *propAsync;
                    m_sessionPropertiesAsync->Completed([this](const RR::ApiHandle<RR::SessionPropertiesAsync>& async)
                        {
                            if (async->GetStatus() == RR::Result::Success)
                            {
                                auto res = async->GetResult();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::ConnectToRuntimeParams init;
                                    init.ignoreCertificateValidation = false;
                                    init.mode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectToRuntime(init);
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
    
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                            }
                            m_sessionPropertiesAsync = nullptr; // next try
                            m_needsStatusUpdate = true;
                        });
                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>座標系の更新

使用する座標系については、レンダリング サービスに同意する必要があります。 使用する座標系にアクセスするには、関数 `HolographicAppMain::OnHolographicDisplayIsAvailableChanged` の末尾で作成される `m_stationaryReferenceFrame` が必要です。

通常、この座標系は変更されないため、これは 1 回限りの初期化となります。 アプリケーションによって座標系が変更された場合は、再度呼び出す必要があります。

双方が参照座標系および接続済みセッションを取得するとすぐに、上記のコードにより `Update` 関数内で座標系が一度設定されます。

#### <a name="camera-update"></a>カメラの更新

サーバー カメラとローカル カメラの同期が維持されるように、カメラのクリップ面を更新する必要があります。 これは `Update` 関数の最末尾で行うことができます。

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>表示

最後の手順では、リモート コンテンツのレンダリングを呼び出します。 この呼び出しは、レンダー ターゲット クリアおよびビューポートの設定の後で、レンダリング パイプライン内の厳密に適切な位置で行う必要があります。 次のスニペットを `HolographicAppMain::Render` 関数内の `UseHolographicCameraResources` ロックに挿入します。

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Exiting check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>サンプルを実行する

これでサンプルは、コンパイルと実行を行える状態になりました。

サンプルが正常に動作すると、回転するキューブが目前に表示され、セッションの作成とモデルの読み込みが何回か行われた後に、現在のヘッド位置にあるエンジン モデルがレンダリングされます。 セッションの作成とモデルの読み込みには、最大で数分かかる場合があります。 現在の状態は、Visual Studio の出力パネルにのみ書き込まれます。 そのため、Visual Studio 内からサンプルを開始することをお勧めします。

> [!CAUTION]
> ティック関数が数秒間呼び出されないと、クライアントはサーバーから切断されます。 そのため、ブレークポイントをトリガーすると、アプリケーションを非常に簡単に切断させることができます。

適切な状態をテキスト パネルに表示させるには、GitHub にある、このチュートリアルの既存のバージョンを参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Remote Rendering をストック **ホログラフィック アプリ** C++/DirectX11 サンプルに追加するために必要なすべての手順を説明しました。
ご自分のモデルを変換するには、次のクイックスタートを参照してください。

> [!div class="nextstepaction"]
> [クイック スタート: モデルをレンダリング用に変換する](../../../quickstarts/convert-model.md)
