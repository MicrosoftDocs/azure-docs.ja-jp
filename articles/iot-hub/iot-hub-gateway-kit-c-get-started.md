---
title: "SensorTag デバイスと Azure IoT Gateway - 作業開始 | Microsoft Docs"
description: "IoT ゲートウェイ スタート キットを使用して Azure IoT ハブを作成し、SensorTag とゲートウェイを IoT ハブに接続します"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure IoT ハブ, IoT ゲートウェイ, モノのインターネットの使用, IoT Toolkit"
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: dd14d137c23fbccb3616b60db8a63e668ae7ea80
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>IoT ゲートウェイ スタート キットと SensorTag の使用

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [シミュレート デバイス](iot-hub-gateway-kit-c-sim-get-started.md)

このチュートリアルでは、[IoT Gateway スタート キット](https://aka.ms/gateway-kit)の操作の基本を学習することから始めます。 Wind River Linux を実行中の Intel NUC と [TI SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main) を操作します。 Azure IoT Hub を使用して、デバイスをクラウドにシームレスに接続する方法について学習します。

***
**キットを持っていない場合**: [こちら](https://aka.ms/gateway-kit)をクリックしてください。 **SensorTag を持っていない場合:** [シミュレート デバイスで開始する](iot-hub-gateway-kit-c-sim-get-started.md)か、[SensorTag を購入](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)してください。
***

## <a name="lesson-1-configure-your-nuc"></a>レッスン 1: NUC を構成する
![レッスン&1; のエンド ツー エンド ダイアグラム](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

このレッスンでは、キットの Intel NUC (Next Unit of Computing) を Azure IoT ゲートウェイとして設定し、Azure IoT Gateway SDK パッケージを NUC にインストールし、サンプル アプリを実行してゲートウェイの機能を検証します。

*推定所要時間: 15 分*

「[IoT ゲートウェイとしての Intel NUC のセットアップ](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)」に移動する

## <a name="lesson-2-create-your-iot-hub"></a>レッスン 2: IoT Hub を作成する
![レッスン&2; のエンド ツー エンド ダイアグラム](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

このレッスンでは、ツールとソフトウェアをホスト コンピューターにインストールします。 その後、無料の Azure アカウントを作成し、Azure IoT ハブをプロビジョニングし、IoT ハブに最初のデバイスを作成します。

このレッスンを開始する前に、レッスン 1 を完了してください。

### <a name="get-the-tools"></a>ツールの入手
ホスト コンピューターにツールとソフトウェアをインストールします。

*推定所要時間: 20 分*

「[ツールの入手](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)」に移動する

### <a name="create-an-iot-hub-and-register-your-device"></a>IoT ハブを作成してデバイスを登録する
リソース グループを作成し、最初の Azure IoT ハブをプロビジョニングし、Azure CLI を使用して Azure IoT ハブに最初のデバイスを追加します。

*推定所要時間: 10 分*

「[IoT ハブを作成してデバイスを登録する](iot-hub-gateway-kit-c-lesson2-register-device.md)」に移動する

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>レッスン 3: SensorTag からメッセージを受信し、IoT ハブからメッセージを読み取る
このレッスンでは、スクリプトを使用して、ゲートウェイでの BLE サンプル アプリケーションの構成と実行を自動化します。 このようなアプリケーションでは、モジュールのコレクションを使用して、データの集計と変換やコマンドの処理、任意の数の関連タスクを実行します。 モジュールは、メッセージ ブローカーを使用して相互に通信します。 サンプル アプリケーションには、BLE モジュールと IoT ハブ モジュールがあります。 BLE モジュールは、BLE SensorTag からデータを受信します。 IoT ハブ モジュールは、受信したデータをパッケージ化し、Azure IoT Gateway SDK に用意されているゲートウェイ フレームワークを通して IoT ハブに送信します。

![レッスン 3 のエンド ツー エンドのダイアグラム](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>BLE サンプル アプリケーションの構成と実行
SensorTag とゲートウェイ間の接続をセットアップします。 その後、構成を終了し、BLE サンプル アプリケーションを実行します。

*推定所要時間: 15 分*

「[BLE サンプル アプリの構成と実行](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)」に移動する

### <a name="read-messages-from-your-iot-hub"></a>IoT ハブからのメッセージの読み取り
IoT ハブからメッセージを読み取るには、ホスト コンピューターでサンプル コードを実行します。

*推定所要時間: 15 分*

「[IoT ハブからのメッセージの読み取り](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)」に移動する

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>レッスン 4: メッセージを Azure Table Storage に保存する
受信メッセージを IoT ハブから取得して Azure Table Storage に書き込む Azure Function App を作成します。

![レッスン 4 のエンド ツー エンドのダイアグラム](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure Function App と Azure ストレージ アカウントの作成
Azure Resource Manager テンプレートを使用して、Azure Function App と Azure ストレージ アカウントを作成します。

*推定所要時間: 10 分*

「[Azure Function App と Azure ストレージ アカウントの作成](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)」に移動する

### <a name="read-messages-persisted-in-azure-table-storage"></a>Azure Table Storage に保持されたメッセージの読み取り
Azure Table Storage に書き込まれるゲートウェイからクラウドへのメッセージを監視します。

*推定所要時間: 5 分*

「[Azure Table Storage に保持されたメッセージの読み取り](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)」に移動する

## <a name="troubleshooting"></a>トラブルシューティング
レッスン中に問題が発生した場合は、「[トラブルシューティング](iot-hub-gateway-kit-c-troubleshooting.md)」で解決策を探してください。

## <a name="explore-more"></a>さらに詳しく
詳細については、[Intel IoT Gateway Kit developer zone](http://software.intel.com/iot/microsoft-azure) (Intel IoT Gateway Kit デベロッパー ゾーン) を参照してください。
