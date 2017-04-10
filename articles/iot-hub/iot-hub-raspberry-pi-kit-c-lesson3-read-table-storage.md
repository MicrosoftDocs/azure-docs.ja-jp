---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 3: Table Storage | Microsoft Docs"
description: "Azure Table Storage に書き込まれた、デバイスからクラウドへのメッセージを監視します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドからのデータの取得, IoT クラウド サービス"
ms.assetid: 8c5558bb-3c31-4445-90e6-b1a978738545
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41e89f524bf9ae67489ab3a0eb09ceda6b211f15
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage に保持されたメッセージの読み取り
## <a name="what-you-will-do"></a>学習内容
Azure Table Storage に書き込まれた、Raspberry Pi 3 から IoT ハブに送信されたデバイスからクラウドへのメッセージを監視します。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、gulp のメッセージ読み取りタスクを使用して、Azure Table Storage に保持されたメッセージを読み取る方法について説明します。

## <a name="what-you-need"></a>必要なもの
このプロセスを開始する前に、[Raspberry Pi 3 で Azure 点滅サンプル アプリケーションを実行する](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md)作業を正常に完了している必要があります。

## <a name="read-new-messages-from-your-storage-account"></a>ストレージ アカウントからの新しいメッセージの読み取り
前の記事では、Pi でサンプル アプリケーションを実行しました。 このサンプル アプリケーションによって、メッセージが Azure IoT ハブに送信されました。 IoT ハブに送信されたメッセージは、Azure Function App を介して Azure Table Storage に格納されます。 Azure Table Storage からメッセージを読み取るには、Azure Storage の接続文字列が必要になります。

Azure Table Storage に格納されたメッセージを読み取るには、次の手順を実行します。

1. 次のコマンドを実行して、接続文字列を取得します。

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   最初のコマンドで `storage name` を取得し、それを&2; 番目のコマンドで使用して接続文字列を取得します。 値を変更していない場合、`{resource group name}` の値として `iot-sample` を使用します。
2. 次のコマンドを実行して、Visual Studio Code で構成ファイル `config-raspberrypi.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. `[Azure storage connection string]` を、手順 1. で取得した接続文字列で置き換えます。
4. `config-raspberrypi.json` ファイルを保存します。
5. もう一度メッセージを送信し、次のコマンドを実行して Azure Table Storage から読み取ります。
   
   ```bash
   gulp run --read-storage
   ```
   
   Azure Table Storage からの読み取りのロジックは、`azure-table.js` ファイルにあります。
   
   ![gulp 実行 - ストレージの読み取り](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message_c.png)

## <a name="summary"></a>概要
クラウドの IoT ハブに Pi を接続し、点滅を行うサンプル アプリケーションを使用してデバイスからクラウドへのメッセージを送信することに成功しました。 さらに、Azure Function App を使用して、IoT ハブの受信メッセージを Azure Table Storage に格納しました。 これで、IoT ハブから Pi に、クラウドからデバイスへのメッセージを送信する準備が整いました。

## <a name="next-steps"></a>次のステップ
[クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)


