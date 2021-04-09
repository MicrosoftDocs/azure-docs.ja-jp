---
title: Azure Relay .NET Standard API の概要 | Microsoft Docs
description: この記事は、Azure Relay ハイブリッド接続 .NET Standard API の主な概要についてまとめてあります。
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625133"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay ハイブリッド接続 .NET Standard API の概要

この記事は主要な Azure Relay ハイブリッド接続 .NET Standard [クライアント API](/dotnet/api/microsoft.azure.relay) についてまとめてあります。
  
## <a name="relay-connection-string-builder-class"></a>Relay 接続文字列ビルダー クラス

[RelayConnectionStringBuilder][RelayConnectionStringBuilder] クラスは、Relay ハイブリッド接続固有の接続文字列の書式を設定します。 このクラスを使用して、接続文字列の形式を確認することや、最初から接続文字列を作成することができます。 例については、次のコードを参照してください。

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

`RelayConnectionStringBuilder` メソッドに接続文字列を直接渡すこともできます。 この操作では、接続文字列が有効な形式であることを確認できます。 いずれかのパラメーターが無効の場合、コンストラクターが `ArgumentException` を生成します。

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>ハイブリッド接続ストリーム

[HybridConnectionStream][HCStream] クラスは、[HybridConnectionClient][HCClient] または [HybridConnectionListener][HCListener] のどちらを使用している場合でも、Azure Relay エンドポイントとの間でデータを送受信するために使用される主要オブジェクトです。

### <a name="getting-a-hybrid-connection-stream"></a>ハイブリッド接続ストリームの取得

#### <a name="listener"></a>リスナー

[HybridConnectionListener][HCListener] オブジェクトを使用して、次のように `HybridConnectionStream` オブジェクトを取得できます。

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

[HybridConnectionClient][HCClient] オブジェクトを使用して、次のように `HybridConnectionStream` オブジェクトを取得できます。

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>データの受信

[HybridConnectionStream][HCStream] クラスでは、双方向の通信が可能です。 ほとんどの場合、継続的にストリームから受信します。 ストリームからテキストを読み取る場合は、[StreamReader](/dotnet/api/system.io.streamreader) オブジェクトも使用できます。これを使用すると、データを容易に解析できます。 たとえば、`byte[]` としてではなく、テキストとしてデータを読み取ることができます。

次のコードは、取り消しが要求されるまで、ストリームから個々のテキスト行を読み取ります。

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>データの送信

接続が確立されたら、リレー エンドポイントにメッセージを送信することができます。 接続オブジェクトは [Stream](/dotnet/api/system.io.stream) を継承するため、データを `byte[]` として送信します。 以下の例は、その方法を示しています。

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

ただし、毎回文字列をエンコードすることなく、テキストを直接送信する場合は、[StreamWriter](/dotnet/api/system.io.streamwriter) オブジェクトを使用して `hybridConnectionStream` オブジェクトをラップすることができます。

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>次のステップ

Azure リレーの詳細については、次のリンク先を参照してください。

* [Microsoft.Azure.Relay リファレンス](/dotnet/api/microsoft.azure.relay)
* [Azure Relay とは](relay-what-is-it.md)
* [使用可能なリレー API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
