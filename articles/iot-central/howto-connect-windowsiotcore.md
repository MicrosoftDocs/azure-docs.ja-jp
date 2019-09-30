---
title: Windows IoT Core デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法を学習します。
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3513dc0a1928168d6313e9d49a8f3d5d27aca781
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066346"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Windows IoT Core デバイスを Azure IoT Central アプリケーションに接続する

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

この記事では、デバイス開発者として、Windows IoT Core デバイスを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

- **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。

- Windows 10 IoT Core オペレーティング システムを実行しているデバイス。 詳細については、[Windows 10 IoT Core デバイスの設定](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)に関するページを参照してください。

- [Node.js](https://nodejs.org/) バージョン 8.0.0 以降がインストールされた開発用コンピューター。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。

## <a name="the-sample-devkits-application"></a>サンプル Devkit アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Windows IoT Core** デバイス テンプレートが含まれています。

- デバイスのテレメトリ測定:**湿度**、**温度**、**圧力**。
- **ファン速度**を制御するための設定。
- デバイス プロパティ **Die number** とクラウド プロパティ **Location**。

デバイス テンプレートの構成について詳しくは、「[Windows IoT Core デバイス テンプレートの詳細](#device-template-details)」をご覧ください。

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションでは、**Device Explorer** ページを使用して **Windows 10 IoT Core** デバイス テンプレートから実デバイスを追加します。 デバイス接続の詳細 ( **[スコープ ID]** 、 **[デバイス ID]** 、 **[主キー]** ) をメモします。

## <a name="prepare-the-device"></a>デバイスの準備

デバイスを IoT Central に接続するには、接続文字列が必要です。

1. `dps-keygen` コマンドライン ユーティリティを使用して接続文字列を生成します。

    [キー ジェネレーター ユーティリティ](https://github.com/Azure/dps-keygen)をインストールするには、次のコマンドを実行します。

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 接続文字列を生成するには、先ほど書き留めておいた接続の詳細を使用して、次のコマンドを実行します。

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. `dps-keygen` 出力からの接続文字列をデバイス コードで使用するためにコピーします。

デバイスコードで接続文字列にアクセスするには、Windows 10 IoT Core デバイスの `C:\Data\Users\DefaultAccount\Documents\` フォルダーにある **connection.string.iothub** という名前のファイルに保存します。

**connection.string.iothub** をデスクトップ コンピューターからお使いのデバイス上の `C:\Data\Users\DefaultAccount\Documents\` フォルダーにコピーするには、[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) を使用できます。

1. Web ブラウザーを使用して、お使いのデバイス上の Windows Device Portal に移動します。
1. お使いのデバイス上のファイルを参照するには、 **[アプリ] > [File explorer]\(ファイル エクスプローラー\)** を選択します。
1. **User Folders\Documents** に移動します。 次に **connection.string.iothub** ファイルをアップロードします。

    ![接続文字列のアップロード](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>デプロイして実行する

お使いのデバイスにサンプル アプリケーションをデプロイして実行するには、[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) を使用できます。

1. Web ブラウザーを使用して、お使いのデバイス上の Windows Device Portal に移動します。
1. **Azure IoT Hub クライアント** アプリケーションをデプロイして実行するには、 **[アプリ] > [Quick-run samples]\(サンプルのクイック実行\)** を選択します。 次に **[Azure IoT Hub Client]\(Azure IoT Hub クライアント\)** を選択します。
1. **[Deploy and run]\(デプロイして実行\)** を選択します。

    ![デプロイして実行する](media/howto-connect-windowsiotcore/quick-run.png)

数分後、お使いの IoT Central アプリケーションのデバイスからテレメトリを表示できます。

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) には、デバイスのトラブルシューティングに使用できるツールが含まれています。

- **[Apps Manager]** ページを使用して、デバイスで実行されているアプリを制御できます。
- デバイスに接続されているモニターがない場合、 **[デバイスの設定]** ページを使用して、デバイスからスクリーン ショットをキャプチャできます。 例:

    ![アプリのスクリーンショット](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>ソース コードをダウンロードする

クライアント アプリケーションのソース コードを調査したり変更したりする場合は、それを [Windows-iotcore-samples GitHub リポジトリ](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)からダウンロードできます。

## <a name="device-template-details"></a>デバイス テンプレートの詳細

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Windows IoT Core** デバイス テンプレートが含まれています。

### <a name="telemetry-measurements"></a>テレメトリ測定

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>設定

数値設定

| Display name | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>properties

| 種類            | Display name | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| Text            | Location     | location   | 該当なし       |

## <a name="next-steps"></a>次の手順

ここでは、Windows IoT Core デバイスをご利用の Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は、独自の IoT デバイス用に[カスタム デバイス テンプレートを設定する](howto-set-up-template.md)方法を学習することです。
