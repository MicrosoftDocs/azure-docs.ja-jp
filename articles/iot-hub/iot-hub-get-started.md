---
title: "Azure IoT Hub の使用開始 | Microsoft Docs"
description: "IoT Hub サービスの使用を開始する方法"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>Azure IoT Hub または Azure IoT Gateway SDK を使って作業を開始する

IoT Hub サービスまたは Gateway SDK を使って作業を開始するには、いくつかのチュートリアルから&1; つを選択できます。

## <a name="iot-hub"></a>IoT Hub

Azure IoT Hub は、何百万もの IoT (モノのインターネット) デバイスとソリューション バックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。

IoT Hub サービスの使用を開始するには、次の手順で実行できます。

- 開発用コンピューターで実行されているシミュレートされたデバイスを使用するチュートリアルに従います。 [.NET][lnk-dotnet]、[Java][lnk-java]、または[Node.js][lnk-nodejs] の中から、希望するプログラミング言語を使用している入門用チュートリアルを選択します。

- 物理デバイスを使用するチュートリアルに従います。 [Raspberry Pi][lnk-rasp-pi]、[Intel Edison][lnk-edison]、または [Arduino][lnk-arduino] の中から、希望するハードウェア プラットフォーム使用している入門用チュートリアルを選択します。 これらのチュートリアルには、ハードウェア デバイスを取得する方法に関する情報が含まれます。

- IoT デバイスを開発するための C 言語の使用方法については、[Azure IoT device SDK for C の概要][lnk-c-intro]に関する記事を参照してください。

## <a name="gateway-sdk"></a>Gateway SDK

Gateway SDK を使用してカスタム フィールド ゲートウェイを作成することができます。 ゲートウェイは、分析の実行、時間の制約がある判断による待機時間の短縮、デバイス管理サービスの提供、セキュリティとプライバシーの制約の強制、プロトコル変換などのタスクを行うことができます。

Gateway SDK を使うには、次の手順を実行します。

- 開発用コンピューターで実行されているシミュレートされたゲートウェイを使用するチュートリアルに従います。 [Linux][lnk-linux] または [Windows][lnk-windows] の入門用チュートリアルを選択できます。

- 物理デバイスを使用するチュートリアルに従います。 [シミュレートされたデバイスと Intel NUC (Next Unit of Computing)][lnk-gateway-sim] を使用する入門用チュートリアル、または [SensorTag デバイスと Intel NUC][lnk-gateway-tag] を使用する入門用チュートリアルを選択できます。

## <a name="next-steps"></a>次のステップ

入門用チュートリアルを終了したら、[開発者ガイド][lnk-devguide]と[方法][lnk-howto]に関するチュートリアルで、IoT Hub と Gateway SDK のさまざまな機能を調べることができます。

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

