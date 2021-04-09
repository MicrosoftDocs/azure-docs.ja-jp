---
title: gRPC 推論サーバーの開発とデプロイ - Azure
description: この記事では、gRPC 推論サーバーを開発およびデプロイする方法に関するガイダンスを提供します。
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6184a369e73c26d3a8a716f9daf1c0420a5239fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881654"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>攻略ガイド - gRPC 推論サーバーの開発とデプロイ

## <a name="overview"></a>概要

この記事では、グラフ拡張機能を介して Live Video Analytics (LVA) と統合できるように、gRPC 推論サーバー内で任意の AI モデルをラップする方法について説明します。 

## <a name="suggested-pre-reading"></a>推奨される事前読み取り

* [メディア グラフ拡張機能](media-graph-extension-concept.md)
* [gRPC 拡張プロトコル](grpc-extension-protocol.md)
* [推論メタデータ スキーマ](inference-metadata-schema.md)
* [gRPC の概要](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 言語ガイド](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>前提条件

* [サポートされている Linux オペレーティング システム](../../iot-edge/support.md#operating-systems)のいずれかを実行している x86-64 または ARM64 デバイス、または Windows マシン。
* マシンに [Docker がインストール](https://docs.docker.com/desktop/#download-and-install)されている。
* [IoT Edge ランタイム](../../iot-edge/how-to-install-iot-edge.md?tabs=linux)がインストールされている。

## <a name="grpc-implementation-steps"></a>gRPC の実装手順

gRPC 推論サーバーを作成し、それを拡張機能として Live Video Analytics と共に実装するには、次の手順を使用します。

### <a name="setup"></a>セットアップ:

[Live Video Analytics モジュールを IoT Edge デバイスにデプロイし動作](deploy-iot-edge-device.md)させるために必要な手順を実行します。

### <a name="high-level-implementation-steps"></a>基本的な導入手順:

1. C#、C++、Dart、Go、Java、Node、Objective-C、PHP、Python、Ruby など、gRPC でサポートされている多くの言語からいずれかを選択します。
1. [proto3 ファイル](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)を使用して Live Video Analytics と通信する gRPC サーバーを実装します。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="proto3 ファイルを使用して Live Video Analytics と通信する gRPC サーバー":::

    このサービス内:
    1. サーバーとクライアント間のセッション説明メッセージの交換を処理します。
    1. [メディア サンプル メッセージ](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)を処理し、結果を返します。

        1. トレーニング済みのモデルを使用して受信メッセージを推論する推論エンジンを呼び出します。
        1. エンジンから推論の結果を受信し、それらをメディア サンプルとしてパッケージ化して、[inferencing.proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) ファイルを使用して Live Video Analytics に送り戻します。

            または、メディア変換機能をメディア サンプルに呼び出します。
1. gRPC サーバー実装をデプロイします。 これを行うには、次の 2 つの方法があります。

    1. Live Video Analytics モジュールと併置されている IoT モジュールとしてデプロイする
    1. Live Video Analytics モジュールとデータを交換できる、ネットワーク アクセス可能なノード (オンプレミスまたはクラウド上) への IoT モジュールとしてデプロイする。
1. Live Video Analytics モジュールを使用して Live Video Analytics グラフ トポロジを構成し、それを gRPC サーバーに向けます。

### <a name="recommendation"></a>推奨事項:

同じノードで併置すると、共有メモリを使用して、最適なパフォーマンスを達成できます。 これには、プログラミング言語/環境によって公開された Linux 共有メモリ機能を使用する必要があります。

1. Linux 共有メモリ ハンドルを開きます。
1. フレームを受信したときに、共有メモリ内のアドレス オフセットにアクセスします。
1. Live Video Analytics でメモリを再利用できるように、フレーム処理の完了を確認します。

## <a name="create-a-grpc-inference-server"></a>gRPC 推論サーバーを作成する

次に、共有メモリ経由で [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージを使用して Live Video Analytics からビデオ フレームを受信する IoT Edge モジュール (外部 AI) を構築し、フレームを "濃色" または "薄色" として分類し、[推論メタデータ スキーマ](inference-metadata-schema.md)を使用して、Live Video Analytics の IoT Hub メッセージシンクに推論結果を返します。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="IoT Edge モジュール (外部 AI) を構築する":::

この gRPC 推論サーバーは、Live Video Analytics とカスタム AI の間で送信される [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージを処理するために構築された .NET Core コンソール アプリケーションです。 次に、Live Video Analytics と gRPC 推論サーバーとの間のメッセージ フローを示します。

1. Live Video Analytics は、メディア ストリーム情報が定義するメディア ストリーム記述子を送信します ([extension.proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) を参照)。この情報にはビデオ フレームが続いて、gRPC ストリーム セッションで [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージとしてサーバーに送信されます。 
1. サーバーは、ストリーム記述子の検証と確認応答を行い、目的のデータ転送方法を設定します。
1. Live Video Analytics は続いて、ビデオ フレームを含む MediaSample ファイルの送信を開始します。
1. サーバーは、受信時にビデオ フレームを分析し、ユーザーが定義したイメージ プロセッサを使用して処理を開始します。
1. サーバーは続いて、推論結果が使用可能になるとすぐに、[protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージとしてこれを返します。 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="gRPC 推論サーバーを作成する":::

ビデオ フレームは、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.)で転送することも、protobuf メッセージに埋め込むこともできます。 データ転送モードは、フレームがどのように転送されるかを特定するように、LVA グラフ トポロジで構成できます。 これを実現するには、次に示すように、MediaGraphGrpcExtension プロパティの **dataTransfer** 要素を構成します。

埋め込み:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

共有メモリ:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 共有メモリで通信する場合は、IpcMode の値を **shareable** に設定し、gRPC サーバー モジュールで IpcMode の値を **container:{CONTAINER_NAME}** に設定する必要があります。 これらの設定は、モジュールを Azure IoT Hub にデプロイするために使用される配置マニフェスト ファイルで行います。 次に、IoT Edge モジュールを設定するときに使用するコンテナー オプションの例を示します。

Live Video Analytics モジュール:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 拡張機能モジュール:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> grpcExtension 内の "container:lvaEdge" の共有メモリ領域にアクセスできることを確認します。

## <a name="sample-grpc-server"></a>gRPC サーバーのサンプル

gRPC サーバーを開発する方法の詳細を理解するには、コード サンプルを参照してください。

1. GitHub リンク [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) からリポジトリをクローンします。
1. VSCode を起動し、/src/edge/modules/grpcExtension フォルダーに移動します。
1. ファイルの簡単なチュートリアルを実行してみましょう。

    1. **Program.cs**: これはアプリケーションのエントリ ポイントです。 これは、ホストとして機能する gRPC サーバーの初期化と管理を担当します。 このサンプルでは、gRPC クライアント (Live Video Analytics など) からの着信 gRPC メッセージをリッスンするポートは、AppConfig.json の grpcBindings 構成要素で指定されています。
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**:このクラスは、[protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) メッセージの処理を担当します。 メッセージ内のフレームが読み取られ、ImageProcessor が呼び出され、推論結果が書き込まれます。
gRPC サーバーのポート接続を構成し初期化したので、次は着信 gRPC メッセージを処理する方法について説明します。

        * gRPC セッションが確立されると、gRPC サーバーがクライアント (Live Video Analytics) から受信する最初のメッセージ は、[extension.proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) ファイルで定義されている MediaStreamDescriptor です。 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * このサーバー実装では、メソッド `ProcessMediaStreamDescriptor` によって、ビデオ ファイルの MediaStreamDescriptor の MediaDescriptor プロパティが検証され、トポロジでの指定内容と使用されるデプロイ テンプレート ファイルに応じて、データ転送モード (共有メモリを使用するか、埋め込みフレーム転送モードを使用するか) が設定されます。 
        * gRPC サーバーは、メッセージを受信し、データ転送モードを正しく設定すると、クライアントに確認応答として MediaStreamDescriptor メッセージを返すので、サーバーとクライアント間の接続が確立されます。    
        * Live Video Analytics は受信確認を受信すると、メディア ストリームを gRPC サーバーに転送し始めます。 このサーバー実装では、メソッド `ProcessMediaStream` が受信 MediaStreamMessage を処理します。 また、MediaStreamMessage は [extension.proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) で定義されています。

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Appconfig.json の batchSize の値に応じて、サーバーは、メッセージを受信し続け、ビデオ フレームを一覧に格納します。 batchSize の制限に達すると、この関数は、イメージを処理する関数またはファイルを呼び出します。 ここでは、メソッドは BatchImageProcessor.cs という名前のファイルを呼び出します
    1. **Processors\BatchImageProcessor.cs**:このクラスは、イメージの処理を担当します。 このサンプルでは、イメージ分類モデルを使用しました。 処理されるすべてのイメージに対して次のアルゴリズムが使用されます。

        1. 処理のために、バイト配列内のイメージを変換します。 次のメソッドを参照してください: `GetBytes(Bitmap image)`
        
            現在使用しているサンプル プロセッサは、JPG でエンコードされたイメージ フレームのみをサポートしており、ピクセル形式ではサポートしていません。 カスタム プロセッサが別のエンコードや形式をサポートしている場合は、プロセッサ クラスの `IsMediaFormatSupported` メソッドを更新します。
        1. [ColorMatrix クラス](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)を使用して、画像をグレー スケールに変換します。 次のメソッドを参照してください: `ToGrayScale(Image source)`。
        1. グレー スケールの画像を取得した後、グレー スケールの平均バイト数を計算します。
        1. 平均値が 127 未満の場合は、画像を "濃色" として分類し、それ以外の場合は、信頼度の値を 1.0 として "薄色" として分類します。 次のメソッドを参照してください: `ProcessImage(List<Image> images)`。

    独自のプロセッサ ロジックを追加するには、このクラスを変更するか、新しいクラスを追加してメソッドを実装します。

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    新しいクラスを追加したら、MediaGraphExtensionService.cs を更新してクラスをインスタンス化し、ProcessImage メソッドを呼び出して処理ロジックを実行する必要があります。 

## <a name="connect-with-live-video-analytics-module"></a>Live Video Analytics モジュールと接続する

gRPC 拡張モジュールを作成したので、次に、メディア グラフ トポロジを作成してデプロイします。

1. Visual Studio Code を使用し、[こちらの手順](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)に従って Docker にサインインします。
1. Visual Studio Code で、src/edge に移動します。 .env ファイルと、いくつかの展開テンプレート ファイルがあります。

    展開テンプレートは、エッジ デバイスの配置マニフェストを参照しています。 そこには、いくつかのプレースホルダー値が含まれています。 .env ファイルには、それらの変数の値が格納されています。
    
    次に、 [Build and Push IoT Edge Solution]\(IoT Edge ソリューションをビルドしてプッシュする\) を選択します。 この手順では、src/edge/deployment.grpc.template.json を使用します。
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Live Video Analytics モジュールと接続する":::
    
    このアクションにより、gRPC サーバー モジュールがビルドされ、お使いの Azure Container Registry に画像がプッシュされます。
    環境変数 CONTAINER_REGISTRY_USERNAME_myacr と CONTAINER_REGISTRY_PASSWORD_myacr が .env ファイル内で定義されていることを確認してください。
1. src/cloud-to-device-console-app フォルダーに移動します。 ここには、appsettings.json ファイルと、他にいくつかのファイルがあります。

    * c2d-console-app.csproj - Visual Studio Code のプロジェクト ファイルです。
    * operations.json - プログラムで実行する操作のリストです。
    * Program.cs - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。
        * プログラムの出力を [ターミナル] ウィンドウで調べたり、モジュールによって生成されたイベントを [出力] ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。
1. operations.json ファイルを編集します。

    * グラフ トポロジへのリンクを変更します。

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * GraphInstanceSet で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * GraphTopologyDelete で、名前を編集します。<br/>`"name": "InferencingWithGrpcExtension"`

            トポロジ (たとえば `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json`) は、拡張アドレスを定義する必要があります。
    * 拡張機能アドレス パラメーター

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 構成

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。
この手順により、src/edge/config/deployment.grpc.amd64.json に IoT Edge 配置マニフェストが作成されます。 そのファイルを右クリックし、 **[単一デバイスのデプロイを作成する]** を選択します。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="IoT Edge の配置マニフェストを生成してデプロイする":::

次に、IoT Hub デバイスを選択するように Visual Studio Code から求められます。 IoT Edge デバイス (lva-sample-device) を選択します。
この段階で、お使いの IoT Edge デバイスへのエッジ モジュールのデプロイが開始されました。 30 秒ほど経過したら、Visual Studio Code の左下のセクションで Azure IoT Hub を最新の情報に更新してください。 lvaExtension という名前の新しいモジュールがデプロイされたことが表示されます。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="新しいモジュールが lvaExtension という名前でデプロイされました":::

## <a name="next-steps"></a>次のステップ

[お使いのモデルでのライブ ビデオ](use-your-model-quickstart.md) クイックスタートで述べられた **イベントの監視の準備をする** 手順に従って、サンプルを実行し、結果を解釈します。 また、[gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json)、[CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json)、EVRtoAssetsByGrpcExtension、[EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json) などの gRPC トポロジのサンプルを確認してください。