---
title: "Azure IoT Gateway SDK の使用 (Linux) | Microsoft Docs"
description: "Linux コンピューターにゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Gateway SDK の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: b3cc8e53b0c8bb7ea40b6ebcebe1f97d4a3e1180
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>IoT Gateway SDK アーキテクチャについて (Linux)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法
作業を開始する前に、Linux で SDK を使用するための[開発環境を設定する][lnk-setupdevbox]必要があります。

1. シェルを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\build.sh** スクリプトを実行します。 このスクリプトでは、**cmake** ユーティリティを使用して、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに **ビルド** という名前のフォルダーを作成し、メイクファイルを生成します。 スクリプトは、次にソリューションをビルドし、単体テストとエンド ツー エンド テストはスキップします。 単体テストをビルドして実行する場合は、**--run-unittests** パラメーターを追加します。 エンド ツー エンド テストをビルドして実行する場合は、**--run-e2e-tests** を追加します。

> [!NOTE]
> **build.sh** スクリプトを実行するたびに **ビルド** フォルダーが削除され、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに再作成されます。
> 
> 

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法
1. **build.sh** スクリプトは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの**ビルド** フォルダーに出力を生成します。 ここには、このサンプルで使用する&2; つのモジュールが含まれています。
   
    このビルド スクリプトにより、**liblogger.so** が **build/modules/logger/** フォルダーに配置され、**libhello_world.so** が **build/modules/hello_world/** フォルダーに配置されます。 次の JSON 設定ファイルに示すように、 **モジュール パス** の値にはこれらのパスを使用します。
2. hello_world_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取得します。 サンプルの JSON ファイルは、**azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** でリポジトリの一部として提供されています。以下にコピーします。 既定以外の場所にモジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、そのままで動作します。

   > [!NOTE]
   > モジュール パスは、hello_world_sample 実行可能ファイルがある場所から現在の作業ディレクトリへの相対パスです。実行可能ファイルが配置されているディレクトリではありません。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。
   
    ```
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links": 
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
3. **azure-iot-gateway-sdk/build** フォルダーに移動します。
4. 次のコマンドを実行します。
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

