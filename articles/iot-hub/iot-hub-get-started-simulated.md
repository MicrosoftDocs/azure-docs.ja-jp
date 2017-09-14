---
title: "Azure IoT Hub へのシミュレートされたデバイス接続入門 |Microsoft Docs"
description: "シミュレートされた IoT デバイスを作成し、それらを Azure IoT Hub に接続する方法を説明します。 デバイスは IoT Hub にテレメトリを送信でき、IoT Hub はデバイスを監視および管理することができます。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT Hub チュートリアル"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 436b3057509a831837159e814490959a2d7455a4
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---
# <a name="azure-iot-hub-get-started-with-simulated-devices-tutorials"></a>Azure IoT Hub とシミュレートされたデバイス入門チュートリアル

以下のチュートリアルでは、Azure IoT Hub と Device SDK について紹介します。 このチュートリアルでは、一般的な IoT シナリオを使用して、IoT Hub の機能を説明します。 また、チュートリアルでは、IoT Hub を他の Azure サービスやツールと組み合わせて、より強力な IoT ソリューションを構築する方法も提示します。 次の表に示すチュートリアルでは、シミュレートされた IoT デバイスを作成する方法を説明します。

| プログラミング言語 |
|----------------------|
| [.NET][Sim_NET]      |
| [Java][Sim_Jav]      |
| [Node.js][Sim_Nd]    |
| [Python][Sim_Pyth]   |

また、IoT Edge ゲートウェイを使用すると、IoT hub にシミュレートされたデバイスを接続できます。

| プログラミング言語 | プラットフォーム           |
|----------------------|------------------- |
| C                    | [Linux][Sim_Lnx]   |
| C                    | [Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md

