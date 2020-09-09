---
title: メディア グラフ拡張機能とは - Azure
description: Live Video Analytics on IoT Edge では、グラフ拡張ノードを使用してメディア グラフ処理機能を拡張することができます。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2e1ca730a6736776425cd70b323147b58e8eacbf
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716080"
---
# <a name="media-graph-extension"></a>メディア グラフ拡張機能

Live Video Analytics on IoT Edge では、グラフ拡張ノードを使用してメディア グラフ処理機能を拡張することができます。 分析拡張機能プラグインでは、従来のイメージ処理手法またはコンピューター ビジョンの AI モデルを利用できます。 グラフ拡張機能を有効にするには、拡張プロセッサ ノードをメディア グラフに含めます。 拡張プロセッサ ノードは、構成済みのエンドポイントにビデオ フレームを中継し、拡張機能へのインターフェイスとして機能します。 ローカルまたはリモートのエンドポイントに接続でき、必要に応じて認証と TLS 暗号化によって保護することができます。 また、グラフ拡張プロセッサ ノードでは、ビデオ フレームをカスタム拡張機能に送信する前に、オプションでそれらのスケーリングとエンコードを実行できます。

Live Video Analytics は、次の 2 種類のメディア グラフ拡張プロセッサをサポートしています。

* [HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor)
* [gRPC 拡張プロセッサ](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP 拡張プロセッサ

HTTP 拡張プロセッサでは、HTTP プロトコルを使用して拡張性シナリオを実現します。この場合、パフォーマンスや最適なリソース使用率が主要な懸念事項ではありません。 HTTP REST エンドポイントを使用して独自の AI をメディア グラフに公開できます。 

HTTP 拡張プロセッサ ノードは、次の場合に使用します。

* 既存の HTTP 推論システムとの相互運用性を向上させたい。
* パフォーマンスの低いデータ転送が許容される。
* Live Video Analytics に対して単純な要求 - 応答インターフェイスを使用したい。

## <a name="grpc-extension-processor"></a>gRPC 拡張プロセッサ

gRPC 拡張プロセッサでは、gRPC ベースの高性能な構造化プロトコルを使用して拡張シナリオを実現します。 これは、パフォーマンスや最適なリソース使用率が優先されるシナリオに最適です。 gRPC 拡張プロセッサを使用すると、構造化データ定義のメリットを最大限に活用できます。 gRPC は、以下を使用して、コンテンツ転送の高いパフォーマンスを実現します。

* [未処理の共有メモリ](https://en.wikipedia.org/wiki/Shared_memory) 
* gRPC メッセージの本文へのコンテンツの直接埋め込み。 

gRPC 拡張プロセッサは、推論メッセージの交換と共にメディア プロパティの送信を行うために使用できます。
したがって、gRPC 拡張プロセッサ ノードは、次の場合に使用します。

* 構造化されたコントラクトを使用したい (たとえば、要求と応答の構造化されたメッセージ)。
* プロトコル バッファー ([protobuf](https://developers.google.com/protocol-buffers)) を、通信の基礎となるメッセージ交換形式として使用したい。
* 受信要求を解析して適切な実装関数を呼び出すためにカスタム要求ハンドラーを必要とする従来の要求 - 応答モデルではなく、単一ストリーム セッションで gRPC サーバーと通信したい。 
* Live Video Analytics と対象のモジュール間で、待ち時間が短くスループットの高い通信を必要とする。

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Live Video Analytics で推論モデルを使用する

メディア グラフ拡張機能を使用すると、ONNX、TensorFlow、PyTorch などの使用可能な任意の推論ランタイム、または独自の Docker コンテナーで、任意の推定モデルを実行できます。 推論カスタム拡張機能は、最適なパフォーマンスを得るために Live Video Analytics の Edge モジュールと共にデプロイする必要があります。その後、グラフ トポロジに含まれる HTTP 拡張プロセッサまたは gRPC 拡張プロセッサを使用して呼び出されます。 さらに、カスタム拡張機能に対する呼び出しの頻度を調整するには、必要に応じて、[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor)と[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor)をメディア拡張プロセッサのアップストリームに追加します。

次の図は、高レベルのデータ フローを示しています。
 
![データ フロー](./media/media-graph-extension/data-flow.png)

## <a name="samples"></a>サンプル

Live Video Analytics 用の [Jupyter ノートブック](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md)のサンプルをいくつか確認してください。 これらのノートブックでは、次の操作について詳しい手順を説明しています。

* 拡張サービスの Docker コンテナー イメージを作成する方法
* 拡張サービスを Live Video Analytics コンテナーと共にコンテナーとしてデプロイする方法
* 拡張クライアントで Live Video Analytics メディア グラフを使用し、拡張 gRPC エンドポイントを指定する方法
