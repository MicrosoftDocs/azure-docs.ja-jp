---
title: Live Video Analytics on IoT Edge に関する FAQ - Azure
description: この記事では、Live Video Analytics on IoT Edge に関してよく寄せられる質問に回答します。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 72a07a1a509aebcd7ba4048d0c84e913481c978e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702251"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Live Video Analytics on IoT Edge の FAQ

この記事では、Live Video Analytics on Azure IoT Edge に関してよく寄せられる質問に回答します。

## <a name="general"></a>全般

**グラフ トポロジ定義で使用できるシステム変数は何ですか。**

| 変数   |  説明  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | UTC 時間でインスタンスを表します。通常は、次の形式の日付と時刻として表現されます:<br>*yyyyMMddTHHmmssZ* | 
| System.PreciseDateTime | ミリ秒を含む ISO8601 ファイル準拠の形式で協定世界時 (UTC) の日時インスタンスを表します。形式は次のとおりです:<br>*yyyyMMddTHHmmss.fffZ* | 
| System.GraphTopologyName   | メディア グラフ トポロジを表し、グラフのブループリントを保持します。 | 
| System.GraphInstanceName |    メディア グラフ インスタンスを表します。パラメーター値を保持し、トポロジを参照します。 | 

## <a name="configuration-and-deployment"></a>構成とデプロイ

**メディア Edge モジュールを Windows 10 デバイスに展開することはできますか。**

はい。 詳細については、「[Windows 10 上の Linux コンテナー](/virtualization/windowscontainers/deploy-containers/linux-containers)」を参照してください。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>IP カメラと RTSP 設定からのキャプチャ

**ビデオ ストリームで送信するには、デバイスで特別な SDK を使用する必要がありますか。**

いいえ。Live Video Analytics on IoT Edge を使用すると、ほとんどの IP カメラでサポートされている RTSP (リアルタイム ストリーミング プロトコル) を使用したメディアのキャプチャがサポートされます。

**リアルタイム メッセージング プロトコル (RTMP) またはスムーズ ストリーミング プロトコル (Media Services ライブ イベントなど) を使用して Live Video Analytics on IoT Edge にメディアをプッシュできますか。**

いいえ。Live Video Analytics を使用すると、IP カメラからのビデオのキャプチャにのみ RTSP がサポートされます。 TCP/HTTP 経由の RTSP ストリーミングをサポートするすべてのカメラが動作します。 

**グラフ インスタンスで RTSP ソース URL をリセットまたは更新できますか。**

はい (グラフ インスタンスが "*非アクティブ*" 状態の場合)。  

**テストおよび開発中に使用できる RTSP シミュレーターはありますか。**

はい。学習プロセスをサポートするためのクイック スタートとチュートリアルで使用できる [RTSP シミュレーター](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge モジュールがあります。 このモジュールは、ベストエフォートとして提供されており、常に使用できるとは限りません。 シミュレーターを数時間以上使用 "*しない*" ことを強くお勧めします。 運用環境のデプロイを計画する前に、実際の RTSP ソースを使用したテストに投資する必要があります。

**Edge での IP カメラの ONVIF 検出をサポートしていますか。**

いいえ。Edge 上のデバイスの ONVIF (Open Network Video Interface Forum) 検出はサポートされていません。

## <a name="streaming-and-playback"></a>ストリーミングと再生

**Edge から Azure Media Services に記録されたアセットは、HLS や DASH などのストリーミング テクノロジを使用して再生できますか。**

はい。 記録されたアセットは、Azure Media Services の他のアセットと同様にストリーミングできます。 コンテンツをストリーミングするには、ストリーミング エンドポイントを作成し、実行状態にする必要があります。 標準ストリーミング ロケーターの作成プロセスを使用すると、対応する任意のプレーヤー フレームワークにストリーミングするための Apple HTTP ライブ ストリーミング (HLS) または Dynamic Adaptive Streaming over HTTP (DASH。MPEG-DASH とも呼ばれる) のマニフェストにアクセスできます。 HLS または DASH マニフェストの発行と作成の詳細については、[ダイナミック パッケージ](../latest/dynamic-packaging-overview.md)に関するページを参照してください。

**アーカイブされたアセットで Media Services の標準的なコンテンツ保護と DRM 機能を使用できますか。**

はい。 動的暗号化コンテンツ保護および DRM (デジタル著作権管理) のすべての標準機能を、メディア グラフから記録されたアセットに対して使用できます。

**記録されたアセットのコンテンツを表示するには、どのプレーヤーを使用できますか。**

準拠している HLS バージョン 3 またはバージョン 4 をサポートするすべての標準プレーヤーがサポートされています。 また、準拠している MPEG-DASH の再生が可能なプレーヤーもサポートされています。

テストに推奨されるプレーヤーは次のとおりです。

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple ネイティブ HTTP ライブ ストリーミング](https://developer.apple.com/streaming/)
* Edge、Chrome、または Safari に組み込まれている HTML5 ビデオ プレーヤー
* HLS または DASH の再生をサポートする商用プレーヤー

**メディア グラフ アセットのストリーミングにはどのような制限がありますか。**

メディア グラフからライブまたは記録されたアセットをストリーミングするには、大規模なインフラストラクチャとストリーミング エンドポイント (メディアと娯楽、Over the Top (OTT)、およびブロードキャストの各顧客向けのオンデマンドおよびライブ ストリーミング用に Media Services でサポートされているのと同じもの) を使用します。 つまり、Azure Content Delivery Network、Verizon、または Akamai をすばやく簡単に有効にして、シナリオに応じて数人の視聴者から最大で数百万の視聴者にコンテンツを配信することができます。

コンテンツの配信には Apple HLS または MPEG DASH のどちらでも使用できます。

## <a name="design-your-ai-model"></a>AI モデルを設計する 

**Docker コンテナーに複数の AI モデルがラップされています。Live Video Analytics でそれらをどのように使用すればよいですか。** 

ソリューションは、推論サーバーで Live Video Analytics との通信に使用される通信プロトコルによって異なります。 以下のセクションで各プロトコルのしくみについて説明します。

*HTTP プロトコルを使用する*:

* 1 つのコンテナー (単一の lvaExtension):  

   推論サーバーでは、1 つのポートを使用できますが、さまざまな AI モデルに対して異なるエンドポイントを使用できます。 たとえば Python サンプルの場合、モデルごとに異なる `route` を次のように使用できます。 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   次に、Live Video Analytics のデプロイで、グラフをインスタンス化するときに各インスタンスの推論サーバー URL を次のように設定します。 

   1 番目のインスタンス: 推論サーバー URL = `http://lvaExtension:44000/score/face_detection`<br/>
   2 番目のインスタンス: 推論サーバー URL = `http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > または、さまざまなポートで AI モデルを公開し、グラフをインスタンス化するときにそれらを呼び出すことができます。  

* 複数のコンテナー: 

   各コンテナーは、異なる名前でデプロイされます。 以前は、Live Video Analytics のドキュメント セットに、*lvaExtension* という名前の拡張機能をデプロイする方法を示していました。 現在は、同じ HTTP インターフェイス (つまり、同じ `/score` エンドポイント) を持つ 2 つの異なるコンテナーを開発できるようになりました。 これらの 2 つのコンテナーを別々の名前でデプロイし、確実に両方が "*異なるポート*" でリッスンするようにします。 

   たとえば、`lvaExtension1` という名前の 1 つのコンテナーでポート `44000` をリッスンし、`lvaExtension2` という名前の 2 つ目のコンテナーでポート `44001` をリッスンしているとします。 

   Live Video Analytics トポロジで、次のような異なる推論 URL を持つ 2 つのグラフをインスタンス化します。 

   1 番目のインスタンス: 推論サーバー URL = `http://lvaExtension1:44001/score`    
   2 番目のインスタンス: 推論サーバー URL = `http://lvaExtension2:44001/score`
   
*gRPC プロトコルを使用する*: 

* 汎用リモート プロシージャ コール (gRPC) プロトコルの使用時に、gRPC サーバーで異なるポートを介して異なる AI モデルが公開されている場合、Live Video Analytics モジュール 1.0 が唯一の方法となります。 [このコード例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)で、単一のポート (44000) ですべての yolo モデルを公開しています。 理論的には、yolo gRPC サーバーは、一部のモデルをポート 44000 で、他のモデルをポート 45000 で公開するように書き換えることができます。 

* Live Video Analytics モジュール 2.0 では、新しいプロパティが gRPC 拡張ノードに追加されます。 このプロパティ (**extensionConfiguration**) は、gRPC コントラクトの一部として使用できる省略可能な文字列です。 1 つの推論サーバーに複数の AI モデルがパッケージ化されている場合、AI モデルごとにノードを公開する必要はありません。 代わりに、グラフ インスタンスの場合は、拡張機能プロバイダーとして、**extensionConfiguration** プロパティを使用して、さまざまな AI モデルを選択する方法を定義できます。 実行中、この文字列が Live Video Analytics から推論サーバーに渡され、推論サーバーは、その文字列を使用して目的の AI モデルを呼び出すことができます。 

**AI モデルに関連する gRPC サーバーを構築しているところです。複数のカメラまたはグラフ インスタンスでの使用をサポートできるようにしたいと考えています。サーバーをどのように構築すればよいですか。** 

 まず、サーバーが一度に複数の要求を処理できるように、または並列スレッドで動作できるようにします。 

たとえば、次の [Live Video Analytics gRPC サンプル](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)には、既定の数の並列チャネルが設定されています。 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

上記の gRPC サーバーのインスタンス化においては、カメラあたり (つまり、グラフ トポロジ インスタンスあたり) 3 つのチャネルのみをサーバーで一度に開くことができます。 4 つ以上のインスタンスをサーバーに接続しないようにしてください。 4 つ以上のチャネルを開こうとすると、既存のチャネルがドロップされるまで、要求は保留されます。  

上記の gRPC サーバー実装は、Python サンプルで使用されています。 開発者は、独自のサーバーを実装することも、前の既定の実装を使用してワーカーの数を増やすこともできます。これは、ビデオ フィードに使用するカメラの数に設定します。 

複数のカメラを設定して使用するには、それぞれが同じまたは異なる推論サーバー (前の段落に記載されているサーバーなど) を指している複数のグラフ トポロジ インスタンスをインスタンス化できます。 

**推論意思決定を行う前に、上流から複数のフレームを受信できるようにしたいと考えています。これを有効にするにはどうすればよいですか。** 

現在の [既定のサンプル](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)は "*ステートレス*" モードで動作します。 前の呼び出しの状態または呼び出し元でさえ保持されません。 つまり、複数のトポロジ インスタンスが同じ推論サーバーを呼び出す可能性がありますが、呼び出し元がだれなのか、または各呼び出し元の状態をサーバーで区別することはできません。 

*HTTP プロトコルを使用する*:

状態を保持するために、各呼び出し元 (またはグラフ トポロジ インスタンス) は、呼び出し元に固有の HTTP クエリ パラメーターを使用して推論サーバーを呼び出します。 たとえば、各インスタンスの推論サーバー URL アドレスは、次のようになります。  

1 番目のトポロジインスタンス = `http://lvaExtension:44000/score?id=1`<br/>
2 番目のトポロジインスタンス = `http://lvaExtension:44000/score?id=2`

… 

サーバー側のスコア ルートにより、呼び出しているユーザーが認識されます。 ID=1 の場合、その呼び出し元またはグラフ トポロジ インスタンスの状態を個別に保持できます。 そのうえで、受信したビデオ フレームをバッファーに保持できます。 たとえば、配列、または DateTime キーを含むディクショナリを使用します。値はフレームです。 次に、*x* 個のフレームを受信した後に処理 (推論) するようにサーバーを定義できます。 

*gRPC プロトコルを使用する*: 

gRPC 拡張機能の場合、各セッションは単一のカメラ フィード用であるため、ID を指定する必要はありません。 現在は、extensionConfiguration プロパティを使用して、ビデオ フレームをバッファーに格納でき、*x* 個のフレームを受信した後に処理 (推論) するようにサーバーを定義できます。 

**特定のコンテナーのすべての ProcessMediaStreams で同じ AI モデルが実行されますか。** 

いいえ。 グラフ インスタンスでのエンド ユーザーからの開始または停止呼び出しによって、セッションが構成されるか、カメラの切断または再接続が行われる可能性があります。 カメラでビデオがストリーミングされている場合は、1 つのセッションを保持することを目標としています。 

* 2 つのカメラで処理対象のビデオを送信すると、2 つのセッションが作成されます。 
* 2 つの gRPC 拡張ノードを持つグラフに 1 つのカメラを移動させると、2 つのセッションが作成されます。 

各セッションは、Live Video Analytics と gRPC サーバー間の全二重接続であり、各セッションは異なるモデルまたはパイプラインを持つことができます。 

> [!NOTE]
> カメラが切断または再接続された場合、カメラが許容範囲の上限を超えて一定期間オフラインになると、Live Video Analytics によって gRPC サーバーとの新しいセッションが開かれます。 サーバーでこれらのセッションにまたがって状態を追跡する必要はありません。 

Live Video Analytics には、グラフ インスタンス内の単一のカメラに対する複数の gRPC 拡張機能のサポートも追加されています。 これらの gRPC 拡張機能を使用して、AI 処理を順次または並列で実行したり、両方の組み合わせで実行したりすることができます。 

> [!NOTE]
> 複数の拡張機能を並行して実行すると、ハードウェア リソースに影響します。 コンピューティングのニーズに合ったハードウェアを選択する際には、この点に留意してください。 

**同時 ProcessMediaStreams の最大数はいくつですか。** 

Live Video Analytics でこの数に適用される上限はありません。  

**推論サーバーで CPU または GPU あるいはその他のハードウェア アクセラレータを使用する必要があるかどうかを判断するには、どうすればよいですか。** 

この決定は、開発した AI モデルの複雑さ、および CPU とハードウェア アクセラレータをどのように使用したいかによって異なります。 AI モデルを開発する際、モデルでどのリソースを使用し、どのようなアクションを実行する必要があるかを指定できます。 

**境界ボックスの後処理を伴う画像を格納するには、どのようにすればよいですか。** 

現在、境界ボックスの座標は、推論メッセージとしてのみ提供しています。 これらのメッセージを使用して境界ボックスをビデオ フレームに重ね合わせることができる、カスタム MJPEG ストリーマーを構築できます。  

## <a name="grpc-compatibility"></a>gRPC の互換性 

**メディア ストリーム記述子の必須フィールドを確認するにはどうすればよいですか。** 

値を指定しないフィールドには、[gRPC によって指定された既定値](https://developers.google.com/protocol-buffers/docs/proto3#default)が与えられます。  

Live Video Analytics には、*proto3* バージョンのプロトコル バッファー言語を使用します。 Live Video Analytics コントラクトによって使用されるすべてのプロトコル バッファー データは、[プロトコル バッファー ファイル](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)で使用できます。 

**最新のプロトコル バッファー ファイルを使用していることを確認するには、どうすればよいですか。** 

最新のプロトコル バッファーファイルは、[コントラクト ファイルのサイト](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)で入手できます。 コントラクト ファイルは更新されるたびに、この場所に置かれます。 プロトコル ファイルを更新する直近の予定はないため、バージョンを知るには、ファイル先頭のパッケージ名を確認してください。 これを次の手順に置き換えます。 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

これらのファイルが更新されると、名前の末尾にある "v-value" が増分されます。 

> [!NOTE]
> Live Video Analytics には proto3 バージョンの言語が使用されているため、フィールドは省略可能であり、バージョンには下位および上位互換性があります。 

**Live Video Analytics で使用できる gRPC の機能について教えてください。どの機能が必須で、どの機能が省略可能ですか。** 

プロトコル バッファー (Protobuf) コントラクトが満たされている場合は、任意のサーバー側の gRPC 機能を使用できます。 

## <a name="monitoring-and-metrics"></a>監視とメトリック

**Azure Event Grid を使用して Edge のメディア グラフを監視できますか。**

はい。 Prometheus メトリックを使用して、それらをイベント グリッドに公開できます。 

**Azure Monitor を使用して、クラウドまたは Edge のメディア グラフの正常性、メトリック、およびパフォーマンスを表示できますか。**

はい、この方法はサポートされています。 詳細については、「[Azure Monitor メトリックの概要](../../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。

**Media Services IoT Edge モジュールを簡単に監視できるツールはありますか。**

LVAEdge モジュール エンドポイントを簡単に監視できる Azure IoT Tools 拡張機能が、Visual Studio Code でサポートされています。 このツールを使用すると、"イベント" の IoT Hub 組み込みエンドポイントの監視をすばやく開始し、Edge デバイスからクラウドにルート指定される推論メッセージを確認できます。 

また、この拡張機能を使用して、LVAEdge モジュールのモジュール ツインを編集し、メディア グラフの設定を変更することもできます。

詳細については、[監視とログ記録](monitoring-logging.md)に関する記事をご覧ください。

## <a name="billing-and-availability"></a>課金と可用性

**Live Video Analytics on IoT Edge はどのように課金されますか。**

課金の詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

[クイック スタート: Live Video Analytics on IoT Edge の概要](get-started-detect-motion-emit-events-quickstart.md)