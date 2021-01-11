---
title: ビデオ分析 - 物体とモーションの検出 Azure IoT Central アプリケーション テンプレートをデプロイする方法
description: このガイドでは、ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートを使用して Azure IoT Central アプリケーションをデプロイする手順をまとめています。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719597"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイする方法

*ビデオ分析 - 物体とモーションの検出* アプリケーションの主要なコンポーネントの概要については、[物体とモーションの検出ビデオ分析アプリケーション アーキテクチャ](architecture-video-analytics.md)に関するページを参照してください。

## <a name="deploy-the-application"></a>アプリケーションをデプロイする

ビデオ分析アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイするには、次の手順を実行します。

1. [Azure IoT Central でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app.md)チュートリアルを実行して、
    - Azure Media Services アカウントを作成する
    - ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートから、IoT Central アプリケーションを作成します。
    - IoT Central アプリケーションでゲートウェイ デバイスを構成します。 ゲートウェイにより、カメラ デバイスがアプリケーションに接続できます。

1. [ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) チュートリアルを実行して、
    - Azure IoT Edge ランタイムがインストールされた Azure VM を作成します。ビデオ分析モジュールをホストするように IoT Edge インストールを準備します。
    - IoT Edge デバイスを IoT Central アプリケーションに接続します。

1. [ビデオ分析アプリケーションの監視と管理](tutorial-video-analytics-manage.md)チュートリアルを実行して、
    - 物体とモーションの検出カメラを IoT Central アプリケーションのゲートウェイに追加します。
    - カメラの処理を開始します。
    - AMS で、キャプチャされたビデオを表示するために、ローカル メディア プレーヤーをインストールします。
    - 検出された物体を示すキャプチャされたビデオを表示します。
    - 整理します。

## <a name="next-steps"></a>次の手順

ビデオ分析アプリケーション テンプレートをデプロイして使用する手順の概要を把握したところで、[Azure IoT Central でビデオ分析アプリケーションを作成する](tutorial-video-analytics-create-app.md)方法に関するページを参照して、始めてください。
