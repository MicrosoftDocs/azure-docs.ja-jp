---
title: メディア グラフ拡張機能とは - Azure
description: Live Video Analytics on IoT Edge では、グラフ拡張ノードを使用してメディア グラフ処理機能を拡張することができます。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 66656690e4e53508253091134a67adeb899b3dbf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455841"
---
# <a name="media-graph-extension"></a>メディア グラフ拡張機能

Live Video Analytics on IoT Edge では、グラフ拡張ノードを使用してメディア グラフ処理機能を拡張することができます。 分析拡張機能プラグインでは、従来のイメージ処理手法またはコンピューター ビジョンの AI モデルを利用できます。 グラフ拡張機能を有効にするには、拡張プロセッサ ノードをメディア グラフに含めます。 拡張プロセッサ ノードは、構成済みのエンドポイントにビデオ フレームを中継し、拡張機能へのインターフェイスとして機能します。 ローカルまたはリモートのエンドポイントに接続でき、必要に応じて認証と TLS 暗号化によって保護することができます。 また、グラフ拡張プロセッサ ノードでは、ビデオ フレームをカスタム拡張機能に送信する前に、オプションでそれらのスケーリングとエンコードを実行できます。 

Live Video Analytics は、次の 2 種類のメディア グラフ拡張プロセッサをサポートしています。

* [HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor)
* [gRPC 拡張プロセッサ](media-graph-concept.md#grpc-extension-processor)

グラフ拡張ノードが結果を JSON 形式で返すためには、分析拡張機能プラグインが必要となります。 理想的には、結果が[推論メタデータ スキーマ オブジェクト モデル](/azure/media-services/live-video-analytics-edge/inference-metadata-schema)に準拠している必要があります。

## <a name="http-extension-processor"></a>HTTP 拡張プロセッサ

HTTP 拡張プロセッサでは、[HTTP プロトコル](/azure/media-services/live-video-analytics-edge/http-extension-protocol)を使用して拡張性シナリオを実現します。この場合、パフォーマンスや最適なリソース使用率が主要な懸念事項ではありません。 HTTP REST エンドポイントを使用して独自の AI をメディア グラフに公開できます。 

HTTP 拡張プロセッサ ノードは、次の場合に使用します。

* 既存の HTTP 推論システムとの相互運用性を向上させたい。
* パフォーマンスの低いデータ転送が許容される。
* Live Video Analytics に対して単純な要求 - 応答インターフェイスを使用したい。

## <a name="grpc-extension-processor"></a>gRPC 拡張プロセッサ

gRPC 拡張プロセッサでは、gRPC ベースの高性能な[構造化プロトコル](/azure/media-services/live-video-analytics-edge/grpc-extension-protocol)を使用して拡張シナリオを実現します。 これは、パフォーマンスや最適なリソース使用率が優先されるシナリオに最適です。 gRPC 拡張プロセッサを使用すると、構造化データ定義のメリットを最大限に活用できます。 gRPC は、以下を使用して、コンテンツ転送の高いパフォーマンスを実現します。

* [未処理の共有メモリ](https://en.wikipedia.org/wiki/Shared_memory) 
* gRPC メッセージの本文へのコンテンツの直接埋め込み。 

gRPC 拡張プロセッサは、推論メッセージの交換と共にメディア プロパティの送信を行うために使用できます。
したがって、gRPC 拡張プロセッサ ノードは、次の場合に使用します。

* 構造化されたコントラクトを使用したい (たとえば、要求と応答の構造化されたメッセージ)。
* プロトコル バッファー ([protobuf](https://developers.google.com/protocol-buffers)) を、通信の基礎となるメッセージ交換形式として使用したい。
* 受信要求を解析して適切な実装関数を呼び出すためにカスタム要求ハンドラーを必要とする従来の要求 - 応答モデルではなく、単一ストリーム セッションで gRPC サーバーと通信したい。 
* Live Video Analytics と対象のモジュール間で、待ち時間が短くスループットの高い通信を必要とする。

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Live Video Analytics で推論モデルを使用する

メディア グラフ拡張機能を使用すると、ONNX、TensorFlow、PyTorch などの使用可能な任意の推論ランタイム、または独自の Docker コンテナーで、任意の推定モデルを実行できます。 推論カスタム拡張機能は、最適なパフォーマンスを得るために Live Video Analytics の Edge モジュールと共にデプロイする必要があります。その後、グラフ トポロジに含まれる HTTP 拡張プロセッサまたは gRPC 拡張プロセッサを使用して呼び出されます。 さらに、カスタム拡張機能に対する呼び出しの頻度を調整するには、必要に応じて、[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor)をメディア拡張プロセッサのアップストリームに追加します。

次の図は、高レベルのデータ フローを示しています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="AI 推論サービス":::

## <a name="samples"></a>サンプル

あらかじめ構築された拡張サービスを使用し、低フレーム レート ([HTTP 拡張プロセッサ](/azure/media-services/live-video-analytics-edge/use-your-model-quickstart?pivots=programming-language-csharp)) または高フレーム レート ([gRPC 拡張プロセッサ](/azure/media-services/live-video-analytics-edge/analyze-live-video-use-your-grpc-model-quickstart?pivots=programming-language-csharp)) でライブ ビデオ分析を行う例を紹介した、いずれかのクイックスタートを体験してみましょう。

上級ユーザーの方は、Live Video Analytics 用の [Jupyter ノートブック](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md)のサンプルをいくつか確認してください。 これらのノートブックでは、次の操作について **メディア グラフ拡張機能** の詳しい手順を説明しています。

* 拡張サービスの Docker コンテナー イメージを作成する方法
* 拡張サービスを Live Video Analytics コンテナーと共にコンテナーとしてデプロイする方法
* 拡張クライアントで Live Video Analytics メディア グラフを使用し、拡張エンドポイント (HTTP/gRPC) を指定する方法
