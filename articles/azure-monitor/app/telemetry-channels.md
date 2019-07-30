---
title: Azure Application Insights におけるテレメトリ チャネル | Microsoft Docs
description: .NET および .NET Core 用の Azure Application Insights SDK でテレメトリ チャネルをカスタマイズする方法について説明します。
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561360"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights のテレメトリ チャネル

テレメトリ チャネルは、[Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md) の不可欠な要素です。 これにより、テレメトリのバッファー処理と Application Insights サービスへの送信が管理されます。 この SDK の .NET バージョンと .NET Core バージョンには、`InMemoryChannel` と `ServerTelemetryChannel` の 2 つのテレメトリ チャネルが組み込まれています。 この記事では、チャネルの動作をカスタマイズする方法も含め、各チャネルの詳細を説明します。

## <a name="what-are-telemetry-channels"></a>テレメトリ チャネルとは

テレメトリ チャネルは、テレメトリ項目をバッファー処理し、Application Insights サービスに送信する役割を担うものです (テレメトリ項目は、クエリと解析のために Application Insights サービスに保存されます)。 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) インターフェイスを実装するクラスはいずれも、テレメトリ チャネルです。

テレメトリ チャネルの `Send(ITelemetry item)` メソッドは、テレメトリ初期化子とテレメトリ プロセッサがすべて呼び出された後に呼び出されます。 つまり、テレメトリ プロセッサによって削除された項目はチャネルに到達しません。 通常であれば、`Send()` が項目をすぐにバックエンドに送信することはありません。 通常はメモリ内でバッファー処理されてから、バッチ単位で効率的に送信されます。

このほか [Live Metrics Stream](live-stream.md) にも、テレメトリのライブ ストリーミングに役立つカスタム チャネルがあります。 このチャネルは通常のテレメトリ チャネルからは独立しているので、このドキュメントの内容が該当しません。

## <a name="built-in-telemetry-channels"></a>あらかじめ組み込まれているテレメトリ チャネル

Application Insights の .NET SDK と .NET Core SDK には、2 つのチャネルがあらかじめ組み込まれています。

* `InMemoryChannel`:項目が送信されるまでの間、項目をメモリ内でバッファー処理する軽量のチャネルです。 項目はメモリ内でバッファー処理され、30 秒ごとまたは 500 項目がバッファー処理されるごとにフラッシュされます。 このチャネルは、障害の後にテレメトリの送信を再試行しないので、保証される信頼性は最小限です。 また、このチャネルでは項目がディスク上に保持されないので、未送信の項目がある場合には、アプリケーションのシャットダウン時に (正常にシャットダウンした場合でも、そうでない場合でも) 完全に失われます。 このチャネルには、メモリ内のすべてのテレメトリ項目を同期的に強制フラッシュできる `Flush()` メソッドが実装されています。 このチャネルは、同期的にフラッシュすることが理想的な実行時間の短いアプリケーションに適しています。

    このチャネルは、これよりも大きな Microsoft.ApplicationInsights NuGet パッケージの一部であり、他に何も構成されていないときに SDK が使用する既定のチャネルです。

* `ServerTelemetryChannel`:再試行ポリシーとローカル ディスクへのデータ保存機能を備えたより高度なチャネルです。 このチャネルでは、一時的なエラーが発生すると、テレメトリの送信が再試行されます。 また、このチャネルは、ネットワーク障害時またはテレメトリが大量にある場合に、ローカル ディスク ストレージを使用してディスク上に項目を保持します。 このように再試行メカニズムとローカル ディスクへの保存機能を備えているため、このチャネルは信頼性が高いと考えられ、あらゆる運用環境のシナリオに推奨されています。 公式ドキュメントに従って構成した [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) および [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) アプリケーションでは、このチャネルが既定となっています。 このチャネルは、実行時間の長いプロセスがあるサーバーのシナリオに最適です。 このチャネルによって実装される [`Flush()`](#which-channel-should-i-use) メソッドは同期的ではありません。

    このチャネルは、Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet パッケージとして提供されるものであり、Microsoft.ApplicationInsights.Web または Microsoft.ApplicationInsights.AspNetCore NuGet パッケージを使用している場合には自動的に取得されます。

## <a name="configure-a-telemetry-channel"></a>テレメトリ チャネルの構成

テレメトリ チャネルを構成するには、テレメトリ チャネルをアクティブなテレメトリ構成に設定します。 ASP.NET アプリケーションの構成では、テレメトリ チャネル インスタンスを `TelemetryConfiguration.Active` に設定するか、`ApplicationInsights.config` を変更します。 ASP.NET Core アプリケーションの構成では、依存関係挿入コンテナーにチャネルを追加します。

以降のセクションでは、さまざまな種類のアプリケーションに存在するチャネルの `StorageFolder` 設定の構成例を示します。 `StorageFolder` は構成可能な設定の 1 つです。 構成設定の完全な一覧については、この記事の後の方にある[設定に関するセクション](telemetry-channels.md#configurable-settings-in-channels)を参照してください。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ApplicationInsights.config を使用した ASP.NET アプリケーションの構成

[ApplicationInsights.config](configuration-with-applicationinsights-config.md) の以下のセクションは、`StorageFolder` にカスタムの場所が構成された `ServerTelemetryChannel` チャネルを示しています。

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>コードによる ASP.NET アプリケーションの構成

次のコードでは、`StorageFolder` にカスタムの場所を設定した "ServerTelemetryChannel" インスタンスを設定しています。 このコードはアプリケーションの最初の方、通常は Global.aspx.cs の `Application_Start()` メソッドの中に追加します。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>コードによる ASP.NET Core アプリケーションの構成

`Startup.cs` クラスの `ConfigureServices` メソッドを次のように変更します。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> ASP.NET Core アプリケーションでは、`TelemetryConfiguration.Active` を使用してチャネルを構成することはお勧めしません。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>コードによる .NET および .NET Core コンソール アプリケーションの構成

コンソール アプリについては、.NET、.NET Core ともコードが同じです。

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel の操作の詳細

`ServerTelemetryChannel` では、到着した項目がメモリ内バッファーに保存されます。 項目は、シリアル化と圧縮処理の後、30 秒ごとまたは 500 項目がバッファー処理されるごとに `Transmission` インスタンスに格納されます。 1 つの `Transmission` インスタンスは最大 500 個の項目を保持することができ、Application Insights サービスに対する 1 回の HTTPS 呼び出しにより送信されるテレメトリのバッチを表しています。

既定では、最大 10 件の `Transmission` インスタンスを並列で送信できます。 これよりも早いペースでテレメトリが到着する場合や、ネットワークまたは Application Insights のバックエンドが低速な場合は、`Transmission` インスタンスがメモリ内に保存されます。 このメモリ内 `Transmission` バッファーの既定の容量は 5 MB です。 メモリ内の容量を超過した場合には、50 MB を上限としてローカル ディスクに `Transmission` インスタンスが保存されます。 このほか、ネットワークの問題がある場合にも、`Transmission` インスタンスがローカル ディスクに保存されます。 アプリケーションがクラッシュした場合に消えずに残るのは、ローカル ディスクに保存されている項目のみです。 これらは、アプリケーションを再起動した時点で送信されます。

## <a name="configurable-settings-in-channels"></a>チャネルの構成可能な設定

各チャネルの構成可能な設定の完全な一覧については、次を参照してください。

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

ここでは、`ServerTelemetryChannel` も設定のなかでも特によく使用するものを紹介します。

1. `MaxTransmissionBufferCapacity`:転送項目をメモリ内でバッファー処理するためにチャネルが使用するメモリ量の最大値 (バイト単位) です。 この容量に達すると、新しい項目がローカル ディスクに直接保存されます。 既定値は 5 MB です。 これよりも高い値を設定するとディスクの使用量を抑えられますが、アプリケーションがクラッシュした場合にメモリ内の項目が失われるという点には注意してください。

1. `MaxTransmissionSenderCapacity`:Application Insights に一括送信される `Transmission` インスタンスの最大数です。 既定値は 10 です。 生成されるテレメトリが膨大な量に及ぶ場合には、これよりも高い数値を設定することをお勧めします。 量が多くなるのは通常、ロード テストの最中や、サンプリングがオフになっている場合です。

1. `StorageFolder`:必要に応じてディスクに項目を保存するためにチャネルが使用するフォルダーです。 Windows では、他のパスが明示的に指定されない限り、%LOCALAPPDATA% または %TEMP% が使用されます。 Windows 以外の環境では、必ず有効な場所を指定する必要があります。そうしないと、テレメトリがローカル ディスクに保存されません。

## <a name="which-channel-should-i-use"></a>どのチャネルを使用すればよいですか?

実行時間の長いアプリケーションが絡む運用シナリオのほとんどでは、`ServerTelemetryChannel` がお勧めです。 `ServerTelemetryChannel` により実装される `Flush()` メソッドは同期的ではなく、保留中の全項目がメモリまたはディスクから送信される保証もありません。 アプリケーションがシャットダウンされそうな状況のシナリオでこのチャネルを使用する場合には、`Flush()` を呼び出した後に多少の遅延を発生させることをお勧めします。 正確にどの程度の遅延が必要になるかは、予測ができません。 項目または `Transmission` インスタンスがメモリ内にある数、ディスク上にある数、バックエンドに送信される数、チャネルがエクスポネンシャル バックオフ シナリオの途中であるかどうかなどの要素に左右されます。

同期フラッシュの実行が必要な場合には、`InMemoryChannel` の使用をお勧めします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights のチャネルでは、テレメトリの配信が保証されるのでしょうか? そうでない場合には、テレメトリが失われる可能性があるシナリオはどのようなものでしょうか?

簡潔に言うと、組み込みチャネルのいずれにおいても、バックエンドへのテレメトリ配信に関して、トランザクションのような保証はありません。 `ServerTelemetryChannel` は配信の信頼性という点で `InMemoryChannel` よりも優れていますが、こちらもテレメトリの送信はベストエフォートにすぎません。 次のような一般的なシナリオを含め、いくつかの状況ではテレメトリが失われることがあります。

1. アプリケーションがクラッシュすると、メモリ内の項目が失われます。

1. ネットワークの問題が長期間に及んだ場合には、テレメトリが失われます。 ネットワークの停止中や Application Insights バックエンドでの問題発生時には、テレメトリがローカル ディスクに保存されます。 ただし、24 時間が経過した項目は破棄されます。

1. Windows でテレメトリが保存される既定のディスクの場所は、%LOCALAPPDATA% または %TEMP% です。 これらは通常、マシンのローカルの場所です。 アプリケーションがある場所から別の場所に物理的に移行した場合、元の場所に保存されているテレメトリが失われます。

1. Windows 上の Web Apps では、既定のディスク ストレージの場所は D:\local\LocalAppData です。 この場所は、永続的なものではありません。 アプリの再起動やスケールアウトなどの操作によりワイプされるので、そこに保存されているテレメトリが失われます。 既定値をオーバーライドして、ストレージに D:\home のような永続的な場所を指定することもできます。 ただし、このような永続的な場所はリモート ストレージによって提供されるので、速度が遅いことがあります。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel は Windows 以外のシステムでも動作しますか?

このチャネルは、パッケージと名前空間の名前に "WindowsServer" という文言が含まれてこそいるものの、次の例外を除き、Windows 以外のシステムでもサポートされます。 Windows 以外のシステムでは、チャネルにより既定でローカル ストレージ フォルダーが作成されることはありません。 ローカル ストレージ フォルダーをご自身で作成したうえで、それを使用するようにチャネルを構成する必要があります。 ローカル ストレージの構成が済んだ後は、チャネルがすべてのシステムで同じように動作します。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK では一時的なローカル ストレージが作成されますか? データはストレージで暗号化されますか?

ネットワークの問題の発生中またはスロットリング中は、SDK によりテレメトリの項目がローカル ストレージに保存されます。 このデータがローカルで暗号化されることはありません。

Windows システムの場合、SDK により自動で %TEMP% または %LOCALAPPDATA% ディレクトリに一時的なローカル フォルダーが作成され、アクセスが管理者と現在のユーザーのみに制限されます。

Windows 以外のシステムの場合、SDK によりローカル ストレージが自動で作成されることはないので、既定ではデータがローカルに保存されません。 ストレージ ディレクトリを作成し、それを使用するようにチャネルを構成できます。 この場合、そのディレクトリのセキュリティ保護については、ご自身の責任となります。
[データ保護とプライバシー](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)の詳細をご確認ください。

## <a name="open-source-sdk"></a>オープンソース SDK
Application Insights の他の SDK と同じく、チャネルもオープン ソースです。 コードの閲覧、投稿、問題のレポートは[公式の GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-dotnet)で行ってください。

## <a name="next-steps"></a>次の手順

* [サンプリング](../../azure-monitor/app/sampling.md)
* [SDK のトラブルシューティング](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
