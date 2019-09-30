---
title: IoT プラグ アンド プレイ デバイスを IoT Central に接続する |Microsoft Docs
description: デバイス開発者向けに、Visual Studio Code を使用して、IoT Central に接続する IoT プラグ アンド プレイ デバイスを作成およびテストする方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997240"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Visual Studio Code を使用して、IoT Central に接続する IoT プラグ アンド プレイ デバイスを作成する

この攻略ガイドでは、次の方法について説明します。

* オペレーターが、Azure IoT Central アプリケーションで実デバイスを追加して構成する方法。

* デバイス開発者が、Visual Studio Code を使用して、IoT Central アプリケーションに接続する [IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md) デバイスを作成する方法。

[デバイス機能モデル](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)を使用して、IoT Central に接続するデバイスを定義します。 このガイドでは、環境センサー デバイスを定義するモデルを使用します。

デバイス開発者はこのモデルを使用してデバイス クライアント コードを生成し、ビルダーはこのモデルを使用して IoT Central で[デバイス テンプレートを作成](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)します。 このモデルは、デバイスと IoT Central アプリケーションの間のコントラクトとして機能します。

本ガイド内の、生成したコードのビルド方法を説明しているセクションでは、Windows を使用していることを前提としています。

このガイドでは、以下の方法について説明します。

* IoT Central のデバイス テンプレートにデバイス機能モデルをインポートする
* デバイスのテレメトリを表示するダッシュボードをテンプレートに追加する
* テンプレートから実デバイスを追加する
* Visual Studio Code にデバイス機能モデルをインポートする
* モデルから C デバイス クライアント アプリケーションを生成する
* C デバイス クライアント アプリケーションをビルドして IoT Central に接続する

## <a name="prerequisites"></a>前提条件

このガイドでデバイス コードをテストするには、**プレビュー** アプリケーション テンプレートから作成された IoT Central アプリケーションが必要です。 使用する IoT Central アプリケーションがまだない場合は、「[Azure IoT Central アプリケーションを作成する (プレビュー機能)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)」のクイック スタートを完了してください。

このガイドでデバイス機能モデルを扱う作業を行うには、次のものが必要です。

* [Visual Studio Code](https://code.visualstudio.com/download):Visual Studio Code は複数のプラットフォームに対応しています
* Visual Studio Code 用の Azure IoT Device Workbench 拡張機能。 VS Code に Azure IoT Device Workbench 拡張機能をインストールするには、次の手順に従ってください。

    1. VS Code で、 **[拡張機能]** タブを選択します。
    1. **Azure IoT Device Workbench** を検索します。
    1. **[インストール]** を選択します。

    > [!NOTE]
    > 拡張機能に含まれているコード ジェネレーターの現在のバージョンでは、**GeoPoint** と **Vector** のスキーマの種類、および **Acceleration**、**Velocity**、**Location** のセマンティックの種類はサポートされていません。 これらのスキーマとセマンティックの種類は IoT Central でサポートされています。

    > [!NOTE]
    > IoT Central で作業するには、デバイス機能モデルで、すべてのインターフェイスが同じファイル内にインラインで定義されている必要があります。

このガイドで生成した C コードを Windows 上でビルドするには、次のものが必要です。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio のインストール時に、**NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/): **CMake** をインストールするときに、 **[Add CMake to the system PATH]\(CMake をシステム パスに追加する\)** オプションを選択します。
* Azure IoT C SDK のローカル コピー:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

この攻略ガイドの最後にあるデバイス優先の手順を実行するには、次のものもインストールする必要があります。

* [Node.JS](https://nodejs.org)
* [dps-keygen ツール](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>デバイス テンプレートを作成する

複製した Azure IoT C SDK を含む **azure-iot-sdk-c** フォルダーに、**pnp_app** という名前のフォルダーを作成します。 GitHub から [EnvironmentalSensor.capabilitymodel.json](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) デバイス機能モデルをダウンロードし、ファイルを **pnp_app** フォルダーに保存します。 `<YOUR_COMPANY_NAME_HERE>` の 2 つのインスタンスを自分の名前に置き換えます。 英数字 (a-z、A-Z、0-9) とアンダースコアのみを使用してください。 デバイス機能モデルを一意に識別する `"@id"` フィールドの完全な値をメモしておきます。後で必要になります。 このモデルには、次の 2 つのインターフェイスが含まれています。

* **DeviceManagement** 共通インターフェイス。
* **EnvironmentalSensor** カスタム インターフェイス。

環境センサー デバイス用のデバイス テンプレートを IoT Central に作成するには、次のようにします。

1. **[Device templates]\(デバイス テンプレート\)** ページに移動して、 **[+ New]\(+ 新規\)** を選択します。 **[Custom]\(カスタム\)** を選択します。

1. デバイス テンプレートの名前として、「**Environmental Sensor**」と入力します。

1. **[Import Capability Model]\(機能モデルのインポート\)** を選択します。 次に、作成した **EnvironmentalSensor.capabilitymodel.json** を見つけ、 **[Open]\(開く\)** を選択します。 **Device Information** と **Environmental Sensor** という 2 つのインターフェイスが**環境センサー機能モデル**に表示されます。

1. **[Views]\(ビュー\)** を選択し、次に **[Visualizing the Device]\(デバイスの視覚化\)** を選択します。

1. ダッシュボードに追加できるフィールドの一覧で、 **[Humidity]\(湿度\)** と **[Temperature]\(気温\)** の 2 つの**テレメトリ**値 を選択し、 **[Combine]\(結合\)** を選択します。 次に、 **[保存]** を選択します。

これで、**環境センサー** モデルを使用し、デバイスからのテレメトリを表示する簡単なダッシュボードが用意されたデバイス テンプレートが完成しました。

## <a name="publish-the-template-and-add-a-real-device"></a>テンプレートを発行して実デバイスを追加する

テンプレートを発行して実デバイスを追加するには、 **[Device templates]\(デバイス テンプレート\)** ページに移動し、**Environmental Sensor** デバイス テンプレートを選択します。 **[Publish]\(発行\)** を選択し、情報を確認して、 **[Publish]\(発行\)** を選択します。

クライアント アプリケーションを接続する前に、 **[Devices]\(デバイス\)** ページでデバイスを追加し、その接続情報を取得する必要があります。

1. **[Devices]\(デバイス\)** ページに移動し、 **[Environmental Sensor]\(環境センサー\)** を選択します。 **[Devices]\(デバイス\)** ページでは、アプリケーションに接続するデバイスを管理することができます。

1. 実デバイスを追加するには、 **[+ New]\(+ 新規\)** を選択し、デバイス ID を **sensor01** に変更して **[Create]\(作成\)** を選択します。 **[Simulated]\(シミュレート済み\)** が **[Off]\(オフ\)** になっていることを確認します。

1. デバイスの一覧で、目的の**環境センサー** デバイスを選択します。 次に、 **[接続]\(Connect\)** を選択します。

1. **スコープ ID**、**デバイス ID**、および **主キー**の値をメモします。 次に、 **[閉じる]\(Close\)** を選択します。

## <a name="generate-a-device-client-application"></a>デバイス クライアント アプリケーションを作成する

Visual Studio Code を使用して、デバイス機能モデルからスケルトン デバイス クライアント アプリケーションを生成します。

1. Visual Studio Code を起動し、**pnp_app** フォルダーを開きます。

1. **Ctrl + Shift + P** キーを使用してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力し、 **[Generate Device Code Stub]\(デバイス コード スタブの生成\)** を選択します。

1. **EnvironmentalSensor.capabilitymodel.json** デバイス機能モデル ファイルを選択します。

1. プロジェクト名として「**sensor_app**」と入力します。

1. 言語として、 **[ANSI C]** を選択します。

1. ターゲットとして、 **[CMake Project]\(CMake プロジェクト\)** を選択します。

1. 接続方法として、 **[Via DPS (Device Provisioning Service) symmetric key]\(DPS (デバイス プロビジョニング サービス) の対称キーを使用する\)** を選択します。

Visual Studio Code は、**sensor_app** フォルダー内で生成された C コードを使って新しいウィンドウを開きます。

## <a name="add-connection-details-to-the-device-client"></a>デバイス クライアントに接続の詳細を追加する

デバイス クライアントに接続情報を追加するには、生成されたコードを含むフォルダーで **main.c** を開きます。

1. `[DPS Id Scope]` を、前にメモした**スコープ ID** の値に置き換えます。

1. `[DPS symmetric key]` を、前にメモした**主キー**の値に置き換えます。

1. `[device registration Id]` を、前にメモした**デバイス ID** の値に置き換えます。

1. 変更を保存します。

## <a name="build-and-run-the-client"></a>クライアントをビルドして実行する

クライアントをビルドして実行するには、Azure IoT C SDK 用にビルドをカスタマイズする必要があります。

1. **azure-iot-sdk-c** フォルダーのルートにある **CMakeLists.txt** ファイルを開きます。

1. このファイルの末尾に次の行を追加して、ビルドに新しいクライアント アプリを含めます。

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. 変更を保存します。

1. コマンド プロンプトを開き、**azure-iot-sdk-c** フォルダーに移動します。

1. アプリケーションをビルドするには、次のコマンドを実行します。

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. アプリケーションを実行するには、同じコマンド プロンプトから次のコマンドを実行します。

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>テレメトリを表示する

数分後、IoT Central アプリケーションのデバイス ダッシュボードでデバイスからのテレメトリを確認できます。

## <a name="connect-device-first"></a>デバイス優先で接続する

[接続](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関する記事で説明されているように、デバイス優先接続を使用して IoT プラグ アンド プレイ デバイスを接続できます。 検出プロセスは、次の順序で実行されます。

1. デバイス テンプレートに関連付けます (IoT Central アプリケーションで既に発行されている場合)。

1. 発行および認定済みの機能モデルの[パブリック リポジトリ](https://aka.ms/ACFI)から機能モデルをフェッチします。

Visual Studio Code と上記の**デバイス コード スタブの生成**コマンドを使用して、次の手順に従ってデバイス優先で接続し、発行されているデバイス機能モデルをパブリック リポジトリから IoT Central に自動的に取り込むことができます。

1. パブリック リポジトリで発行されている既存のデバイス機能モデルを使用します。 完全なデバイス機能モデルが必要であり、このモデルの URN を書き留めておく必要があります。

1. 上記の手順に従って、Visual Studio Code を使用する[デバイス クライアント アプリケーションを生成](#generate-a-device-client-application)し、デバイス コードを生成します。

1. IoT Central アプリケーションから **[Administration]\(管理\)** タブに移動し、 **[Device Connection]\(デバイス接続\)** セクションを選択します。 アプリケーションの**スコープ ID** と**主キー**の値をメモします。

    > [!NOTE]
    > このページに表示される**主キー**は、アプリケーションの複数のデバイス キーを生成するために使用できるグループ共有アクセス署名です。

1. [DPS keygen](https://www.npmjs.com/package/dps-keygen) ツールを使用して、前にメモした主キーからデバイス キーを生成します。 デバイス キーを生成するには、次のコマンドを実行します。

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. 前のセクション「[デバイス クライアントに接続の詳細を追加する](#add-connection-details-to-the-device-client)」の手順に従って、**スコープ ID**、**主キー**、および**デバイス ID** を追加します。

1. 前のセクション「[クライアントをビルドして実行する](#build-and-run-the-client)」の手順に従います。

1. これで、デバイスが IoT Central アプリケーションに接続され、デバイス機能モデルがパブリック リポジトリからデバイス テンプレートとして自動的に取り込まれるようになりました。

## <a name="next-steps"></a>次の手順

実デバイスを IoT Central に接続する方法を学習したので、次の手順では、[デバイス テンプレートのセットアップ](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関するページでデバイス テンプレートについて学習することをお勧めします。
