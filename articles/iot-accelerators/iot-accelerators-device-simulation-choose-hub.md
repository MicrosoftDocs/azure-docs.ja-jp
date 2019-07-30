---
title: デバイス シミュレーション ソリューションで既存の IoT Hub を使用する - Azure | Microsoft Docs
description: この記事では、デバイス シミュレーション ソリューション アクセラレータを構成して既存の IoT Hub を使用する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203951"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>デバイス シミュレーション ソリューション アクセラレータで既存の IoT Hub を使用する

デバイス シミュレーションのデプロイ時に、シミュレーションで使用する IoT ハブをデプロイするように選択することもできます。 このオプションでは、[単一スケール ユニットの S2 レベルの IoT ハブ](../iot-hub/iot-hub-scaling.md)がデプロイされます。 この省略可能な IoT ハブをデプロイする場合、シミュレーションの実行のために別の IoT ハブをターゲットにするように選択することもできます。

省略可能な IoT ハブをデプロイしないことにした場合、実行するすべてのシミュレーションのために独自のハブを使用する必要があります。

IoT Hub がない場合は、いつでも [Azure Portal](https://portal.azure.com) で新しく作成できます。

既存の IoT ハブを使用するには、**iothubowner** 共有アクセス ポリシーに対する接続文字列が必要です。 この接続文字列は、[Azure Portal](https://portal.azure.com) から取得できます。

1. ポータルのハブの構成ページで、 **[共有アクセス ポリシー]** をクリックします。

1. **iothubowner** をクリックします。

1. プライマリ キーまたはセカンダリの接続文字列をコピーします。

[![接続文字列を取得する](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

シミュレーションを構成するときは、コピーした接続文字列を使用します。

![シミュレーションを構成する](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>次の手順

このハウツー ガイドでは、シミュレーションで既存の IoT Hub を使用する方法について学習しました。 次は、シミュレーション用の[高度なデバイス モデルを作成する](iot-accelerators-device-simulation-advanced-device.md)方法について学習することができます。
