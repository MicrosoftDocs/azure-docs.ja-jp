---
title: "Azure IoT Edge の使用 (Windows) | Microsoft Docs"
description: "Windows コンピューターに Azure IoT Edge ゲートウェイを作成する方法と、モジュールや JSON 構成ファイルなどの Azure IoT Edge の主要な概念について説明します。"
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
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 5db39bab8e31a8e7026b34e72b4614b0f6f57772
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Azure IoT Edge アーキテクチャについて (Windows)

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

**build.cmd** スクリプトは、**iot-edge** リポジトリのローカル コピーの **build** フォルダーに出力を生成します。 この出力には、このサンプルで使用する 2 つの IoT Edge モジュールが含まれています。

このスクリプトにより、**logger.dll** が **build\\modules\\logger\\Debug** フォルダーに配置され、**hello\_world.dll** が **build\\modules\\hello_world\\Debug** フォルダーに配置されます。 次の JSON 設定ファイルに示されているように、**モジュール パス**の値には、これらのパスを使用します。

hello\_world\_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取ります。 次のサンプルの JSON ファイルは、**samples\\hello\_world\\src\\hello\_world\_win.json** の SDK リポジトリで提供されています。 既定以外の場所に IoT Edge モジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、この構成ファイルはそのままで動作します。

> [!NOTE]
> モジュール パスは、hello\_world\_sample.exe が配置されているディレクトリに対して相対的です。 サンプルの JSON 構成ファイルにより、既定で現在の作業ディレクトリに "log.txt" というファイルが作成されます。

```json
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

1. **iot-edge** レポジトリのローカル コピーのルートにある **build** フォルダーに移動します。

1. 次のコマンドを実行します。

    ```cmd
    samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

