---
title: Live Video Analytics on IoT Edge リリース ノート - Azure
description: このトピックでは、Live Video Analytics on IoT Edge のリリース、機能強化、バグ修正、および既知の問題に関するリリース ノートを提供します。
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954397"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Live Video Analytics on IoT Edge リリース ノート

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

この記事では、次の項目に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

<hr width=100%>

## <a name="january-12-2021"></a>2021 年 1 月 12 日

このリリース タグは、モジュールの 2021 年 1 月の更新用です:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 2 のタグ (live-video-analytics:2) を使用します。 そのため、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。
### <a name="bug-fixes"></a>バグの修正 

* シグナル ゲート プロセッサの `ActivationSignalOffset`、`MinimumActivationTime`、`MaximumActivationTime` のフィールドが誤って必須のプロパティとして設定されました。 これらは現在、**任意** のプロパティです。
* 一部のリージョンにデプロイされたとき、IoT Edge モジュールで Live Video Analytics をクラッシュさせる使用バグを修正しました。

<hr width=100%>

## <a name="december-14-2020"></a>2020 年 12 月 14 日
このリリースは、Live Video Analytics on IoT Edge のパブリック プレビューの更新リリースです。 リリース タグは、次のとおりです。

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>モジュールの更新
* 複数の HTTP 拡張プロセッサおよび gRPC 拡張機能プロセッサをグラフトポロジごとに使用するためのサポートが追加されました。
* [シンク ノードのディスク領域管理](upgrading-lva-module.md#disk-space-management-with-sink-nodes)のサポートが追加されました。
* `MediaGraphGrpcExtension` ノードで、1 つの gRPC サーバー内で複数の AI モデルを使用するための [extensionConfiguration](grpc-extension-protocol.md) プロパティがサポートされるようになりました。
* [Prometheus 形式](https://prometheus.io/docs/practices/naming/)で Live Video Analytics モジュール メトリックを収集できるようになりました。 [Azure Monitor でメトリックを収集し、表示する方法の詳細はこちらで](monitoring-logging.md#azure-monitor-collection-via-telegraf)ご覧ください。 
* 出力の選択をフィルター処理する機能が追加されました。 `outputSelectors` を使用して、任意のグラフ ノードに **オーディオのみ**、**ビデオのみ**、または **オーディオとビデオの両方** を渡すことができます。 
* フレーム レート フィルター プロセッサは、**非推奨** となります。  
    * グラフ拡張機能のプロセッサ ノード自体でフレーム レート管理を使用できるようになりました。

### <a name="visual-studio-code-extension"></a>Visual Studio Code 拡張機能
* [Live Video Analytics on IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) - LVA メディア グラフの管理に役立つ Visual Studio Code 拡張機能がリリースされました。 この拡張機能は **LVA 2.0 モジュール** と連携し、滑らかに操作できる使いやすいグラフィカル インターフェイスでメディア グラフを編集および管理できます。
## <a name="september-22-2020"></a>2020 年 9 月 22 日

このリリース タグは、モジュールの 2020 年 9 月の更新用です:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (live-video-analytics:1) を使用します。 そのため、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

### <a name="module-updates"></a>モジュールの更新

* Cognitive Services の[空間分析](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview) (プレビュー) モジュールと統合するために、新しいグラフ拡張機能ノード [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) を利用できます。
* Linux ARM64 デバイスのサポートの追加 - このようなデバイスをデプロイするには、[手動の手順](deploy-iot-edge-device.md)を使用します。

### <a name="documentation-updates"></a>ドキュメントの更新

* Azure Stack Edge デバイスで Live Video Analytics on IoT Edge を使用するための[手順](deploy-azure-stack-edge-how-to.md)が公開されました。
* [Custom Vision サービス](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)を使用してシナリオ固有のコンピューター ビジョン モデルを開発し、それを使用してリアルタイムで[ライブ ビデオを分析する](custom-vision-tutorial.md)方法に関する新しいチュートリアル。

<hr width=100%>

## <a name="august-19-2020"></a>2020 年 8 月 19 日

このリリース タグは、モジュールの 2020 年 8 月の更新用です:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (live-video-analytics:1) を使用します。 そのため、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

### <a name="module-updates"></a>モジュールの更新

* gRPC フレームワークを使用して、IoT Edge の Live Video Analytics とカスタム拡張機能間でデータ コンテンツ転送のパフォーマンスを向上させることができるようになりました。 [クイックスタート](analyze-live-video-use-your-grpc-model-quickstart.md)を参照して使用を開始してください。
* Live Video Analytics のリージョン デプロイの範囲が広くなり、クラウド サービスのみが更新されました。  
* Live Video Analytics を世界各地の 25 の追加リージョンで使用できるようになりました。 使用可能なすべてのリージョンの[一覧](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)を次に示します。  
* クイック スタートの[セットアップ](https://aka.ms/lva-edge/setup-resources-for-samples)でも、新しいリージョンがサポートされるように更新されました。
    * 既にリソースをセットアップしているユーザーには、アクションが呼び出されません。

### <a name="bug-fixes"></a>バグの修正 

* セットアップ スクリプトで非推奨の Azure 拡張機能の使用を削除

<hr width=100%>

## <a name="july-13-2020"></a>2020 年 7 月 13 日

このリリース タグは、モジュールの 2020 年 7 月の更新用です:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> クイックスタートおよびチュートリアルでは、配置マニフェストで 1 のタグ (live-video-analytics:1) を使用します。 そのため、このようなマニフェストを再配置するだけで、エッジとデバイスでモジュールが更新されます。

### <a name="module-updates"></a>モジュールの更新

* シグナル ゲート プロセッサ ノードの下流に資産シンク ノードとファイル シンク ノードを持つグラフ トポロジを作成できるようになりました。 例については、[トポロジ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)に関する記事を参照してください。

### <a name="bug-fixes"></a>バグの修正

* 必要なプロパティの検証機能の強化

<hr width=100%>

## <a name="june-1-2020"></a>2020 年 6 月 1 日

このリリースは、Live Video Analytics on IoT Edge の最初のパブリック プレビュー リリースです。 リリース タグは、次のとおりです。

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>サポートされる機能

* AI モジュールを使用してライブ ビデオ ストリームを分析し、必要に応じて、エッジ デバイスまたはクラウドにビデオを記録する
* IoT Edge によって[サポートされている ](../../iot-edge/support.md)Linux AMD64 オペレーティング システム上で使用する
* Azure portal または Visual Studio Code を使用して、IoT Hub 経由でモジュールをデプロイおよび構成する
* 次のダイレクト メソッド呼び出しを使用して、リモートまたはローカルに[グラフ トポロジーおよびグラフ インスタンス](media-graph-concept.md#media-graph-topologies-and-instances)を管理する

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
