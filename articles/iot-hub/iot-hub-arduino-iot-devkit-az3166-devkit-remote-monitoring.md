---
title: MXChip IoT DevKit を Azure IoT Hub リモート監視に接続する
description: このチュートリアルでは、IoT DevKit AZ3166 でのセンサーの状態を Azure IoT リモート監視ソリューション アクセラレータに送信する方法を説明します。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785018"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>MXChip IoT DevKit を Azure IoT リモート監視ソリューション アクセラレータに接続する

このチュートリアルでは、DevKit でサンプル アプリを実行して、センサー データを Azure IoT リモート監視ソリューション アクセラレータに送信する方法を説明します。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 [Arduino 向け Visual Studio Code 拡張機能](https://aka.ms/arduino)を使用して開発できます。 また、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](./iot-hub-arduino-iot-devkit-az3166-get-started.md)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備

有効な Azure サブスクリプション 持っていない場合は、次の 2 つの方法のいずれかを使用して登録できます。

* [30 日間の無料試用版 Microsoft Azure アカウント](https://azure.microsoft.com/free/)をアクティブにする

* MSDN または Visual Studio サブスクライバーの場合、[Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を要求する

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Azure IoT リモート監視ソリューション アクセラレータを作成する

1. [Azure IoT ソリューション アクセラレータのサイト](https://www.azureiotsolutions.com/)に移動して、 **[新しいソリューションの作成]** をクリックします。

   ![Azure IoT ソリューション アクセラレータの種類の選択](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > 既定では、このサンプルでは、1 つの IoT リモート監視ソリューション アクセラレータが作成された後、S2 IoT Hub が作成されます。 この IoT ハブを膨大な数のデバイスと共に使用しない場合、S2 から S1 にダウングレードして IoT リモート監視ソリューション アクセラレータを削除することを強くお勧めします。これにより、必要なくなった際に関連の IoT Hub も削除できます。 

2. **[リモート監視]** を選択します。

3. ソリューション名を入力し、サブスクリプションとリージョンを選択して、 **[ソリューションの作成]** をクリックします。 ソリューションのプロビジョニングにはしばらく時間がかかる場合があります。
  
   ![ソリューションを作成する](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. プロビジョニングが完了したら、 **[起動]** をクリックします。 一部のシミュレートされたデバイスが、プロビジョニング処理中にソリューション用に作成されます。 **[デバイス]** をクリックしてチェック アウトします。

   ![ダッシュボード](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![コンソール](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. **[デバイスの追加]** をクリックします。

6. **[カスタム デバイス]** で **[新規追加]** をクリックします。
  
   ![新しいデバイスを追加する](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. **[デバイス ID を自分で定義する]** をクリックして、「`AZ3166`」と入力し、 **[作成]** をクリックします。
  
   ![ID を使用したデバイスの作成](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. **IoT Hub ホスト名** をメモし、 **[完了]** をクリックします。

## <a name="open-the-remotemonitoring-sample"></a>RemoteMonitoring サンプルを開く

1. 接続されている場合は、コンピューターから DevKit を切断します。

2. VS Code を起動します。

3. DevKit をコンピューターに接続します。 VS Code により DevKit が自動的に検出され、次のページが開きます。

   * DevKit 概要ページ
   * [Arduino Examples]\(Arduino の例\): DevKit の使用を開始するためのハンズオン サンプル。

4. 左側の **[Arduino Examples]\(Arduino の例\)** セクションを展開し、 **[Examples for MXCHIP AZ3166] > [AzureIoT]** を参照して、 **[RemoteMonitoring]** を選択します。 プロジェクト フォルダーを含む新しい VS Code ウィンドウが開きます。

   > [!NOTE]
   > ウィンドウを偶然閉じた場合は、再度開くことができます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、 **[Arduino:Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-required-azure-services"></a>必要な Azure サービスのプロビジョニング

ソリューション ウィンドウで表示されたテキスト ボックスに「`task cloud-provision`」と入力し、`Ctrl+P` キー (macOS: `Cmd+P` キー) を使用してタスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![Azure リソースをプロビジョニングする](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

1. `Ctrl+P` (macOS: `Cmd + P`) を使用し、「**task config-device-connection**」と入力します。

2. ターミナルにより、`task cloud-provision` 手順から取得される接続文字列を使用するかどうか確認するメッセージが表示されます。 [新規作成...] をクリックして、独自のデバイスの接続文字列を入力することもできます。

3. ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に、DevKit の ID と "構成" が表示されます。

   ![接続文字列の入力](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. `task config-device-connection` が完了したら、`F1` をクリックして VS Code コマンドを読み込み、`Arduino: Upload` を選択します。 VS Code が、Arduino スケッチの確認とアップロードを開始します。
  
   ![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit が再起動され、コードの実行が開始されます。

## <a name="test-the-project"></a>プロジェクトのテスト

サンプル アプリが実行されると、DevKit はセンサー データを WiFi 経由で Azure IoT リモート監視ソリューション アクセラレータに送信します。 結果を表示するには、次の手順に従います。

1. Azure IoT リモート監視ソリューション アクセラレータに移動し、 **[DASHBOARD]** をクリックします。

2. リモート監視ソリューション コンソールに DevKit センサーの状態が表示されます。

   ![Azure IoT リモート監視ソリューション アクセラレータのセンサー データ](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>デバイス ID の変更

ハードコードされた **AZ3166** をコード内のカスタマイズされたデバイス ID に変更したい場合は、[リモート監視の例](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2)に示されているコード行を変更します。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT 開発者キットの FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルを使用して Microsoft までお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次のステップ

ここでは、DevKit デバイスを Azure IoT リモート監視ソリューション アクセラレータに接続して、センサー データを視覚化する方法を説明しました。推奨する次の手順は、以下のとおりです。

* [Azure IoT ソリューション アクセラレータの概要](/azure/iot-suite/)

* [IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/)