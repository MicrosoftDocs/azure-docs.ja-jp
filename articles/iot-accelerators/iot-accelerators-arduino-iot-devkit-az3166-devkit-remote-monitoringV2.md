---
title: IoT DevKit をリモート監視ソリューション アクセラレータに接続する - Azure | Microsoft Docs
description: このハウツー ガイドでは、テレメトリを IoT DevKit AZ3166 デバイス上のセンサーから監視および視覚化するためのリモート監視ソリューション アクセラレータに送信する方法について説明します。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 3551d088c1d02715bf9ace09d7eb0048bc10111e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105252"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>IoT DevKit デバイスをリモート監視ソリューション アクセラレータに接続する

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このハウツー ガイドでは、IoT DevKit デバイス上でサンプル アプリケーションを実行する方法を示します。 このサンプル コードでは、DevKit デバイス上のセンサーからお使いのソリューション アクセラレータにテレメトリが送信されます。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 Visual Studio Code で [Azure IoT Device Workbench](https://aka.ms/iot-workbench) または [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張機能パックを使用して、これを開発することができます。 [プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)に、IoT ソリューションをプロトタイプ化するのに役立つサンプル アプリケーションが含まれています。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、最初に次のタスクを行います。

* 「[IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)」の手順に従って、DevKit を準備します。

## <a name="open-sample-project"></a>サンプル プロジェクトを開く

VS Code でリモート監視サンプルを開くには:

1. IoT DevKit がお使いのコンピューターに接続されていないことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、**[Azure IoT Device Workbench:Open Examples...]\(Azure IoT Device Workbench: 例を開く...\)** を入力して選択します。次に、**[IoT DevKit]** をボードとして選択します。

1. **[リモート監視]** を見つけ、**[Open Sample]\(サンプルを開く\)** をクリックします。 新しい VS Code ウィンドウが開いて、プロジェクト フォルダーが表示されます。

   ![IoT Workbench、リモート監視サンプルの選択](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>デバイスを構成する

お使いの DevKit デバイス上に IoT Hub デバイス接続文字列を構成するには:

1. IoT DevKit を**構成モード**に切り替えます。

    * **A** ボタンを押しながら、
    * **リセット** ボタンを押して離します。

1. 画面に、DevKit の ID と `Configuration` が表示されます。

    ![IoT DevKit 構成モード](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. **F1** キーを押してコマンド パレットを開き、**[Azure IoT Device Workbench: デバイス設定の構成...] > [Config Device Connection String]\(デバイス接続文字列の構成\)** を入力して選択します。

1. コピーしておいた接続文字列を貼り付け、**Enter** キーを押してデバイスを構成します。

## <a name="build-the-code"></a>コードのビルド

デバイス コードをビルドしてアップロードするには:

1. `F1` キーを押してコマンド パレットを開き、**[Azure IoT Device Workbench: Upload Device Code]\(Azure IoT Device Workbench: デバイス コードのアップロード\)** を入力して選択します。

1. VS Code によってコードがコンパイルされ、DevKit デバイスにアップロードされます。

    ![IoT Workbench:デバイス - > アップロード](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit デバイスが再起動し、アップロードしたコードが実行されます。

## <a name="test-the-sample"></a>サンプルをテストする

DevKit デバイスにアップロードしたサンプル アプリケーションが動作していることを確認するには、次の手順を完了します。

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>リモート監視ソリューションに送信されたテレメトリを表示する

サンプル アプリが実行されているときは、DevKit デバイスのセンサー データから Wi-fi 経由でソリューション アクセラレータにテレメトリが送信されます。 テレメトリを表示するには:

1. ソリューション ダッシュボードに移動し、**[デバイス エクスプローラー]** をクリックします。

1. DevKit デバイスのデバイス名をクリックします。 右側のタブで、DevKit から送信されるテレメトリをリアルタイムで確認できます。

    ![Azure IoT Suite でのセンサー データ](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>DevKit デバイスを制御する

リモート監視ソリューション アクセラレータを使用して、デバイスをリモートで制御できます。 サンプル コードでは 3 つのメソッドが実装されます。**[デバイス エクスプローラー]** ページでデバイスを選択すると表示される **[メソッド]** セクションでこれらを確認できます。

![IoT DevKit メソッド](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

いずれかの DevKit LED の色を変更するには、**LedColor** メソッドを使用します。

1. デバイスの一覧からデバイス名を選択し、**[ジョブ]** をクリックします。

    ![ジョブを作成する](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. 次の値を使用してジョブを構成し、**[適用]** をクリックします。

   * ジョブを選択:**Run メソッド**
   * メソッド名:**LedColor**
   * ジョブ名:**ChangeLedColor**

     ![ジョブの設定](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. 数秒後、DevKit 上の RGB LED (A ボタンの下) の色が変化します。

    ![IoT DevKit の赤い LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルに進む場合は、リモート監視ソリューション アクセラレータをデプロイしたままにしておきます。

ソリューション アクセラレータが不要になった場合は、[プロビジョニングされたソリューション] ページでそれを選択し、[ソリューションの削除] をクリックして削除してください。

![ソリューションを削除する](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用して Microsoft までお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

ここでは、DevKit デバイスをお使いのリモート監視ソリューション アクセラレータに接続する方法を説明しました。推奨する次の手順は、以下のとおりです。

* [Azure IoT ソリューション アクセラレータの概要](https://docs.microsoft.com/azure/iot-accelerators/)
* [UI のカスタマイズ](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit を Azure IoT Central アプリケーションに接続する](../iot-central/howto-connect-devkit.md)