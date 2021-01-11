---
title: 独自の HTTP モデルを使用してライブ ビデオを分析する - Azure
description: このクイックスタートでは、コンピューター ビジョンを適用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析します。
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 5fd7f1a7db323e3aa7971c1ced6fc588d6e60e37
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684183"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>クイック スタート:独自の HTTP モデルを使用してライブ ビデオを分析する

このクイックスタートでは、Live Video Analytics on IoT Edge を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 コンピューター ビジョン モデルを適用して、オブジェクトを検出する方法を確認します。 ライブ ビデオ フィード内のフレームのサブセットが、推論サービスに送信されます。 その結果が IoT Edge Hub に送信されます。 

このクイックスタートでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。 これは、C# で記述されたサンプル コードに基づいており、クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を基に構築されています。 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>概要

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>メディア グラフを作成してデプロイする

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>結果を解釈する

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このクイックスタートを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

* [セキュリティで保護されたバージョンの YoloV3 モデル](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md)を試し、IoT Edge デバイスにデプロイします。 

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、AMD64 または x64 Linux デバイスを使用してください。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../iot-edge/how-to-install-iot-edge-linux.md)に関するページの手順を参照できます。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../iot-edge/quickstart-linux.md)」の手順に従って、デバイスを Azure IoT Hub に登録します。
