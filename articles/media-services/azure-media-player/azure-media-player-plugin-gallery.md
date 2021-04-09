---
title: Azure Media Player プラグイン ギャラリー
description: この記事では、Azure Media Player で利用できるプラグインの一覧を掲載しています。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99089344"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player プラグイン ギャラリー #

## <a name="plugins"></a>プラグイン ##

| プラグイン名                         | デモ URL                    | ソース コード                | 説明    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| 追加機能                 | | | |
| **新しい情報!** AMP360Video                | [デモ](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | このプラグインを使用すると、VR 対応デバイスまたはデスクトップの AMP で 360 ビデオを視覚化できます。 詳細なドキュメントは、[こちら](https://doc.babylonjs.com/extensions/amp360video)でご覧いただけます。 |
|  Sprite Tip                         | [デモ](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Services (AMS) Media Encoder Standard (MES) から生成されたビデオ サムネイル画像スプライトのタイムライン レンダリングに使用される Azure Media Player (AMP) プラグイン。 |
| Diagnostics Overlay                 | [デモ](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | このプラグインには、以下のものが表示されます。すべての主要パラメーター、ビデオの統計情報、ビデオ再生ライフサイクルのすべてのイベント、および保護されている場合は DRM 保護情報 (キー ID、ライセンスの取得 URL など)。                                                                                                                                                                      |
| Frame rate and Timecode calculator | 使用可能なデモはありません | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | このプラグインは、最初の MPEG-DASH ビデオ フラグメントの `tfhd`/`trun` MP4 ボックスに基づいてビデオのフレーム レートを計算し、MPEG-DASH クライアント マニフェストからタイム スケール値を解析します。また、プレーヤーから特定の絶対時間のタイムコードを生成する手段を備え、また、そのタイムコードからプレーヤーの絶対時間を得ることもできます。 |
| <strike>Playback Speed</strike>                      | [デモ](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 閲覧者は、このプラグインを使用してビデオの速度を制御できます。 *バージョン v2.0.0 以上の AMP では、そのままでこの機能が利用できますが、既定では無効になっていることに注意してください。* 有効にする方法については、[こちら](https://github.com/Azure-Samples/azure-media-player-samples)のサンプルを参照してください。 |
| Hover Time Tip                      | [デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 時間に正確なシークを実現するために、進行状況バーにマウス ポインターを合わせたときにタイム ヒントを表示します。 *注意事項: このプラグインは既に AMP に統合されています。* どのようにプログラムされているのか興味がある方はご覧ください。                                                                                                                 |
| Title Overlay                       | [デモ](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | ビデオ タイトル (構成可能) を画面にオーバーレイします。 |
| Timeline Markers                    | [デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | 時間の配列を受け取って、進行状況バー上の対応する時間位置に小さなマーカーをオーバーレイします。 |
| Analytics                           | | | |
| Application Insights                | [ブログ記事](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | プレーヤーのメトリックを追跡し、それを Power BI に転送することで、閲覧者のプレーヤー エクスペリエンスを直感的かつグラフィカルに表現するプラグイン。 |
| Google アナリティクス                    | 該当なし                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Azure Media Player 用の Google アナリティクス プラグイン |
| 診断                         | | | |
| Diagnostics Output                  | [デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | このプラグインは、プレーヤーからさまざまな診断情報を出力します。プラグインの動作を確認するには、デモ リンクにアクセスして、JavaScript コンソールを開いてください。 |
| 簡単操作                      | | | |
| 拡大                             | [デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | 閲覧者がコンテンツを拡大表示できるよう、ドラッグに対応したズームイン スケールをプレーヤー画面上に表示します。 |
| Live Captions                       | [Azure ブログ記事](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/)、[SubPly 記事](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | 該当なし | *詳細については、記事を参照してください。* Azure Media Player 向けのライブ キャプションを実現するためのエンド ツー エンドのワークフローが組み込まれたプラグイン。このソリューションの詳細については、左端のリンクをクリックして SubPly のサイトにアクセスしてください |
| Hot Keys                            | <strike>[デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | 閲覧者は、F (全画面表示)、M (ミュート)、方向キー (進行状況バーの制御) など、一般的なプラグインの組み合わせでプレーヤーのさまざまな機能を制御できます。 *注意事項: このプラグインは既に AMP に統合されていますが、参考としてご覧ください。* |
| ソーシャル                              | | | |
| 共有                               | [デモ](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | プレーヤーのコントロール バーに共有ボタンを追加します。閲覧者は、Facebook、Twitter、Linkedin を通じて、視聴しているビデオを友だちに共有できます。 |

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
