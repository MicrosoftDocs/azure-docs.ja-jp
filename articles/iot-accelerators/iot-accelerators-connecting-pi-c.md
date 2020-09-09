---
title: C を使用してリモート監視するために Raspberry Pi をプロビジョニング - Azure | Microsoft Docs
description: C で記述されたアプリケーションを使用して、Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454503"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、実デバイスをリモート監視ソリューション アクセラレータに接続する方法を示します。 制限付きのデバイス上で実行される多くの組み込みアプリケーションと同様、Raspberry Pi デバイス アプリケーションのためのクライアント コードは C で書かれています。このチュートリアルでは、Raspbian OS を実行する Raspberry Pi でアプリケーションを構築します。

デバイスをシミュレートする場合は、「[新しいシミュレートされたデバイスの作成とテスト](iot-accelerators-remote-monitoring-create-simulated-device.md)」を参照してください。

### <a name="required-hardware"></a>必要なハードウェア

Raspberry Pi でコマンド ラインにリモート接続するためのデスクトップ コンピューター。

[Raspberry Pi 3 用 Microsoft IoT スタート キット](https://azure.microsoft.com/develop/iot/starter-kits/)または同等のコンポーネント。 このチュートリアルでは、キット内の次のものを使用します。

- Raspberry Pi 3
- microSD カード (NOOBS をインストール済み)
- USB ミニ ケーブル
- イーサネット ケーブル

### <a name="required-desktop-software"></a>必要なデスクトップ ソフトウェア

Raspberry Pi でコマンド ラインにリモートでアクセスするための SSH クライアントがデスクトップ コンピューターに必要です。

- Windows には SSH クライアントは含まれていません。 [PuTTY](https://www.putty.org/) を使用することをお勧めします。
- ほとんどの Linux ディストリビューションと Mac OS には、コマンド ライン SSH ユーティリティが含まれています。 詳細については、「[SSH Using Linux or Mac OS (Linux または Mac OS を使用した SSH 接続)](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)」を参照してください。

### <a name="required-raspberry-pi-software"></a>必要な Raspberry Pi ソフトウェア

この記事では、ユーザーが最新バージョンの [Raspbian OS を Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/) にインストール済みであることを前提としています。

次の手順では、ソリューション アクセラレータに接続する C アプリケーションを構築するために、Raspberry Pi を準備する方法を示しています。

1. **ssh** を使用して Raspberry Pi に接続します。 詳細については、[Raspberry Pi の Web サイト](https://www.raspberrypi.org/)の [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) のセクションを参照してください。

1. 次のコマンドを使用して Raspberry Pi を更新します。

    ```sh
    sudo apt-get update
    ```

1. このハウツー ガイドの手順を完了するには、[Linux 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)に関するページに示されている手順に従って、必要な開発ツールとライブラリを Raspberry Pi に追加します。

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
