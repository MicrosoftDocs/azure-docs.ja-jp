---
title: Remote Rendering のセッション
description: Remote Rendering セッションの概要について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 79f3f93338d15562dcc37857d63bc8b2d7e96b05
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530554"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering のセッション

Azure Remote Rendering (ARR) では、"*セッション*" は重要な概念です。 この記事では、セッションとは具体的に何かについて説明します。

## <a name="overview"></a>概要

Azure Remote Rendering は、複雑なレンダリング タスクをクラウドにオフロードすることによって機能します。 ほとんどのクラウド サーバーには GPU が搭載されていないため、これらのレンダリング タスクはどのサーバーでも満たすことはできません。 使用されるデータ量と、対話的なフレーム レートで結果を生成するためのハード要件により、より一般的な Web トラフィックでも起こり得る、どのユーザー要求をどのサーバーで処理するかという責任も、その場で別のコンピューターに渡すことはできません。

つまり、Azure Remote Rendering を使用する場合、レンダリング要求を処理するために、必要なハードウェア機能を備えたクラウド サーバーを排他的に予約する必要があります。 "*セッション*" は、このサーバーとの対話に関係するすべてのものを指します。 まず、使用するマシンを予約 ("*リース*") する最初の要求から始まり、続いてモデルを読み込んで操作するためのすべてのコマンド、最後はクラウド サーバー上のリースの解放で終了します。

## <a name="managing-sessions"></a>セッションの管理

セッションを管理および操作する方法は複数あります。 [セッション管理 REST API](../how-tos/session-rest-api.md) を使用して、言語に依存しない方法でセッションを作成、更新、およびシャットダウンできます。 C# および C++ では、これらの操作は `RemoteRenderingClient` と `RenderingSession` のクラスを通じて公開されます。 Unity アプリケーションの場合は、`ARRServiceUnity` コンポーネントによって提供されるその他のユーティリティ関数があります。

アクティブなセッションに "*接続*" されると、[モデルの読み込み](models.md)やシーンとの対話といった操作は、`RenderingSession` クラスを介して公開されます。

### <a name="managing-multiple-sessions-simultaneously"></a>複数のセッションの同時管理

1 つのデバイスから複数のセッションに完全に "*接続*" することはできません。 ただし、1 つのアプリケーションから任意の数のセッションを作成、監視、およびシャットダウンすることはできます。 セッションに接続することを意図していない限り、アプリを HoloLens 2 のようなデバイスで実行する必要もありません。 このような実装のユース ケースは、中央のメカニズムを使用してセッションを制御したい場合が考えられます。 たとえば、複数のタブレットと HoloLenses デバイスでログインできる Web アプリを作成できます。 次に、そのアプリで、表示する CAD モデルなどのオプションをタブレットに表示できます。 ユーザーが選択すると、この情報はすべての HoloLenses デバイスに伝達され、共有エクスペリエンスが作成されます。

## <a name="session-phases"></a>セッション フェーズ

すべてのセッションが複数のフェーズを経ます。

### <a name="session-startup"></a>セッションの開始

ARR に[新しいセッションの作成](../how-tos/session-rest-api.md)を要求すると、最初にセッション [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) が返されます。 この UUID を使用すると、セッションに関する情報を照会できます。 UUID とセッションに関するいくつかの基本的な情報は 30 日間保持されるため、セッションが停止した後でも、その情報を照会することができます。 この時点で、**セッション状態** は **Starting** として報告されます。

次に、Azure Remote Rendering が、セッションをホストできるサーバーを見つけようとします。 この検索には、2 つのパラメーターがあります。 1 つ目は、ご利用の[リージョン](../reference/regions.md)内のサーバーのみを予約します。 これは、リージョン間のネットワーク待機時間が長すぎて、適切なエクスペリエンスを保証できない場合があるためです。 2 つ目の要素は、指定した必要な "*サイズ*" です。 各リージョンでは、[*Standard*](../reference/vm-sizes.md) または [*Premium*](../reference/vm-sizes.md) のサイズ要求を満たすことができるサーバーの数に制限があります。 その結果、要求されたサイズのすべてのサーバーがご利用のリージョン内で現在使用されている場合、セッションの作成は失敗します。 失敗の原因は[照会できます](../how-tos/session-rest-api.md)。

> [!IMPORTANT]
> *Standard* サーバーのサイズを要求したところ、高需要という理由でその要求が失敗したとしても、*Premium* サーバーの要求も失敗することを意味するわけではありません。 そのため、このような選択肢がある場合は、*Premium* サーバーのサイズへのフォール バックを試すことができます。

サービスは、適切なサーバーを見つけると、適切な仮想マシン (VM) をそのサーバーにコピーして、Azure Remote Rendering ホストにする必要があります。 このプロセスには数分かかります。 その後、VM が起動し、**セッション状態** が **Ready** に移行します。

この時点で、サーバーは入力を排他的に待機しています。 これは、サービスに対して課金されるポイントでもあります。

### <a name="connecting-to-a-session"></a>セッションへの接続

セッションが *ready* になったら、そのセッションに "*接続*" できます。 接続されている間、デバイスは、モデルを読み込んで変更するためのコマンドを送信できます。 すべての ARR ホストは一度に 1 つのクライアント デバイスにしかサービスを提供しません。そのため、クライアントがセッションに接続すると、レンダリングされたコンテンツが排他的に制御されます。 これは、自分では制御できない理由でレンダリングのパフォーマンスが変化することはないことも意味します。

> [!IMPORTANT]
> セッションに "*接続*" できるのは 1 つのクライアントだけですが、セッションに関する基本情報 (現在の状態など) は、接続しなくても照会することができます。

デバイスがセッションに接続されている間に、他のデバイスが接続しようとすると失敗します。 ただし、接続されているデバイスが自発的に、または何らかの障害によって切断された場合、セッションは別の接続要求を受け入れます。 以前の状態 (読み込まれたモデルなど) は、次に接続しているデバイスが白紙の状態から始められるように、すべて破棄されます。 したがって、セッションはデバイスごとに何度も再利用することができ、ほとんどの場合、セッション開始のオーバーヘッドをエンド ユーザーから隠すことができます。

> [!IMPORTANT]
> リモート サーバーによってクライアント側のデータの状態が変更されることはありません。 データのすべての変更 (変換の更新や読み込み要求など) は、クライアント アプリケーションで実行する必要があります。 すべてのアクションは、クライアントの状態を直ちに更新します。

### <a name="session-end"></a>セッションの終了

新しいセッションを要求するときは、"*最大リース時間*" を、通常は 1 - 8 時間の範囲で指定します。 これが、ホストが入力を受け入れる期間になります。

セッションが終了するには、2 つの正常な理由があります。 セッションを停止するように手動で要求するか、最大リース時間の有効期限が切れるかのいずれかです。 どちらの場合も、ホストへのアクティブな接続はすぐに切断され、そのサーバーでのサービスがシャットダウンされます。 その後、サーバーは、Azure プールに戻され、他の目的のために要求される場合があります。 セッションの停止は、元に戻したり、取り消したりすることはできません。 停止したセッションで **セッション状態** を照会すると、セッションが手動でシャットダウンされたか、最大リース時間に達したかによって、**Stopped** または **Expired** が返されます。

セッションは、何らかの障害によって停止することもあります。

どのような場合でも、セッションが停止されると、それ以上課金されることはありません。

> [!WARNING]
> セッションに接続しているかどうか、およびその期間に関係なく、課金には影響しません。 サービスの料金は、"*セッションの期間*"、つまり、サーバーが排他的に予約されている時間、および要求されているハードウェア機能 ([割り当てられたサイズ](../reference/vm-sizes.md)) によって異なります。 セッションを開始して、5 分間接続してから、リースが期限切れになるまでセッションを停止せずに実行し続けた場合、セッションのリース時間全体に対して課金されます。 逆に言うと、"*最大リース時間*" は、ほとんどの場合、セーフティ ネットです。 リース時間が 8 時間のセッションを要求するかどうかに関係なく、後でセッションを手動で停止した場合は、5 分間に対してのみ使用されます。

#### <a name="extend-a-sessions-lease-time"></a>セッションのリース時間を延長する

アクティブ セッションの[リース時間の延長](../how-tos/session-rest-api.md)が必要になった場合は、延長することができます。

## <a name="example-code"></a>コード例

次のコードは、セッションを開始し、*ready* 状態を待機し、接続してからもう一度切断およびシャットダウンするという簡単な実装を示しています。

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

SessionConfiguration sessionConfig = new SessionConfiguration();
// fill out sessionConfig details...

RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

RenderingSessionCreationOptions rendererOptions = new RenderingSessionCreationOptions();
// fill out rendererOptions...

CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(rendererOptions);

RenderingSession session = result.Session;
RenderingSessionProperties sessionProperties;
while (true)
{
    var propertiesResult = await session.GetPropertiesAsync();
    sessionProperties = propertiesResult.SessionProperties;
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
ConnectionStatus connectStatus = await session.ConnectAsync(new RendererInitOptions());

// Connected!

while (...)
{
    // per frame update

    session.Connection.Update();
}

// Disconnect
session.Disconnect();

// stop the session
await session.StopAsync();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

`RemoteRenderingClient` と `RenderingSession` の複数のインスタンスは、コードから保持、操作、および照会できます。 ただし、`RenderingSession` に接続できるデバイスは、一度に 1 つだけです。

仮想マシンの有効期間は、`RemoteRenderingClient` インスタンスまたは `RenderingSession` インスタンスに関連付けられていません。 セッションを停止するには、`RenderingSession.StopAsync` を呼び出す必要があります。

永続的なセッション ID は、`RenderingSession.SessionUuid()` を使用して照会し、ローカルにキャッシュできます。 アプリケーションでこの ID を使用して `RemoteRenderingClient.OpenRenderingSessionAsync` を呼び出し、そのセッションにバインドできます。

`RenderingSession.IsConnected` が true の場合、`RenderingSession.Connection` は `RenderingConnection` のインスタンスを返します。このインスタンスには、[モデルの読み込み](models.md)、[エンティティ](entities.md)の操作、およびレンダリングされたシーンに関する[情報の照会](../overview/features/spatial-queries.md)を行う関数が含まれています。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingSession クラス](/dotnet/api/microsoft.azure.remoterendering.renderingsession)
* [C# RemoteRenderingClient.CreateNewRenderingSessionAsync()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.createnewrenderingsessionasync)
* [C# RemoteRenderingClient.OpenRenderingSessionAsync()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.openrenderingsessionasync)
* [C++ RenderingSession クラス](/cpp/api/remote-rendering/renderingsession)
* [C++ RemoteRenderingClient::CreateNewRenderingSessionAsync](/cpp/api/remote-rendering/remoterenderingclient#createnewrenderingsessionasync)
* [C++ RemoteRenderingClient::OpenRenderingSession](/cpp/api/remote-rendering/remoterenderingclient#openrenderingsession)

## <a name="next-steps"></a>次のステップ

* [エンティティ](entities.md)
* [モデル](models.md)