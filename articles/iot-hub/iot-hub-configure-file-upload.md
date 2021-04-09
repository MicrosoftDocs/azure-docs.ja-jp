---
title: Azure Portal を使用してファイルのアップロードを構成する | Microsoft Docs
description: Azure Portal を使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535877"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>ファイルのアップロード

[IoT Hub でファイルのアップロード機能](iot-hub-devguide-file-upload.md)を使用するには、最初に Azure Storage アカウントとハブを関連付ける必要があります。 **[ファイルのアップロード]** を選択して、変更対象の IoT Hub のファイル アップロード プロパティのリストを表示します。

![ポータルで IoT Hub ファイル アップロード設定を表示する](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **ストレージ コンテナー**: Azure Portal を使用して現在の Azure サブスクリプションの Azure Storage アカウントの BLOB コンテナーを選択し、IoT Hub に関連付けます。 必要に応じて、 **[ストレージ アカウント]** ブレードで Azure Storage アカウントを作成し、 **[コンテナー]** ブレードで BLOB コンテナーを作成できます。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

   ![ポータルでファイル アップロードのストレージ コンテナーを表示する](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)** : トグル ボタンを使用して、ファイルのアップロードに関する通知を有効または無効にします。

* **SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

* **File notification settings default TTL (ファイルの通知設定 既定の TTL)** : 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

* **File notification maximum delivery count (ファイルの通知設定 最大配信回数)** : IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定で 10 に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

   ![ポータルで IoT Hub ファイル アップロードを構成する](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>次のステップ

IoT Hub のファイルのアップロード機能に関する詳細については、IoT Hub 開発者ガイドの [デバイスからのファイルのアップロード](iot-hub-devguide-file-upload.md)に関する記事をご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub の監視](monitor-iot-hub.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)
* [IoT ソリューションの徹底的なセキュリティ保護](../iot-fundamentals/iot-security-ground-up.md)