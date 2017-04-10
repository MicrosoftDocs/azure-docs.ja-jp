---
title: "Azure IoT Gateway SDK の使用 (Windows) | Microsoft Docs"
description: "Windows コンピューターにゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Gateway SDK の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: 4b6cb52c3dcc34c70fd3b86031af48a12573cb8b
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-windows"></a>Windows での IoT Gateway SDK アーキテクチャの確認
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法
作業を開始する前に、Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]する必要があります。

1. **開発者コマンド プロンプト for VS2015** コマンド プロンプトを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドします。 Visual Studio ソリューションは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **build** フォルダーにあります。 スクリプトで追加のパラメーターを指定すると、ユニット テストとエンド ツー エンド テストをビルドして実行できます。 パラメーターはそれぞれ、**--run-unittests** と **--run-e2e-tests** です。 

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法
1. **build.cmd** スクリプトにより、リポジトリのローカル コピーに**ビルド**という名前のフォルダーが作成されます。 このフォルダーには、このサンプルで使用する&2; つのモジュールが含まれています。
   
    このスクリプトにより、**logger.dll** が **build\\modules\\logger\\Debug** フォルダーに配置され、**hello_world.dll** が **build\\modules\\hello_world\\Debug** フォルダーに配置されます。 次の JSON 設定ファイルに示すように、**モジュール パス**の値にはこれらのパスを使用します。
2. hello_world_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取得します。 次のサンプル JSON ファイルは、**azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json** でリポジトリの一部として提供されています。 既定以外の場所にモジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、そのままで動作します。 

   > [!NOTE]
   > モジュール パスは、hello_world_sample.exe が配置されているディレクトリに対して相対的です。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。
   
    ```
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```
3. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。

4. 次のコマンドを実行します。
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

