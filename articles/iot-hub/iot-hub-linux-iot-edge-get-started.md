---
title: "Azure IoT Edge の使用 (Linux) | Microsoft Docs"
description: "Linux コンピューターにゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Edge の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: e2f26c1abe09feff77e1e2633d8bfcf4ca527aee
ms.contentlocale: ja-jp
ms.lasthandoff: 09/23/2017

---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Azure IoT Edge アーキテクチャについて (Linux)

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

**build.sh** スクリプトは、**iot-edge** リポジトリのローカル コピーの**ビルド** フォルダーに出力を生成します。 この出力には、このサンプルで使用する 2 つの IoT Edge モジュールが含まれています。

このビルド スクリプトにより、**liblogger.so** が **build/modules/logger/** フォルダーに配置され、**libhello\_world.so** が **build/modules/hello_world/** フォルダーに配置されます。 次の JSON 設定ファイルの例に示すように、**モジュール パス**の値にはこれらのパスを使用します。

hello\_world\_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取ります。 次のサンプルの JSON ファイルは、**samples/hello\_world/src/hello\_world\_lin.json** の SDK リポジトリに提供されています。 既定以外の場所に IoT Edge モジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、この構成ファイルはそのままで動作します。

> [!NOTE]
> モジュール パスは、hello\_world\_sample 実行可能ファイルが起動される現在の作業ディレクトリからの相対パスです。実行可能ファイルが配置されているディレクトリではありません。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。

```json
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

1. **iot-edge** レポジトリのローカル コピーのルートにある **build** フォルダーに移動します。

1. 次のコマンドを実行します。

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


