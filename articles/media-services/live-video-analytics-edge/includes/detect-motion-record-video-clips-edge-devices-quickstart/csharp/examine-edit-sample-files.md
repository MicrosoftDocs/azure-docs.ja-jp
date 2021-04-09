---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486724"
---
このクイックスタートの前提条件の一環として、サンプル コードがフォルダーにダウンロードされています。 サンプル コードを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    展開テンプレートは、エッジ デバイスの配置マニフェストを参照します。マニフェストでは、一部のプロパティに変数が使用されています。 *.env* ファイルには、それらの変数の値が格納されています。
1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。
    * ***c2d-console-app.csproj*** - Visual Studio Code のプロジェクト ファイルです。
    * ***operations.json*** - プログラムで実行する操作のリストです。
    * ***Program.cs*** - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、その[ダイレクト メソッド](../../../direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。 
        * 一時停止することで、プログラムからの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできます。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

1. *operations.json* ファイルを編集します。
    * グラフ トポロジへのリンクを変更します。

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * `GraphInstanceSet` で、前のリンクの値と一致するようにグラフ トポロジの名前を編集します。
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * ビデオ ファイルを指すように RTSP URL を編集します。

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * `GraphTopologyDelete` で、名前を編集します。

        `"name": "EVRToFilesOnMotionDetection"`
