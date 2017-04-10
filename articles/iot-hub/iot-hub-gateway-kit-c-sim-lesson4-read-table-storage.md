---
title: "シミュレートされたデバイスと Azure IoT Gateway - レッスン 4: Table Storage | Microsoft Docs"
description: "Intel NUC のメッセージを IoT ハブに保存し、Azure Table Storage に書き込んだ後、クラウドから読みます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドからのデータの取得, IoT クラウド サービス"
ms.assetid: 78e4b6ea-968d-401e-a7dc-8f9acdb3ec1a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b12e16a5a532448cf2e939cfcad322225b9ee811
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Azure Table Storage に保持されたメッセージの読み取り

## <a name="what-you-will-do"></a>学習内容

- IoT ハブにメッセージを送信するサンプル アプリケーションをゲートウェイで実行します。
- Azure Table Storage 内のメッセージを読み取るサンプル コードをホスト コンピューターで実行します。

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-sim-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

gulp ツールを使用して、Azure Table Storage 内のメッセージを読み取るサンプルコードを実行する方法。

## <a name="what-you-need"></a>必要なもの

次のタスクを正常に終了している必要があります。

- [Azure Function App と Azure ストレージ アカウントの作成](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)。
- [ゲートウェイ サンプル アプリケーションの実行](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)。
- [IoT ハブからのメッセージの読み取り](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)。

## <a name="get-your-azure-storage-connection-strings"></a>Azure Storage 接続文字列の取得

このレッスンの前半で、Azure ストレージ アカウントを正常に作成しています。 Azure ストレージ アカウントの接続文字列を取得するには、次のコマンドを実行します。

* すべてのストレージ アカウントを一覧表示します。

```bash
az storage account list -g iot-gateway --query [].name
```

* Azure ストレージ接続文字列を取得します。

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

レッスン 2 で値を変更していない場合、`{resource group name}` の値として `iot-gateway` を使用します。

## <a name="configure-the-device-connection"></a>デバイス接続の構成

ホスト コンピューターで実行されるサンプル コードで Azure Table Storage のメッセージを読み取ることができるように `config-azure.json`ファイルを更新します。 デバイス接続を構成するには、次の手順を実行します。

1. 次のコマンドを実行して、デバイス構成ファイル `config-azure.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![構成](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. `[Azure storage connection string]` を、取得済みの Azure ストレージ接続文字列に置き換えます。

   `[IoT hub connection string]` は、レッスン&3; の「[Azure IoT Hub からのメッセージの読み取り](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)」セクションで既に置き換えています。

## <a name="read-messages-in-your-azure-table-storage"></a>Azure Table Storage 内のメッセージの読み取り

次のコマンドでゲートウェイ サンプル アプリケーションを実行して、Azure Table Storage のメッセージを読み取ります。

```bash
gulp run --table-storage
```

IoT ハブは、新しいメッセージが到着すると、Azure Table Storage にメッセージを保存する Azure Function App をトリガーします。
`gulp run` コマンドは、IoT ハブにメッセージを送信するゲートウェイ サンプル アプリケーションを実行します。 さらに、`table-storage` パラメーターによって、Azure Table Storage に保存されているメッセージを受信する子プロセスを生成します。

送受信されるすべてのメッセージは、ホスト コンピューターのコンソール ウィンドウにすぐに表示されます。 このサンプル アプリケーション インスタンスは 40 秒後に自動的に終了します。

   ![gulp の読み取り](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table_simudev.png)


## <a name="summary"></a>まとめ

Azure Function App によって保存された Azure Table Storage 内のメッセージを読み取るサンプル コードを実行しました。

