---
title: C を使用してリモート監視するために Linux デバイスをプロビジョニングする - Azure | Microsoft Docs
description: Linux で実行されている C で記述されたアプリケーションを使用して、デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454498"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>デバイスをリモート監視ソリューション アクセラレータに接続する (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、実デバイスをリモート監視ソリューション アクセラレータに接続する方法を示します。

制限付きのデバイス上で実行される多くの組み込みアプリケーションと同様に、デバイス アプリケーションのためのクライアント コードは C で書かれています。このチュートリアルでは、Ubuntu (Linux) を実行しているコンピューターでアプリケーションを構築します。

デバイスをシミュレートする場合は、「[新しいシミュレートされたデバイスの作成とテスト](iot-accelerators-remote-monitoring-create-simulated-device.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、Ubuntu バージョン 15.04 以降が実行されているデバイスが必要です。 次に進む前に、[Linux 開発環境を設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)します。

## <a name="view-the-code"></a>コードを表示する

このガイドで使用される[サンプル コード](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)は、Azure IoT C SDK GitHub リポジトリで入手できます。

### <a name="download-the-source-code-and-prepare-the-project"></a>ソース コードをダウンロードしてプロジェクトを準備する

プロジェクトを準備するには、GitHub から [Azure IoT C SDK リポジトリ](https://github.com/Azure/azure-iot-sdk-c)を複製またはダウンロードします。

サンプルは、**samples/solutions/remote_monitoring_client** フォルダーにあります。

テキスト エディターで、**samples/solutions/remote_monitoring_client** フォルダー内の **remote_monitoring.c** ファイルを開きます。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

次の手順では、*CMake* を使用してクライアント アプリケーションをビルドする方法について説明します。 リモート監視クライアント アプリケーションは、SDK のビルド プロセスの一環として構築されます。

1. **remote_monitoring.c** ファイルを編集し、`<connectionstring>` を、ソリューション アクセラレータにデバイスを追加したときにこのハウツー ガイドの冒頭でメモしたデバイス接続文字列に置き換えます。

1. [Azure IoT C SDK リポジトリ](https://github.com/Azure/azure-iot-sdk-c)リポジトリの複製されたコピーのルートに移動し、次のコマンドを実行してクライアント アプリケーションを構築します。

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. クライアント アプリケーションを実行し、テレメトリを IoT Hub に送信します。

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    コンソールには、次のメッセージが表示されます。

    - アプリケーションは、ソリューション アクセラレータにサンプル テレメトリを送信します。
    - ソリューションのダッシュ ボードから呼び出されたメソッドに応答します。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
