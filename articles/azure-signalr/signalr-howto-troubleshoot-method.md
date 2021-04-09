---
title: Azure SignalR Service のトラブルシューティングの実践
description: 接続とメッセージ配信に関する問題のトラブルシューティング方法について説明します
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707658"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>接続とメッセージ配信に関する問題のトラブルシューティング方法

このガイダンスでは、自己診断を行って、根本原因を直接見つけたり、問題を絞り込んだりするのに役立つ方法をいくつか紹介します。 自己診断の結果は、より詳細な調査のために Microsoft に報告する際にも役立ちます。

最初に、Azure portal から、Azure SignalR Service (**ASRS**) がどの [ServiceMode](./concept-service-mode.md) に構成されているかを確認する必要があります。

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* `Default` モードの場合は、「[既定モードのトラブルシューティング](#default_mode_tsg)」を参照してください

* `Serverless` モードの場合は、「[サーバーレス モードのトラブルシューティング](#serverless_mode_tsg)」を参照してください

* `Classic` モードの場合は、「[クラシック モードのトラブルシューティング](#classic_mode_tsg)」を参照してください

<a name="default_mode_tsg"></a>

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>既定モードのトラブルシューティング

**ASRS** が "*既定*" モードになっている場合は、**3 つ** のロールがあります。"*クライアント*"、"*サーバー*"、"*サービス*" です。

* *クライアント*: "*クライアント*" は、**ASRS** に接続されているクライアントを表します。 クライアントと **ASRS** を接続する永続的な接続は、このガイダンスでは *クライアント接続* と呼びます。

* *[サーバー]* :"*サーバー*" は、クライアント ネゴシエーションを提供し、SignalR の `Hub` ロジックをホストするサーバーを表します。 そして、"*サーバー*" と **ASRS** の間の永続的な接続は、このガイダンスでは "*サーバー接続*" と呼びます。

* *サービス*:"*サービス*" は、このガイダンスでの **ASRS** の短い名前です。

アーキテクチャとワークフローの全体の詳しい紹介については、「[Azure SignalR Service の内部構造](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)」を参照してください。

問題の絞り込みに役立つ方法はいくつかあります。 

* 問題がまさに発生中であったり、再現可能であったりする場合、単純な方法は、動作中のトラフィックを表示することです。 

* 問題を再現するのが困難な場合は、トレースとログが役立つことがあります。

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>トラフィックを表示して問題を絞り込む方法

動作中のトラフィックをキャプチャすることは、問題を絞り込むための最も単純な方法です。 以下で説明しているオプションを使用して、[ネットワーク トレース](/aspnet/core/signalr/diagnostics#network-traces)をキャプチャできます。

* [Fiddler でネットワーク トレースを収集する](/aspnet/core/signalr/diagnostics#network-traces)

* [tcpdump でネットワーク トレースを収集する](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [ブラウザーでネットワーク トレースを収集する](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>クライアント要求

SignalR の永続的な接続の場合は、まず、ホストされているアプリ サーバーに対して `/negotiate` を行い、次に、Azure SignalR サービスにリダイレクトしてから、Azure SignalR サービスへの永続的な接続を実際に確立します。 詳細な手順については、「[Azure SignalR Service の内部構造](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)」を参照してください。

クライアント側のネットワーク トレースを入手したら、どの要求がどのような状態コードと応答で失敗しているかを調べて、[トラブルシューティング ガイド](./signalr-howto-troubleshoot-guide.md)内で解決策を探します。

#### <a name="server-requests"></a>サーバー要求

SignalR *Server* では、"*サーバー*" と "*サービス*" の間の "*サーバー接続*" を管理します。 アプリ サーバーが起動すると、Azure SignalR サービスへの **WebSocket** 接続が開始されます。 すべてのクライアント トラフィックは、Azure SignalR サービスを経由してこれらの "*サーバー接続*" にルーティングされてから、`Hub` にディスパッチされます。 "*サーバー接続*" が切断されると、この "*サーバー接続*" にルーティングされるクライアントが影響を受けます。 Azure SignalR SDK には、影響を最小限に抑えるため、最大 1 分の遅延で "*サーバー接続*" を再接続する、"常に再試行する" ロジックが備わっています。

Azure SignalR Service のネットワークが不安定であるかその定期的メンテナンスのため、またはホストされているアプリ サーバーの更新やメンテナンスのために、"*サーバー接続*" が切断される場合があります。 クライアント側に切断/再接続メカニズムがある限り、クライアント側で発生した切断/再接続と同様に、影響は最小限になります。

サーバー側のネットワーク トレースを表示して、"*サーバー接続*" が切断された理由や、*サービス* に拒否された理由について、状態コードとエラーの詳細を確認します。そして、[トラブルシューティング ガイド](./signalr-howto-troubleshoot-guide.md)で根本原因を調べます。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>ログを追加する方法

ログは、問題を診断し、実行状態を監視するのに役立つ場合があります。

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>クライアント側のログを有効にする方法

クライアント側のログ記録エクスペリエンスは、セルフホステッド SignalR を使用する場合とまったく同じです。

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>`ASP.NET Core SignalR` に対するクライアント側のログ記録を有効にする

* [JavaScript クライアントのログ記録](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET クライアントのログ記録](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>`ASP.NET SignalR` に対するクライアント側のログ記録を有効にする

* [.NET クライアント](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Windows Phone 8 クライアントでのトレースの有効化](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [JavaScript クライアントでのトレースの有効化](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>サーバー側のログを有効にする方法

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>`ASP.NET Core SignalR` に対するサーバー側のログ記録を有効にする

`ASP.NET Core SignalR` のためのサーバー側ログ記録は、`ASP.NET Core` フレームワークで提供されている `ILogger` ベースの[ログ記録](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1)と統合されています。 サーバー側のログ記録は、`ConfigureLogging` を使用して有効にできます。以下に使用例を示します。

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR のロガー カテゴリは常に `Microsoft.Azure.SignalR` で始まります。 Azure SignalR からの詳細なログを有効にするには、**appsettings.json** ファイルで次のように、先行するプレフィックスを `Information` レベルに構成します。

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

通常とは異なる警告やエラーのログが記録されているかどうかを確認します。 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>`ASP.NET SignalR` に対するサーバー側のトレースを有効にする

バージョンが `1.0.0` 以上の SDK を使用する場合、`web.config` に以下を追加すればトレースを有効にできます。([詳細](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

通常とは異なる警告やエラーのログが記録されているかどうかを確認します。 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Azure SignalR サービス内でログを有効にする方法

Azure SignalR サービスに対して[診断ログを有効にする](./signalr-howto-diagnostic-logs.md)こともできます。これらのログでは、Azure SignalR サービスに接続されているすべての接続の詳細情報が提供されます。

<a name="serverless_mode_tsg"></a>

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>サーバーレス モードのトラブルシューティング

**ASRS** が "*サーバーレス*" モードになっている場合、**ASP.NET Core SignalR** では `Serverless` モードのみがサポートされ、**ASP.NET SignalR** ではこのモードはサポート **されません**。

`Serverless` モードでの接続の問題を診断する場合、最も簡単な方法は、[クライアント側のトラフィックを表示する](#view_traffic_client)ことです。 [クライアント側のログ](#add_logs_client)と[サービス側のログ](#add_logs_server)を有効にすることも役立つ場合があります。

<a name="classic_mode_tsg"></a>

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>クラシック モードのトラブルシューティング

`Classic` モードは古くなっており、使用することはお勧めできません。 このモードでは、Azure SignalR サービスは接続された "*サーバー接続*" を使用して、現在のサービスが `default` モードと `serverless` モードのどちらになっているかを判断します。 ネットワークの不安定性などの理由で、接続されているすべての "*サーバー接続*" が突然切断されると、Azure SignalR では `serverless` モードに切り替えられたと認識されて、この期間中に接続されたクライアントは、ホストされていたアプリ サーバーにルーティングされなくなるため、これが中間クライアント接続の問題につながる可能性があります。 [サービス側のログ](#add_logs_server)を有効にして、ホストされているアプリ サーバーがあっても、一部のクライアントがアプリ サーバー側に到達しない場合は、`ServerlessModeEntered` として記録されたクライアントがあるかどうかを確認します。 存在する場合は、[これらのクライアント接続を中止](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API)して、クライアントを再起動させると役立つことがあります。

`classic` モードの接続とメッセージ配信に関する問題のトラブルシューティングは、[既定モードの問題のトラブルシューティング](#default_mode_tsg)に似ています。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>サービス正常性

サービス正常性のための正常性 API を確認できます。

* 要求:GET `https://{instance_name}.service.signalr.net/api/v1/health`

* 応答の状態コード:
  * 200: 正常。
  * 503: サービスが異常です。 次のようにすることができます。
    * 自動復旧するのを数分待ちます。
    * IP アドレスがポータルの IP アドレスと同じであることを確認します。
    * または、インスタンスを再起動します。
    * 上記のすべての選択肢が機能しない場合は、Azure portal で新しいサポート要求を追加し、Microsoft までお問い合わせください。

[ディザスター リカバリー](./signalr-concept-disaster-recovery.md)の詳細を確認してください。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>次のステップ

このガイドでは、接続とメッセージ配信に関する問題のトラブルシューティング方法について学習しました。 一般的な問題を処理する方法についても学習できました。 

> [!div class="nextstepaction"]
> [トラブルシューティング ガイド](./signalr-howto-troubleshoot-guide.md)