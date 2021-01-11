---
title: Live Video Analytics on IoT Edge のクォータ - Azure
description: この記事では、Live Video Analytics on IoT Edge のクォータと制限について説明します。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 32a24079b36655bfdacd25b07d419009f5012507
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750226"
---
# <a name="quotas-and-limitations"></a>クォータと制限

この記事では Live Video Analytics on IoT Edge モジュールのクォータと制限を列挙します。

## <a name="maximum-period-of-disconnected-use"></a>切断された状態での最大使用期間

エッジ モジュールでは、ネットワーク接続が一時的に失われることがあります。 モジュールの切断状態が 36 時間を超えている場合は、実行されていたすべてのグラフ インスタンスが非アクティブ化され、さらなるダイレクト メソッド呼び出しがブロックされます。

エッジ モジュールを動作状態に戻すには、ネットワーク接続を復元し、モジュールが Azure Media Service アカウントと正常に通信できる必要があります。

## <a name="maximum-number-of-graph-instances"></a>グラフ インスタンスの最大数

モジュールあたり最大 1000 個のグラフ インスタンス (GraphInstanceSet を使用して作成される) を使用できます。

## <a name="maximum-number-of-graph-topologies"></a>グラフ トポロジの最大数

モジュールあたり最大 50 個のグラフ トポロジ (GraphTopologySet を使用して作成される) を使用できます。

## <a name="limitations-on-graph-topologies-at-preview"></a>プレビュー時のグラフ トポロジに関する制限事項

プレビュー リリースでは、メディア グラフ トポロジで相互に接続できる各種ノードに制限があります。

* RTSP ソース
   * グラフ トポロジごとに 1 つの RTSP ソースのみが許可されます。
* フレーム レート フィルター プロセッサ
   * RTSP ソースまたはモーション検出プロセッサのすぐ下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサの下流で使用することはできません。
   * モーション検出プロセッサの上流に存在することはできません。
* HTTP 拡張プロセッサ
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
* gRPC 拡張プロセッサ
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
* モーション検出プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
   * HTTP または gRPC 拡張プロセッサの下流で使用することはできません。
* シグナル ゲート プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
* 資産シンク 
   * RTSP ソースまたはシグナル ゲート プロセッサのすぐ下流にある必要があります。
* ファイル シンク
   * シグナル ゲート プロセッサのすぐに下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサまたはモーション検出プロセッサのすぐ下流に存在することはできません。
* IoT Hub シンク
   * IoT Hub ソースのすぐ下流に存在することはできません。

モーション検出プロセッサ ノードとフィルター レート プロセッサ ノードの両方が使用されている場合、それらは RTSP ソース ノードにつながる同じノード チェーン内に存在する必要があります。

## <a name="limitations-on-media-service-operations-at-preview"></a>プレビュー時の Media Service 操作に関する制限事項

プレビュー リリースの時点では、Live Video Analytics on IoT Edge では次がサポートされていません。

* あるサブスクリプションから別のサブスクリプションにメディア サービス アカウントを中断なく移行する機能。
* メディア サービス アカウントで複数のストレージ アカウントを使用する機能。
* 再起動せずに、モジュールの目的のプロパティ内のサービス プリンシパル情報を動的に変更する機能。

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
