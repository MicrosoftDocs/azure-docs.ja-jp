---
title: チュートリアル:ビデオ分析 - 物体とモーションの検出 Azure IoT Central アプリケーション テンプレートをデプロイする方法
description: チュートリアル - このガイドでは、ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートを使用して Azure IoT Central アプリケーションをデプロイする手順をまとめています。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727466"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>チュートリアル:ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイする方法

*ビデオ分析 - 物体とモーションの検出* アプリケーションの主要なコンポーネントの概要については、[物体とモーションの検出ビデオ分析アプリケーション アーキテクチャ](architecture-video-analytics.md)に関するページを参照してください。

次のビデオでは、"_ビデオ分析 - 物体とモーションの検出アプリケーション テンプレート_" を使用して IoT Central ソリューションをデプロイする方法を段階的に説明しています。

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

この一連のチュートリアルでは、次の方法を学習します。

> [!div class="checklist"]
> * アプリケーションの配置
> * アプリケーションに接続する IoT Edge インスタンスのデプロイ
> * アプリケーションの監視と管理

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお勧めします。 また、7 日間無料の試用版を使用することもできます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="deploy-the-application"></a>アプリケーションをデプロイする

ビデオ分析アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイするには、次の手順を実行します。

1. [Azure IoT Central でのビデオ分析アプリケーションの作成 (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) または [Azure IoT Central でのビデオ分析の作成 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) に関するいずれかのチュートリアルを完了して、以下を行います。
    - Azure Media Services アカウントを作成する
    - ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートから、IoT Central アプリケーションを作成します。
    - IoT Central アプリケーションでゲートウェイ デバイスを構成します。 ゲートウェイにより、カメラ デバイスがアプリケーションに接続できます。

1. 「[ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)](tutorial-video-analytics-iot-edge-vm.md)」または「[チュートリアル: ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)」のいずれかのチュートリアルを完了して、以下を行います。
    - Azure IoT Edge ランタイムがインストールされた Azure VM を作成します。ビデオ分析モジュールをホストするように IoT Edge インストールを準備します。
    - IoT Edge デバイスを IoT Central アプリケーションに接続します。

1. [ビデオ分析アプリケーションの監視と管理](tutorial-video-analytics-manage.md)チュートリアルを実行して、
    - 物体とモーションの検出カメラを IoT Central アプリケーションのゲートウェイに追加します。
    - カメラの処理を開始します。
    - AMS で、キャプチャされたビデオを表示するために、ローカル メディア プレーヤーをインストールします。
    - 検出された物体を示すキャプチャされたビデオを表示します。
    - 整理します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを使い終えたら、次の手順で、作成したリソースをすべて削除することができます。

1. IoT Central アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動します。 次に、 **[削除]** を選択します。
1. Azure portal で、**lva-rg** リソース グループを削除します。
1. ローカル コンピューターで、**amp-viewer** Docker コンテナーを停止します。

## <a name="next-steps"></a>次のステップ

ビデオ分析アプリケーション テンプレートをデプロイして使用する手順の概要を把握したら、

> [!div class="nextstepaction"]
> [Azure IoT Central (YOLO v3) でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app-yolo-v3.md)方法に関するページか、

> [!div class="nextstepaction"]
> [Azure IoT Central (OpenVINO&trade;) でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app-openvino.md)方法に関するページに進んでください。