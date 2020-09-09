---
ms.openlocfilehash: ce5611a92e9899d64ff2117385af008c37c22c5e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682320"
---
このクイックスタートの前提条件の一環として、サンプル コードがフォルダーにダウンロードされています。 サンプル コードを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    展開テンプレートは、エッジ デバイスの配置マニフェストを参照します。マニフェストでは、一部のプロパティに変数が使用されています。 *.env* ファイルには、それらの変数の値が格納されています。
1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。
    * ***operations.json*** - プログラムで実行する操作のリストです。
    * ***main.py*** - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、その[ダイレクト メソッド](../../../direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。 
        * 一時停止することで、プログラムからの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできます。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

1. *operations.json* ファイルを編集します。
    * グラフ トポロジへのリンクを変更します。

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * `GraphInstanceSet` で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * ビデオ ファイルを指すように RTSP URL を編集します。

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * `GraphTopologyDelete` で、名前を編集します。

        `"name": "EVRToFilesOnMotionDetection"`
