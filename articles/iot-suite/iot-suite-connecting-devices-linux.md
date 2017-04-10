---
title: "Linux で C を使用してデバイスを接続する | Microsoft Docs"
description: "C で記述され、Linux で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c844f9a84878f12fbdf7a5b4eaf7bbf19f4ce666
ms.openlocfilehash: 4a1615c4bea8c54d506c3252e2de42642bb55e46
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>デバイスをリモート監視構成済みソリューションに接続する (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Linux で C のサンプル クライアントをビルドして実行する
次の手順では、リモート監視が事前構成されたソリューションと通信するクライアント アプリケーションを作成する方法を示します。 このアプリケーションは C で記述し、Ubuntu Linux 上でビルドおよび実行します。

次の手順を完了するには、Ubuntu バージョン 15.04 または 15.10 が実行されているデバイスが必要です。 次に進む前に、次のコマンドを使用して、前提条件となるパッケージを Ubuntu デバイスにインストールします。

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>デバイスにクライアント ライブラリをインストールする
Azure の IoT Hub クライアント ライブラリは、 **apt-get** コマンドを使用してパッケージとして Ubuntu デバイスにインストールし、使用できます。 IoT Hub クライアント ライブラリとヘッダー ファイルが含まれるパッケージを Ubuntu コンピューターにインストールするには、次の手順を実行します。

1. シェルで、コンピューターに Azure IoT リポジトリを追加します。
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. azure-iot-sdk-c-dev パッケージをインストールします
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Parson JSON パーサーをインストールする
IoT Hub クライアント ライブラリでは、メッセージ ペイロードの解析に Parson JSON のパーサーが使用されます。 次のコマンドを使用して、コンピューター上の適切なフォルダーに Parson GitHub リポジトリを複製します。

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>プロジェクトを準備する
Ubuntu コンピューターで、**remote\_monitoring** という名前のフォルダーを作成します。 **remote\_monitoring** フォルダーで次の手順を実行します。

- **main.c**、**remote\_monitoring.c**、**remote\_monitoring.h**、**CMakeLists.txt** の&4; ファイルを作成します。
- **parson** という名前のフォルダーを作成します。

ファイル **parson.c** と **parson.h** を Parson リポジトリのローカル コピーから **remote\_monitoring/parson** フォルダーにコピーします。

テキスト エディターで、**remote\_monitoring.c** ファイルを開きます。 次の `#include` ステートメントを追加します。
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>remote\_monitoring\_run 関数を呼び出す
テキスト エディターで、**remote_monitoring.h** ファイルを開きます。 次のコードを追加します。

```
void remote_monitoring_run(void);
```

テキスト エディターで、 **main.c** ファイルを開きます。 次のコードを追加します。

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行
次の手順では、 *CMake* を使用してクライアント アプリケーションをビルドする方法について説明します。

1. テキスト エディターで、**remote_monitoring** フォルダーの **CMakeLists.txt** ファイルを開きます。

1. 次の手順を追加して、クライアント アプリケーションをビルドする方法を定義します。
   
    ```
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER ".." "../parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h
    _files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
1. **remote_monitoring** フォルダーに、CMake によって生成される *make* ファイルを格納するフォルダーを作成し、次に示すように **cmake** コマンドと **make** コマンドを実行します。
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. クライアント アプリケーションを実行し、テレメトリを IoT Hub に送信します。
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


