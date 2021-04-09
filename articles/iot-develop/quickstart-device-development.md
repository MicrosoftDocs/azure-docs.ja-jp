---
title: Azure IoT 組み込みデバイス開発のクイックスタート
description: Azure RTOS と Azure IoT を使用して、組み込みデバイス開発を行う方法を示すクイックスタート ガイドです。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: bc8317e3402fa0185186c06ed1f89e062e99fd5e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654747"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Azure IoT 組み込みデバイス開発の概要

**適用対象**: [組み込みデバイスの開発](about-iot-develop.md#embedded-device-development)

このファースト ステップ ガイドには、組み込みデバイスと Azure IoT の使用を開始する方法を示す一連のクイックスタートが含まれています。 

各クイックスタートでは、次の基本的なタスクを実行します。
* C で特定のデバイスをプログラミングするための、一連の組み込み開発ツールをインストールする
* Azure RTOS コンポーネントとサンプルを含むイメージを作成し、デバイスをフラッシュする
* デバイスを Azure IoT に安全に接続する
* デバイスのテレメトリを表示し、プロパティを表示し、cloud-to-device メソッドを呼び出す

## <a name="quickstarts"></a>クイックスタート
このファースト ステップ ガイドには、次のチュートリアルが含まれています。

|クイック スタート|Device|
|---------------|-----|
|[ST Microelectronics B-L475E-IOT01 Discovery kit の概要](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST Microelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[ST Microelectronics B-L4S5I-IOT01 Discovery kit の概要](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST Microelectronics B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[NXP MIMXRT1060-EVK Evaluation kit の概要](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[NXP MIMXRT1050-EVKB Evaluation kit の概要](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[Microchip ATSAME54-XPRO Evaluation kit の概要](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[Microchip ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[MXChip AZ3166 IoT DevKit の概要](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) |[MXChip AZ3166 IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)|
|[Renesas Starter Kit+ for RX65N-2MB の概要](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[Renesas Starter Kit+ for RX65N-2MB](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>次のステップ
このガイドのデバイス固有のクイックスタートを完了したら、Azure RTOS の概要リポジトリにあるその他のデバイス固有の記事とサンプルを確認してください。
* [Azure RTOS と Azure IoT の概要](https://github.com/azure-rtos/getting-started)