---
title: Visual Studio と Visual Studio Code を使用して IoT プラグ アンド プレイ プレビュー デバイスを構築する | Microsoft Docs
description: Visual Studio と Visual Studio Code を使用して、IoT プラグ アンド プレイ デバイス モデルの作成とデバイス コードの実装を迅速にします。
author: liydu
ms.author: liydu
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d68a3f99096ca64b357239f61cf7ff17d6fc3f83
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935528"
---
# <a name="use-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code 用 Azure IoT Tools を使用する

Visual Studio Code 用 Azure IoT Tools は、デバイス機能モデル (DCM) とインターフェイスを作成し、モデル リポジトリに発行し、デバイス アプリケーションを実装するスケルトン C コードを生成するための統合環境を提供します。

この記事では、その方法について説明します。

- デバイス コードとアプリケーション プロジェクトを生成する。
- 生成されたコードをデバイス プロジェクトで使用する。
- スケルトン コードを再生成して繰り返す。

VS Code を使用した IoT デバイスの開発の詳細については、[https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench) を参照してください。

## <a name="prerequisites"></a>前提条件

[Visual Studio Code](https://code.visualstudio.com/) をインストールします。

VS Code に拡張機能パックをインストールするには、次の手順を実行します。

1. VS Code で **[拡張機能]** タブを選択します。
1. マーケットプレースから **Azure IoT Tools** を検索してインストールします。

## <a name="generate-device-code-and-project"></a>デバイス コードとプロジェクトを生成する

VS Code で、**Ctrl + Shift + P** キーを押してコマンド パレットを開きます。「**IoT プラグ アンド プレイ**」と入力し、 **[Generate Device Code Stub]\(デバイス コード スタブを生成する\)** を選択してスケルトン コードおよびプロジェクトの種類を構成します。 次の一覧では、各手順の詳細について説明します。

- **コードを生成するために使用される DCM ファイル**。 スケルトン デバイス コードを生成するには、実装する DCM ファイルおよびインターフェイス ファイルが含まれているフォルダーを開きます。 DCM が必要とするインターフェイスをコード ジェネレーターが検出できない場合、コード ジェネレーターはこれを必要に応じてモデル リポジトリからダウンロードします。

- **デバイス コード言語**。 現在、コード ジェネレーターでは ANSI C のみがサポートされています。

- **プロジェクト名**。 プロジェクト名は、生成されたコードおよびその他のプロジェクト ファイルのフォルダー名として使用されます。 既定では、このフォルダーは DCM ファイルの横に配置されます。 DCM を更新してデバイス コードを再生成するたびに、生成されたコード フォルダーを手動でコピーしなくてもすむようにするには、DCM ファイルをプロジェクト フォルダーと同じフォルダーに保持します。

- **プロジェクトの種類**。 コード ジェネレーターは、プロジェクト ファイルも生成します。これにより、独自のプロジェクトまたはデバイス SDK プロジェクトにコードを統合できるようになります。 現在サポートされているプロジェクトの種類は次のとおりです。

    - **CMake プロジェクト**: ビルド システムとして [CMake](https://cmake.org/) を使用するデバイス プロジェクトの場合。 このオプションでは、C コードと同じフォルダーに `CMakeLists.txt` ファイルが生成されます。
    - **MXChip IoT DevKit プロジェクト**: [MXChip IoT DevKit](https://aka.ms/iot-devkit) デバイス上で実行するデバイス プロジェクトの場合。 このオプションでは、[VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) または Arduino IDE で使用し、ビルドして IoT DevKit デバイス上で実行できる Arduino プロジェクトが生成されます。

- **Azure IoT に接続するための方法**。 生成されたファイルには、Azure IoT Hub に接続するようにデバイスを構成するコードも含まれています。 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) に直接接続するか、[デバイス プロビジョニング サービス](https://docs.microsoft.com/azure/iot-dps)を使用するかを選択できます。

    - **IoT Hub デバイス接続文字列を使用する**: デバイス アプリケーションが IoT Hub に直接接続するためのデバイス接続文字列を指定します。
    - **DPS 対称キーを使用する**: DPS を使用して IoT Hub または IoT Central に接続するために必要なデバイス アプリケーションの**スコープ ID**、**登録 ID**、および**SaS キー**を指定します。

コード ジェネレーターは、ローカル フォルダーにある DCM およびインターフェイス ファイルを使用しようとします。 インターフェイス ファイルがローカル フォルダーに存在しない場合、コード ジェネレーターはパブリック モデル リポジトリまたは会社モデル リポジトリでそれらを検索します。 [共通インターフェイス ファイル](./concepts-common-interfaces.md)はパブリック モデル リポジトリに格納されています。

コードの生成が完了すると、拡張機能によって新しい VS Code ウィンドウが開き、コードが表示されます。 **main.c** などの生成されたファイルを開くと、C SDK ソース ファイルを開くことができないという IntelliSense レポートが表示される場合があります。 IntelliSense およびコード参照を正しく使用できるようにするには、次の手順を使用して C SDK ソースを組み込みます。

1. VS Code で、**Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**C/C++: Edit Configurations (JSON)** 」と入力して選択し、**c_cpp_properties.json** ファイルを開きます。

1. `includePath` セクションに、デバイス SDK のパスを追加します。

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. ファイルを保存します。

## <a name="use-the-generated-code"></a>生成されたコードを使用する

次の手順では、ユーザー独自のデバイス プロジェクトで生成されたコードをさまざまな開発用コンピューター プラットフォーム上で使用する方法について説明します。 この手順では、生成されたコードと IoT Hub デバイス接続文字列を使用していることを前提としています。

### <a name="linux"></a>Linux

Ubuntu や Debian などの Linux 環境で CMake を使用して、デバイス コードをデバイス C SDK と一緒にビルドするには、次のようにします。

1. ターミナル アプリケーションを開きます。

1. `apt-get` コマンドを使用して、**GCC**、**Git**、`cmake`、およびすべての依存関係をインストールします。

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    `cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

    ```sh
    cmake --version
    gcc --version
    ```

1. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を複製します。

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    この操作は、完了するまでに数分かかります。

1. 生成されたコードを含むフォルダーを、デバイス SDK ルート フォルダーにコピーします。

1. VS Code で、デバイス SDK のルート フォルダーにある `CMakeLists.txt` ファイルを開きます。

1. SDK のコンパイル時にデバイス コード スタブのフォルダーが取り込まれるように、`CMakeLists.txt` ファイルの末尾に次の行を追加します。

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. デバイス SDK ルート フォルダーに `cmake` というフォルダーを作成し、そのフォルダーに移動します。

    ```sh
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、CMake を使用して、デバイス SDK と生成されたコード スタブをビルドします。

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. ビルドが成功したら、IoT Hub デバイス接続文字列をパラメーターとして指定してアプリケーションを実行します。

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

コマンド ラインで CMake と Visual Studio C/C++ コンパイラを使用して、Windows 上のデバイス C SDK と共にデバイス コードをビルドする方法については、[IoT プラグ アンド プレイに関するクイックスタート](./quickstart-create-pnp-device.md)を参照してください。 次の手順では、Visual Studio で CMake プロジェクトとしてデバイス C SDK と共にデバイス コードをビルドする方法について説明します。

1. [Visual Studio 2019 (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)　のインストール: **NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。

1. Visual Studio を開き、 **[Get Started]\(はじめに\)** ページで **[Clone or check out code]\(コードを複製またはチェックアウトする\)** を選択します。

1. **[Repository location]\(リポジトリの場所\)** で、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) リポジトリを複製します。

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    この操作は完了するまでに数分かかることが予想されます。進行状況は **[チーム エクスプローラー]** ウィンドウで確認できます。

1. **[チーム エクスプローラー]** で **azure-iot-sdk-c** リポジトリを開き、 **[Branches]\(ブランチ\)** を選択し、**public-preview** ブランチを検索してチェックアウトします。

    ![パブリック プレビュー](media/howto-develop-with-vs-vscode/vs-public-preview.png)

1. 生成されたコードを含むフォルダーを、デバイス SDK ルート フォルダーにコピーします。

1. VS で `azure-iot-sdk-c` フォルダーを開きます。

1. デバイス SDK ルート フォルダーにある `CMakeLists.txt` ファイルを開きます。

1. SDK のコンパイル時にデバイス コード スタブのフォルダーが取り込まれるように、`CMakeLists.txt` ファイルの末尾に次の行を追加します。

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. **[General]\(全般\)** ツールバーで、 **[Configurations]\(構成\)** ドロップダウンを見つけます。 **[Manage Configuration]\(構成の管理\)** を選択して、プロジェクトの CMake 設定を追加します。

    ![構成を管理する](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. **[CMake Settings]\(CMake の設定\)** で、新しい構成を追加し、ターゲットとして **[x64-Release]** を選択します。

1. **[CMake Command Argument]\(CMake コマンド引数\)** に、次の行を追加します。

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. ファイルを保存します。

1. **ソリューション エクスプローラー**で、ルート フォルダー内の `CMakeLists.txt` を右クリックし、コンテキスト メニューから **Build\(ビルド\)** を選択して、デバイス SDK と生成されたコード スタブをビルドします。

1. ビルドが成功したら、コマンド プロンプトで、IoT Hub デバイス接続文字列をパラメーターとして指定してアプリケーションを実行します。

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Visual Studio での CMake の使用の詳細については、「[CMake プロジェクトのビルド](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)」を参照してください。

### <a name="macos"></a>macOS

次の手順では、CMake を使用して、macOS でデバイス C SDK と共にデバイス コードをビルドする方法について説明します。

1. ターミナル アプリケーションを開きます。

1. [Homebrew](https://homebrew.sh) を使用して、すべての依存関係をインストールします。

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. [CMake](https://cmake.org/) がバージョン **2.8.12** 以上であることを確認します。

    ```bash
    cmake --version
    ```

1. 使用可能な最新バージョンになるよう [CURL を修正します](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)。

1. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) リポジトリを複製します。

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    この操作は、完了するまでに数分かかります。

1. 生成されたコードを含むフォルダーを、デバイス SDK ルート フォルダーにコピーします。

1. VS Code で、デバイス SDK のルート フォルダーにある `CMakeLists.txt` ファイルを開きます。

1. SDK のコンパイル時にデバイス コード スタブのフォルダーが取り込まれるように、`CMakeLists.txt` ファイルの末尾に次の行を追加します。

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. デバイス SDK ルート フォルダーに `cmake` というフォルダーを作成し、そのフォルダーに移動します。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、CMake を使用して、デバイス SDK と生成されたコード スタブをビルドします。

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. ビルドが成功したら、IoT Hub デバイス接続文字列をパラメーターとして指定してアプリケーションを実行します。

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>スケルトン コードを再生成して繰り返す

DCM ファイルまたはインターフェイス ファイルを更新すると、コード ジェネレーターはコードを再生成できます。 DCM ファイルからデバイス コードが既に生成されていると仮定して、コードを再生成するには次のようにします。

1. DCM ファイルが開いているフォルダーで、**Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**IoT プラグ アンド プレイ**」と入力して、 **[Generate Device Code Stub]\(デバイス コード スタブを生成する\)** を選択します。

1. 更新した DCM ファイルを選択します。

1. **[Re-generate code for {project name}]\({project name} のコードを再生成する\)** を選択します。

1. コード ジェネレーターは、以前に構成した設定を使用してコードを再生成します。 ただし、`main.c` や `{project_name}_impl.c` などのユーザー コードを含む可能性のあるファイルは上書きされません。

> [!NOTE]
> インターフェイス ファイルの URN id を更新すると、新しいインターフェイスとして扱われます。 コードを再生成すると、コード ジェネレーターによってインターフェイスのコードが生成されますが、`{project_name}_impl.c` ファイル内の元のコードは上書きされません。

## <a name="problems-and-feedback"></a>問題とフィードバック

Azure IoT Tools は、GitHub 上のオープンソース プロジェクトです。 問題や機能に関する要望については、[GitHub で問題を作成](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)できます。

## <a name="next-steps"></a>次の手順

このハウツー記事では、Visual Studio と Visual Studio Code を使用して、デバイス アプリケーションを実装するスケルトン C コードを生成する方法について学習しました。 次は、[Azure IoT エクスプローラー ツールをインストールして使用する](./howto-install-iot-explorer.md)方法について学習することをお勧めします。
