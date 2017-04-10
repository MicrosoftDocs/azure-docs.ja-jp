---
title: "Azure Portal を使用してファイルのアップロードを構成する | Microsoft Docs"
description: "Azure Portal を使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: aadbee1388e2c2aafaf0a5c50d2af195284d2bec
ms.lasthandoff: 12/07/2016


---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>ファイルのアップロード
[IoT Hub でファイルのアップロード機能][lnk-upload]を使用するには、最初に Azure ストレージ アカウントとハブを関連付ける必要があります。 **[ファイルのアップロード]** 設定を選択して、変更対象の IoT Hub のファイル アップロード プロパティのリストを表示します。

![][13]

**ストレージ コンテナー**: Azure Portal を使用して現在の Azure サブスクリプションの Azure Storage アカウントの BLOB コンテナーを選択し、IoT Hub に関連付けます。 必要に応じて、**[ストレージ アカウント]** ブレードで Azure Storage アカウントを作成し、**[コンテナー]** ブレードで BLOB コンテナーを作成できます。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

![][14]

**Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)**: トグル ボタンを使用して、ファイルのアップロードに関する通知を有効または無効にします。

**SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

**File notification settings default TTL (ファイルの通知設定 既定の TTL)**: 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

**File notification maximum delivery count (ファイルの通知設定 最大配信回数)**: IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定で 10 に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

![][15]

## <a name="next-steps"></a>次のステップ
IoT Hub のファイルのアップロード機能に関する詳細については、IoT Hub 開発者ガイドの [Upload files from a device (デバイスからのファイルのアップロード)][lnk-upload] に関する記事をご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [IoT Hub メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

