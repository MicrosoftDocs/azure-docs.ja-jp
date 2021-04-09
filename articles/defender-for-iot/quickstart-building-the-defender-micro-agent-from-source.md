---
title: ソース コードから Defender マイクロ エージェントをビルドする (プレビュー)
description: マイクロ エージェントには、ディストリビューションをカスタマイズするために使用できるインフラストラクチャが含まれています。
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: aac9bf224064dd8393acfeb2928f5ed2d1f84381
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781111"
---
# <a name="build-the-defender-micro-agent-from-source-code-preview"></a>ソース コードから Defender マイクロ エージェントをビルドする (プレビュー)

マイクロ エージェントには、ディストリビューションをカスタマイズするために使用できるインフラストラクチャが含まれています。 使用可能な構成パラメーターの一覧については、`configs/LINUX_BASE.conf` ファイルを参照してください。

単一ディストリビューションでは、ベース `.conf` ファイルを変更します。 

複数のディストリビューションが必要な場合は、ベース構成を継承し、それの値をオーバーライドできます。 

値をオーバーライドするには、以下の操作を行います。

1. 新しい `.dist` ファイルを作成します。

1. 先頭に `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` を追加します。
 
1. 必要に応じて新しい値を定義します。次に例を示します。 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. ビルドするときに、`.dist` ファイルに参照を指定します。 たとえば、次のように入力します。 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>ベースライン構成の署名 

エージェントは、既定では、改ざんの可能性を軽減するために、ディスクに配置されている構成ファイルの信頼性を検証します。

このプロセスを停止するには、プリプロセッサ フラグ `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` を定義します。

運用環境で署名チェックを無効にすることは、お勧めしません。 

運用環境のシナリオで別の構成が必要な場合は、Defender for IoT チームにお問い合わせください。 

## <a name="prerequisites"></a>前提条件 

1. Defender for IoT のソース コードへのアクセスが必要な場合は、アカウント マネージャーに問い合わせてください。
 
1. ディスク上のフォルダーに、ソース コードを複製するか抽出します。

1. そのディレクトリに移動します。

1. 次のコードを使用して、サブモジュールをプルします。

    ```bash
    git submodule update --init
    ```
    
1. 次に、次のコードを使用して、Azure IoT SDK のサブモジュールをプルします。 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. 実行アクセス許可を追加し、開発者環境のセットアップ スクリプトを実行します。

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. ビルド出力用のディレクトリを作成します。 

    ```bash
    mkdir cmake 
    ```

1. (省略可能) [VSCode](https://code.visualstudio.com/download ) をダウンロードしてインストールします 

1. (省略可能) VSCode 用の [C/C++ 拡張機能](https://code.visualstudio.com/docs/languages/cpp )をインストールします。

## <a name="building-the-defender-iot-micro-agent"></a>Defender IoT のマイクロ エージェントのビルド 

1. VSCode を使用してディレクトリを開きます 

1. `cmake` ディレクトリに移動します。 

1. 次のコマンドを実行します。 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    次に例を示します。 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>次のステップ

[Azure Defender for IoT ソリューションを構成](quickstart-configure-your-solution.md)します。
