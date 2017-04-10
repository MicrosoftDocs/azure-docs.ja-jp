---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 4: Function App の作成 | Microsoft Docs"
description: "Intel NUC のメッセージを IoT ハブに保存し、Azure Table Storage に書き込んだ後、クラウドから読みます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドへのデータの格納, クラウドに格納されているデータ, IoT クラウド サービス"
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Azure Function App とストレージ アカウントを作成する

Azure Functions は、"_関数_" (小規模なコード) をクラウドで手軽に実行するためのソリューションです。 Azure Function App は、Azure での関数の実行をホストします。 

## <a name="what-you-will-do"></a>学習内容

- Azure Resource Manager テンプレートを使用して、Azure Function App と Azure Storage アカウントを作成します。 Azure Function App は、Azure IoT ハブのイベントをリッスンして受信メッセージを処理し、それらを Azure Table Storage に書き込みます。

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。


## <a name="what-you-will-learn"></a>学習内容

このレッスンでは、次の内容を学習します。

- Azure Resource Manager を使用して Azure リソースをデプロイする方法。
- Azure Function App を使用して、IoT Hub のメッセージを処理して Azure Table Storage のテーブルに書き込む方法。

## <a name="what-you-need"></a>必要なもの

前のレッスンを正常に完了している必要があります。

- [レッスン 1: Intel NUC を IoT ゲートウェイとしてセットアップする](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [レッスン 2: ホスト コンピューターと Azure IoT ハブを準備する](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [レッスン 3: SensorTag からメッセージを受信し、IoT ハブからメッセージを読み取る](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>サンプル アプリを開く

`iot-hub-c-intel-nuc-gateway-getting-started` リポジトリ フォルダーに移動し、構成ファイルを初期化した後、次のコマンドを実行して、Visual Studio Code でサンプル プロジェクトを開きます。

```bash
cd Lesson4
npm install
gulp init
code .
```

![リポジトリの構造](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- `arm-template.json` ファイルは、Azure Function App と Azure Storage アカウントが含まれた Azure Resource Manager テンプレートです。
- `arm-template-param.json` ファイルは、Azure Resource Manager テンプレートによって使用される構成ファイルです。
- `ReceiveDeviceMessages` サブフォルダーは、Azure 関数の Node.js コードが含まれています。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Azure Resource Manager テンプレートの構成と Azure でのリソースの作成

Visual Studio Code で `arm-template-param.json` ファイルを更新します。

![arm-template json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- `[your IoT Hub name]` を、レッスン 2 で指定した `{my hub name}`に置き換えます。

`arm-template-param.json` ファイルの更新後、次のコマンドを実行してリソースを Azure にデプロイします。

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

レッスン 2 で値を変更していない場合、`{resource group name}` の値として `iot-gateway` を使用します。

## <a name="summary"></a>まとめ

IoT ハブのメッセージを処理する Azure Function App と、それらのメッセージを格納する Azure Storage アカウントを作成しました。 これで、ゲートウェイによって IoT ハブに送信されるメッセージを読み取ることができます。

## <a name="next-steps"></a>次のステップ
[Azure Storage に保持されたメッセージの読み取り](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)

