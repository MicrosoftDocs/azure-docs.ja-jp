---
title: gRPC 拡張プロトコル - Azure
description: この記事では、gRPC 拡張プロトコルを使用した、Live Video Analytics モジュールと AI または CV カスタム拡張機能の間でのメッセージの送信について説明します。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f7b5be859702199b07dfa0d6a43a09ca8ff0c42f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455858"
---
# <a name="grpc-extension-protocol"></a>gRPC 拡張プロトコル

Live Video Analytics on IoT Edge では、[グラフ拡張ノード](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept)を使用してメディア グラフ処理機能を拡張することができます。 gRPC 拡張プロセッサを拡張ノードとして使用した場合、Live Video Analytics モジュールとご利用の AI (または CV) モジュールとの間の通信は、gRPC ベースの高性能な構造化プロトコルで行われます。

この記事では、gRPC 拡張プロトコルを使用した、Live Video Analytics モジュールと AI または CV カスタム拡張機能の間でのメッセージの送信について説明します。

gRPC は、あらゆる環境で動作する最新のオープンソースの高パフォーマンス RPC フレームワークです。異なるプラットフォーム間、異なる言語間の通信がサポートされます。 gRPC トランスポート サービスでは、以下の間で HTTP/2 双方向ストリーミングを使用します。

* gRPC クライアント (IoT Edge モジュールの Live Video Analytics) 
* gRPC サーバー (カスタム拡張機能)

gRPC セッションは、TCP (TLS) ポートを介した、gRPC クライアントから gRPC サーバーへの単一の接続です。 

単一セッション: クライアントは、gRPC ストリーム セッションを介して、サーバーにメディア ストリーム記述子を [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージとして送信し、その後にビデオ フレームを送信します。 サーバーはストリーム記述子を検証し、ビデオ フレームを分析して、推論結果を protobuf メッセージとして返します。 

[推論メタデータ スキーマ オブジェクト モデル](/azure/media-services/live-video-analytics-edge/inference-metadata-schema)に従って定義した事前に定義されたスキーマに従って、有効な JSON ドキュメントを使用して応答を返すことを強くお勧めします。 これにより、他のコンポーネント、および Live Video Analytics モジュールに今後追加される可能性がある機能との相互運用性が向上します。

![gRPC 拡張機能のコントラクト](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>gRPC プロトコルの実装

### <a name="creating-a-grpc-connection"></a>gRPC 接続の作成

カスタム拡張機能では、次の gRPC サービスを実装する必要があります。

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

これを呼び出すと、gRPC 拡張機能と Live Video Analytics グラフの間を流れるメッセージの双方向ストリームが開きます。 このストリームで各パーティから送信される最初のメッセージには、後続の MediaSample で送信される情報を定義する MediaStreamDescriptor が含まれます。

たとえば、グラフ拡張機能では、gRPC メッセージに埋め込まれた、416x416 rgb24 エンコードされたフレームをカスタム拡張機能に送信することを示すメッセージ (ここでは JSON で表現) を送信できます。

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

カスタム拡張機能では、推論のみを返すことを示す次のメッセージを応答で送信します。

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

双方がメディア記述子を交換したら、Live Video Analytics によって拡張機能へのフレームの送信が開始されます。

> [!NOTE]
> gRPC サーバー側の実装は、任意のプログラミング言語で行えます。
### <a name="sequence-numbers"></a>シーケンス番号

gRPC 拡張機能ノードとカスタム拡張機能はどちらも、それらのメッセージに割り当てられた一連の個別のシーケンス番号を維持します。 これらのシーケンス番号は、1 から始まって単調に増加する必要があります。 メッセージが確認されていない場合、`ack_sequence_number` は無視されるおそれがあります。これは、最初のメッセージが送信されたときに発生する可能性があります。

対応するメッセージが完全に処理されたら、要求を確認する必要があります。 共有メモリ転送の場合、この確認は送信側が共有メモリを解放でき、受信側が共有メモリにアクセスしなくなることを示します。 送信側は、確認が完了するまで共有メモリを保持する必要があります。

### <a name="reading-embedded-content"></a>埋め込みコンテンツの読み取り

埋め込みコンテンツは、`content_byte`s フィールドを介して `MediaSample` メッセージから直接読み取ることができます。 データは `MediaDescriptor` に従ってエンコードされます。

### <a name="reading-content-from-shared-memory"></a>共有メモリからのコンテンツの読み取り

共有メモリを介してコンテンツを転送する場合、送信側は最初にセッションを確立するときに `MediaStreamDescriptor` に `SharedMemoryBufferTransferProperties` を含めます。 これは次のようになります (JSON で表現)。

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

その後、受信側は `/dev/shm/inference_client_share_memory_2146989006636459346` ファイルを開きます。 送信側は、このファイル内の特定の場所を参照する `ContentReference` を含む `MediaSample` メッセージを送信します。

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

その後、受信側はファイル内のこの場所からデータを読み取ります。

Live Video Analytics コンテナーが共有メモリを介して通信するには、コンテナーの IPC モードが正しく構成されている必要があります。 これはさまざまな方法で行うことができますが、推奨される構成をいくつか次に示します。

* ホスト デバイス上で実行されている gRPC 推論エンジンと通信する場合は、IPC モードをホストに設定する必要があります。
* 別の IoT Edge モジュールで実行されている gRPC サーバーと通信する場合、IPC モードは、Live Video Analytics モジュールでは共有可能に設定し、カスタム拡張機能では `container:liveVideoAnalytics` に設定する必要があります。`liveVideoAnalytics` は、Live Video Analytics モジュールの名前です。

上記の最初のオプションを使用したデバイス ツインでは次のようになります。

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

IPC モードの詳細については、 https://docs.docker.com/engine/reference/run/#ipc-settings---ipc を参照してください。

## <a name="mediagraph-grpc-extension-contract-definitions"></a>MediaGraph gRPC 拡張機能のコントラクトの定義

このセクションでは、データ フローを定義する gRPC コントラクトを定義します。

### <a name="protocol-messages"></a>プロトコル メッセージ

![プロトコル メッセージ](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>クライアント認証

カスタム拡張機能の実装側は、受信 gRPC 接続の信頼性を検証して、それらが gRPC 拡張機能ノードからのものであることを確認できます。 このノードは、検証対象となる要求ヘッダーのエントリを提供します。

ユーザー名およびパスワード資格情報を使用して、これを行うことができます。 gRPC 拡張機能ノードを作成する場合、資格情報は次のように提供されます。

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

gRPC 要求の送信時に、次のヘッダーが要求メタデータに含まれ、HTTP 基本認証が模倣されます。

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>gRPC 拡張機能経由で各 MediaGraph の推論サーバーを構成する
推論サーバーを構成するとき、推論サーバー内にパッケージされた AI モデルごとにノードを公開する必要はありません。 代わりにグラフ インスタンスに対して、`MediaGraphGrpcExtension` ノードの `extensionConfiguration` プロパティを使用して、AI モデルの選択方法を定義することができます。 実行中、この文字列が LVA から推論サーバーに渡され、推論サーバーは、その文字列を使用して目的の AI モデルを呼び出すことができます。 この `extensionConfiguration` プロパティはオプションのプロパティであり、サーバー固有です。 このプロパティは、次のように使用できます。
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>TLS 経由での gRPC の使用

推論に使用される gRPC 接続は、TLS を介してセキュリティ保護できます。 これは、Live Video Analytics と推論エンジン間のネットワークのセキュリティを保証できない場合に役立ちます。 TLS により、gRPC メッセージに埋め込まれたすべてのコンテンツが暗号化されるため、フレームを高速で送信するときに追加の CPU オーバーヘッドが発生します。

IgnoreHostname および IgnoreSignature 検証オプションは gRPC ではサポートされていないため、推論エンジンが提示するサーバー証明書には、gRPC 拡張機能ノードのエンドポイント URL の IP アドレスおよびホスト名と完全に一致する CN が含まれている必要があります。

## <a name="next-steps"></a>次のステップ

[推論メタデータ スキーマについて確認する](inference-metadata-schema.md)
