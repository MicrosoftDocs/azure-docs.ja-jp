---
title: アプリ サーバーを正常に停止します。
description: この記事では、SignalR アプリ サーバーを正常にシャットダウンする方法について説明します。
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201673"
---
# <a name="server-graceful-shutdown"></a>サーバーのグレースフル シャットダウン
Microsoft Azure SignalR Service には、サーバーを正常にシャットダウンするためのモードが 2 つあります。 

この機能を利用する主要な利点は、予想外の接続の切断を顧客に体験させないことにあります。 

その代わり、ビジネス ロジックとの関連でクライアント接続が自動的に閉じられるのを待ちます。あるいは、データを失うことなく、別のサーバーにクライアント接続を移行することもできます。 

## <a name="how-it-works"></a>しくみ

通常、グレースフル シャットダウン プロセスには、次の 4 つの段階があります。

1. **サーバーをオフラインにする**

    このサーバーにルート指定されるクライアント接続がなくなることを意味します。

2. **`OnShutdown` フックをトリガーする**

    サーバーに所有しているハブごとにシャットダウン フックを登録できます。
    それらは、Azure SignalR Service でこのサーバーがオフラインに設定されたことを意味する **FINACK** 応答が Azure SignalR Service から届いた直後に、登録されている順序で呼び出されます。

    この段階では、メッセージの配信や消去作業などを実行できます。すべてのシャットダウン フックが実行されると、次の段階に進みます。

3. **すべてのクライアント接続が完了するまで待つ**。選択したモードに依存します。次があります。

    **WaitForClientsToClose に設定されたモード**

    Azure SignalR Service には既存のクライアントが保持されます。

    場合によっては、終了メッセージをすべてのクライアントに配信するなど、手段を設計し、自動的に閉じるか再接続するタイミングをクライアントに決定させる必要があります。

    使用例については [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) をお読みください。このサンプルでは、シャットダウン フックでクライアント終了をトリガーする "exit" メッセージを配信します。

    **MigrateClients に設定されたモード**

    Azure SignalR Service では、このサーバーでのクライアント接続のルート指定を別の有効なサーバーに変更することが試行されます。 
    
    このシナリオでは、`OnConnectedAsync` と `OnDisconnectedAsync` が新しいサーバーと古いサーバーでそれぞれトリガーされます。`IConnectionMigrationFeature` が `HttpContext` に設定されていますが、これはクライアント接続が移行で入ってきたのか、出て行ったのかを特定する目的で利用できます。ステートフル シナリオで特に便利です。

    クライアント接続は、現在のメッセージが配信された直後に、つまり、次のメッセージが新しいサーバーにルート指定されるタイミングで移行されます。

4. **サーバー接続を停止する**

    すべてのクライアント接続が閉じられるか、移行されると、あるいはタイムアウト時間 (既定で 30 秒) を経過すると、

    SignalR Server SDK のシャットダウン プロセスがこの段階に進み、すべてのクライアント接続が閉じられます。

    閉じられなかったり、移行されなかったりした場合でも、クライアント接続は切断されます。 たとえば、適切なターゲット サーバーまたは、クライアントとサーバーの間の、現在のメッセージが完了していません。

## <a name="sample-codes"></a>サンプル コード。

`AddAzureSignalR` の場合は次のオプションを追加します。

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>グレースフル シャットダウン モードが `MigrateClients` に設定されているとき、`OnConnected` と `OnDisconnected` を構成します。

クライアント接続が移行で入ってきたのか、出て行ったのかを示す "IConnectionMigrationFeature" を導入しました。

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```