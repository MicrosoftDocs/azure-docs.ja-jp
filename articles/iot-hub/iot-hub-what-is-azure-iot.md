---
title: "モノのインターネット (IoT Suite) 用の Azure ソリューション | Microsoft Docs"
description: "サンプル IoT ソリューション アーキテクチャの概要と、このアーキテクチャとデバイス、Azure IoT Hub サービス、Azure IoT device SDK、Azure IoT service SDK、その他の Azure サービスとの関係。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e95d02e706b87eaa355951a34bbaa3abf2ac2370
ms.lasthandoff: 01/24/2017


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>次のステップ

Azure IoT Hub は、ソリューション バックエンドと何百万ものデバイスとの間に信頼性のある保護された双方向通信を確立できるサービスです。 これによりソリューション バックエンドから次のことを実行できるようになります。

* デバイスから大量のテレメトリを受信する。
* デバイスからストリーム イベント プロセッサにデータをルーティングする。
* デバイスからアップロードされたファイルを受信する。
* クラウドからデバイスへのメッセージを特定のデバイスに送信する。

IoT Hub を使用して、独自のソリューション バックエンドを実装できます。 さらに、IoT Hub には、デバイス、デバイスのセキュリティ資格情報、デバイスが IoT Hub に接続するための権限をプロビジョニングするために使用する ID レジストリが含まれています。 IoT Hub の詳細については、「[Azure IoT Hub とは][lnk-iot-hub]」を参照してください。

Azure IoT Hub では、標準ベースのデバイス管理が可能となっており、リモートからデバイスを管理、構成、更新することができます。詳細については、「[IoT Hub を使用したデバイス管理の概要][lnk-device-management]」を参照してください。

クライアント アプリケーションを各種デバイス ハードウェア プラットフォームやオペレーティング システムに実装するために、Azure IoT device SDK を使用できます。 device SDK には、IoT Hub へのテレメトリの送信とクラウドからデバイスへのメッセージの受信を容易にするライブラリが含まれています。 device SDK を使用すると、さまざまなネットワーク プロトコルのうちのいずれかを選択して IoT Hub と通信することができます。 詳細については、[device SDK に関する情報][lnk-device-sdks]を参照してください。

実際にコードを作成してサンプルを実行するには、[IoT Hub の使用][lnk-getstarted]に関するチュートリアルを参照してください。

構成済みのソリューションのコレクションである [Azure IoT Suite][lnk-iot-suite] もご確認ください。 IoT Suite を使用すると、リモート モニタリング、資産管理、予測メンテナンスなど、一般的な IoT シナリオに対処するための IoT プロジェクトをすばやく開始してスケーリングできます。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md

