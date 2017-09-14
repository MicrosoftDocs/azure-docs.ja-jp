---
title: "Raspberry Pi を Azure IoT Suite に 接続する | Microsoft Docs"
description: "このチュートリアルでは、Node.js または C を使用して、Raspberry Pi 3 と IoT Suite リモート監視ソリューションに対応した Microsoft Azure IoT スタート キットの使用方法について説明します。 テレメトリをシミュレートするチュートリアル、実際のセンサーを使用するチュートリアル、またはリモート ファームウェア更新を有効にするチュートリアルの中から選択できます。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 5efb78c2c514bd0b21be842ddee5465fe4e4dcf9
ms.contentlocale: ja-jp
ms.lasthandoff: 05/04/2017

---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Microsoft Azure IoT Raspberry Pi 3 スタート キットをリモート監視ソリューションに接続する

このセクションのチュートリアルでは、Raspberry Pi 3 デバイスをリモート監視ソリューションに接続する方法について説明します。 使用するプログラミング言語に応じたチュートリアルを選択し、お使いの Raspberry Pi で使用できるセンサー ハードウェアの有無を確認します。

## <a name="the-tutorials"></a>チュートリアル

| チュートリアル | メモ | 言語 |
| -------- | ----- | --------- |
| シミュレートされたテレメトリ (初級)| センサー データをシミュレートします。 スタンドアロンの Raspberry Pi を使用します。 | [C][lnk-c-simulator]、[Node.js][lnk-node-simulator] |
| 実際のセンサー (中級) | Raspberry Pi に接続している BME280 センサーからのデータを使用します。 | [C][lnk-c-basic]、[Node.js][lnk-node-basic] |
| ファームウェアの更新の実装 (上級)| Raspberry Pi に接続している BME280 センサーからのデータを使用します。 Raspberry Pi のリモート ファームウェア更新を有効にします。 | [C][lnk-c-advanced]、[Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>次のステップ

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。

[lnk-node-simulator]: iot-suite-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-raspberry-pi-kit-c-get-started-advanced.md
