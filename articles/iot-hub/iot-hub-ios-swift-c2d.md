---
title: Azure IoT Hub (iOS) を使用した cloud-to-device メッセージ | Microsoft Docs
description: Azure IoT SDK for iOS を使用して、cloud-to-device メッセージを Azure IoT Hub からデバイスへ送信する方法。
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 1875e3437c13a678532c05b4057126f785b9125c
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147531"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>IoT Hub (iOS) を使用した cloud-to-device メッセージの送信

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 「[Send telemetry from a device to an IoT hub](quickstart-send-telemetry-ios.md)」(デバイスから IoT ハブへのテレメトリの送信) のクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

このチュートリアルでは、次の操作方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイドのメッセージに関するセクション](iot-hub-devguide-messaging.md)を参照してください。

この記事の最後で、2 つの Swift iOS プロジェクトを実行します。

* **sample-device**: [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-ios.md) (デバイスから IoT ハブへのテレメトリの送信) で作成されたサンプル アプリであり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **sample-service**: IoT Hub を介してシミュレート対象デバイス アプリに cloud-to-device メッセージを送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Python、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター](https://www.azure.com/develop/iot)のページを参照してください。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* Azure のアクティブな IoT ハブ。

* [Azure サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)からのコード サンプル。

* iOS SDK の最新バージョンを実行している最新バージョンの [XCode](https://developer.apple.com/xcode/)。 このクイック スタートは、XCode 9.3 と iOS 11.3 でテストされました。

* 最新バージョンの [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。

## <a name="simulate-an-iot-device"></a>IoT デバイスのシミュレート

ここでは、Swift アプリケーションを実行して IoT ハブから cloud-to-device メッセージを受信する iOS デバイスをシミュレートします。 

これは、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-ios.md)」の記事で作成したサンプル デバイスです。 既に実行中の場合、このセクションはスキップしてかまいません。

### <a name="install-cocoapods"></a>CocoaPods のインストール

CocoaPods は、サードパーティ製のライブラリを使用する iOS プロジェクトの依存関係を管理します。

ターミナル ウィンドウで、前提条件としてダウンロードした Azure-IoT-Samples-iOS フォルダーに移動します。 その後、サンプル プロジェクトに移動します。

```sh
cd quickstart/sample-device
```

XCode が終了していることを確認し、次のコマンドを実行して、**podfile** ファイルで宣言されている CocoaPods をインストールします。

```sh
pod install
```

インストール コマンドでは、プロジェクトに必要なポッドをインストールすると共に、依存関係にポッドを使用するように既に構成されている XCode ワークスペース ファイルも作成されます。

### <a name="run-the-sample-device-application"></a>サンプル デバイス アプリケーションの実行

1. デバイスの接続文字列を取得します。 [Azure Portal](https://portal.azure.com) のデバイスの詳細ブレードからこの文字列をコピーするか、次の CLI コマンドを使用して取得できます。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. XCode で、サンプル ワークスペースを開きます。

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT Client Sample** プロジェクトを展開してから、同じ名前のフォルダーを展開します。  

3. XCode で編集するために **ViewController.swift** を開きます。 

4. **connectionString** 変数を検索し、最初の手順でコピーしたデバイス接続文字列で値を更新します。

5. 変更を保存します。 

6. デバイス エミュレーターで、 **[ビルド/実行]** ボタンまたは **Command + r** キーの組み合わせを使用してプロジェクトを実行します。

   ![プロジェクトを実行する](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-ios.md)に関するページで作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続**のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>サービス デバイスのシミュレート

ここでは、IoT ハブを通じて cloud-to-device メッセージを送信する Swift アプリを使用している 2 番目の iOS デバイスをシミュレートします。 この構成は、IoT ハブに接続されている他の iOS デバイスのコントローラーとして機能している 1 台の iPhone または iPad がある IoT シナリオに役立ちます。

### <a name="install-cocoapods"></a>CocoaPods のインストール

CocoaPods は、サードパーティ製のライブラリを使用する iOS プロジェクトの依存関係を管理します。

前提条件でダウンロードした Azure IoT iOS Samples フォルダーに移動します。 その後、サンプル サービス プロジェクトに移動します。

```sh
cd quickstart/sample-service
```

XCode が終了していることを確認し、次のコマンドを実行して、**podfile** ファイルで宣言されている CocoaPods をインストールします。

```sh
pod install
```

インストール コマンドでは、プロジェクトに必要なポッドをインストールすると共に、依存関係にポッドを使用するように既に構成されている XCode ワークスペース ファイルも作成されます。

### <a name="run-the-sample-service-application"></a>サンプル サービス アプリケーションの実行

1. XCode で、サンプル ワークスペースを開きます。

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. **AzureIoTServiceSample** プロジェクトを展開し、同じ名前のフォルダーを展開します。  

3. XCode で編集するために **ViewController.swift** を開きます。 

4. **connectionString** 変数を検索し、「[Get the IoT hub connection string](#get-the-iot-hub-connection-string)」で前にコピーしたサービス接続文字列で値を更新します。

5. 変更を保存します。

6. Xcode で、エミュレーターの設定を、IoT デバイスの実行に使用したのとは異なる iOS デバイスに変更します。 XCode では、同じ種類の複数のエミュレーターを実行できません。

   ![エミュレーター デバイスの変更](media/iot-hub-ios-swift-c2d/change-device.png)

7. デバイス エミュレーターで、 **[ビルド/実行]** ボタンまたは **Command + r** キーの組み合わせを使用してプロジェクトを実行します。

   ![プロジェクトを実行する](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

2 つのアプリケーションを使用して、cloud-to-device メッセージを送受信する準備が整いました。

1. シミュレートされた IoT デバイスで実行されている **iOS App Sample** アプリで、 **[開始]** をクリックします。 アプリケーションは device-to-cloud メッセージの送信を開始しますが、cloud-to-device メッセージのリッスンも開始します。

   ![サンプル IoT デバイス アプリの表示](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. シミュレートされたサービス デバイスで実行されている **IoTHub Service Client Sample** アプリで、メッセージの送信先の IoT デバイスの ID を入力します。 

3. プレーンテキスト メッセージを作成し、 **[送信]** をクリックします。

    [送信] をクリックするとすぐにいくつかのアクションが発生します。 サービス サンプルでは、指定したサービス接続文字列によりアプリがアクセスできる IoT ハブにメッセージが送信されます。 IoT ハブは、デバイス ID を確認し、宛先デバイスにメッセージを送信し、ソース デバイスに受信確認を送信します。 シミュレートされた IoT デバイスで実行されているアプリは、IoT ハブからのメッセージを確認し、最新のメッセージのテキストを画面に出力します。

    出力は次の例のようになります。

   ![cloud-to-device メッセージの表示](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンドツーエンド ソリューションの例については、[Azure IoT ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するドキュメントを参照してください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
