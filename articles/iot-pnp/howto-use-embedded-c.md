---
title: 制約のあるデバイスで IoT プラグ アンド プレイを使用する | Microsoft Docs
description: 埋め込み C または Azure RTOS 用のいずれかの SDK を使用して、制約のあるデバイスに IoT プラグ アンド プレイを実装する方法について説明します。
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d61ca10612a0935f8483745d164835d7498280c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92042815"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>制約のあるデバイスに IoT プラグ アンド プレイを実装する

*制約のあるデバイス* 向けの開発を行っている場合、[埋め込み C IoT 用の Azure SDK クライアント ライブラリ](https://aka.ms/embeddedcsdk)または [Azure RTOS](/azure/rtos/overview-rtos) を使用して、IoT プラグ アンド プレイを使用できます。 この記事には、これらの制約のあるシナリオに向けたリンクとリソースが含まれています。

## <a name="use-the-sdk-for-embedded-c"></a>埋め込み C 用の Azure SDK を使用する

埋め込み C 用の SDK には、[IoT プラグ アンド プレイ規則](concepts-convention.md)の使用を含め、制約のあるデバイスを Azure IoT サービスに接続するための軽量ソリューションが用意されています。 下記のリンク先には、実際のデバイスのサンプルと、教育およびデバッグ目的のサンプルが含まれます。

### <a name="use-a-real-device"></a>実在のデバイスの使用

埋め込み C 用の SDK、Device Provisioning Service、IoT プラグ アンド プレイを実際のデバイスで使用する完全なエンド ツー エンドのチュートリアルについては、[PIC-IoT Wx 開発ボードを用途変更して、IoT Hub Device Provisioning Service を介して Azure に接続する](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)ことに関するページを参照してください。

### <a name="introductory-samples"></a>導入用サンプル

埋め込み C 用の SDK のリポジトリには、IoT プラグ アンド プレイの使用方法を示す[いくつかのサンプル](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample)が含まれています。

> [!NOTE]
> これらのサンプルでは、教育およびデバッグの目的で、Windows および Linux での実行が示されます。 運用環境のシナリオでは、サンプルは制約のあるデバイスのみを対象としています。

- [埋め込み C 用の SDK を使用するサーモスタットの例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [埋め込み C 用の SDK を使用する温度コントローラーの例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTOS の使用

Azure RTOS には、Azure IoT クラウド サービスにネイティブ接続を追加する軽量なレイヤーが含まれています。 このレイヤーでは、Azure RTOS の高度な機能を使用しながら、制約のあるデバイスを Azure IoT に接続するための簡単なメカニズムが提供されます。 詳細については、「[Microsoft Azure RTOS とは](/azure/rtos/overview-rtos)」を参照してください。

### <a name="toolchains"></a>ツールチェーン

Azure RTOS のサンプルは、さまざまな種類の IDE とツールチェーンの組み合わせで提供されています。以下に例を示します。

- IAR:IAR の [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake:オープン ソース [CMake](https://cmake.org/) ビルド システムと [GNU Arm Embedded toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) の上に構築されています。
- MCUExpresso:NXP の [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube:STMicroelectronic の [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB:Microchip の [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>サンプル

Azure RTOS と IoT プラグ アンド プレイによってさまざまなデバイスの使用を開始する方法を示すサンプルについては、次の表を参照してください。

Manufacturer | Device | サンプル |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>次の手順

ここまでに、制約のあるデバイスに IoT プラグ アンド プレイを実装する場合のオプションについて学習しました。推奨される次の手順は、[IoT プラグ アンド プレイ規則](concepts-convention.md)について学習することです。