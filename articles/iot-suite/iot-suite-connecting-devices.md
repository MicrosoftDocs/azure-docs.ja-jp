---
title: "Windows で C を使用してデバイスを接続する | Microsoft Docs"
description: "C で記述され、Windows で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート管理ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3536777690a9b00ded7c7fdf4d5f39638dad71b0
ms.lasthandoff: 03/10/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>デバイスをリモート監視構成済みソリューションに接続する (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Windows で C のサンプル ソリューションを作成する
次の手順では、リモート監視が事前構成されたソリューションと通信するクライアント アプリケーションを作成する方法を示します。 このアプリケーションは C で記述し、Windows 上でビルドおよび実行します。

Visual Studio 2015 または Visual Studio 2017 にスタート プロジェクトを作成し、IoT Hub デバイス クライアントの NuGet パッケージを追加します。

1. Visual Studio で、Visual C++ **Win32 コンソール アプリケーション** テンプレートを使用して、C コンソール アプリケーションを作成します。 プロジェクトの名前として「 **RMDevice**」と入力します。
2. **Win32 アプリケーション ウィザード**の **[アプリケーション設定]** ページで、**[コンソール アプリケーション]** が選択されていることを確認し、**[プリコンパイル済みヘッダー]** と **[Security Development Lifecycle (SDL) checks (Security Development Lifecycle (SDL) チェック)]** をオフにします。
3. **ソリューション エクスプローラー**で、stdafx.h、targetver.h、stdafx.cpp の各ファイルを削除します。
4. **ソリューション エクスプローラー**で、RMDevice.cpp ファイルの名前を RMDevice.c に変更します。
5. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[参照]** をクリックし、次の NuGet パッケージを検索してインストールします。
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックして **[プロパティ]** をクリックし、プロジェクトの **[プロパティ ページ]** ダイアログ ボックスを開きます。 詳細については、[Visual C++ プロジェクトのプロパティの設定][lnk-c-project-properties]に関するページをご覧ください。 
7. **[リンカー]** フォルダーをクリックして、**[入力]** プロパティ ページをクリックします。
8. **crypt32.lib** を、**[追加の依存ファイル]** プロパティに追加します。 **[OK]** をクリックし、もう一度 **[OK]** をクリックして、プロジェクトのプロパティ値を保存します。

Parson JSON ライブラリを **RMDevice** プロジェクトに追加し、必要な `#include` ステートメントを追加します。

1. 次のコマンドを使用して、コンピューター上の適切なフォルダーに Parson GitHub リポジトリを複製します。

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Parson リポジトリのローカル コピーから parson.h と parson.c の各ファイルを **RMDevice** プロジェクト フォルダーにコピーします。

1. Visual Studio で **RMDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。

1. **[既存項目の追加]** ダイアログで、**RMDevice** プロジェクト フォルダー内にある parson.h と parson.c の各ファイルを選択します。 **[追加]** をクリックして、この&2; ファイルをプロジェクトに追加します。

1. Visual Studio で RMDevice.c ファイルを開きます。 既存の `#include` ステートメントを次のコードで置き換えます。
   
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

    > [!NOTE]
    > ビルドすることで、プロジェクトに正しい依存関係が設定されたことを確認できます。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

**remote\_monitoring\_run** 関数を呼び出すコードを追加し、デバイス アプリケーションをビルドして実行します。

1. **main** 関数を、**remote\_monitoring\_run** 関数を呼び出す次のコードで置き換えます。
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、デバイス アプリケーションをビルドします。

1. **ソリューション エクスプローラー**で、**RMDevice** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックして、サンプルを実行します。 アプリケーションが、事前構成済みのソリューションにサンプル テレメトリを送信し、ソリューションのダッシュボードで設定された目的のプロパティ値を受け取り、ソリューションのダッシュボードから呼び出されたメソッドに応答すると、コンソールにメッセージが表示されます。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

