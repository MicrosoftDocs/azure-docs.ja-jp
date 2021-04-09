---
title: Unity 向けに Remote Rendering を設定する
description: Unity プロジェクトで Azure Remote Rendering を初期化する方法
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594216"
---
# <a name="set-up-remote-rendering-for-unity"></a>Unity 向けに Remote Rendering を設定する

Unity で Azure Remote Rendering (ARR) を有効にするために、Unity 固有のいくつかの側面を処理する専用のメソッドを提供しています。

## <a name="startup-and-shutdown"></a>起動とシャットダウン

Remote Rendering を初期化するには、`RemoteManagerUnity` を使用します。 このクラスは、ジェネリック `RenderingConnection` を呼び出しますが、Unity 固有の詳細を既に実装しています。 たとえば、Unity は特定の座標系を使用します。 `RemoteManagerUnity.Initialize` を呼び出すと、適切な規則が設定されます。 この呼び出しでは、リモートでレンダリングされるコンテンツを表示するために使用する必要がある Unity カメラを指定する必要もあります。

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Remote Rendering をシャットダウンするには、`RemoteManagerStatic.ShutdownRemoteRendering()` を呼び出します。

`RenderingSession` が作成され、プライマリ レンダリング セッションとして選択された後、`RemoteManagerUnity` に登録する必要があります。

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>完全なコード例

次のコードは、Unity で Azure Remote Rendering を初期化するために必要なすべての手順を示しています。

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>便利な関数

### <a name="session-state-events"></a>セッション状態イベント

`RemoteManagerUnity.OnSessionUpdate` は、そのセッション状態が変化した際のイベントを出力します。詳細については、コード ドキュメントを参照してください。

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` は、セットアップとセッション管理を効率化するためのオプションのコンポーネントです。 これには、アプリケーションが終了したとき、またはエディターで再生モードが終了したときにセッションを自動的に停止するオプションが含まれています。また、必要に応じてセッションのリースを自動的に更新することもできます。 セッション プロパティ (`LastProperties` 変数を参照) などのデータをキャッシュし、セッション状態の変更およびセッション エラーのイベントを公開します。

同時に複数の `ARRServiceUnity` のインスタンスは存在できません。 これは、いくつかの一般的な機能を実装することによって、すばやく作業を開始することを意図しています。 ただし、大規模なアプリケーションの場合は、これらの操作を自分で行うことをお勧めします。

`ARRServiceUnity` を設定して使用する方法の例については、[チュートリアル:リモートでレンダリングされたモデルの表示](../../tutorials/unity/view-remote-models/view-remote-models.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Unity 用の Remote Rendering パッケージをインストールする](install-remote-rendering-unity-package.md)
* [チュートリアル:リモートでレンダリングされたモデルの表示](../../tutorials/unity/view-remote-models/view-remote-models.md)
