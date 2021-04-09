---
title: Azure IoT Hub のエラー 404001 DeviceNotFound のトラブルシューティング
description: エラー 404001 DeviceNotFound を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960304"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

この記事では、**404001 DeviceNotFound** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

C2D メッセージ、ツイン更新、ダイレクト メソッドなどの cloud-to-device (C2D) 通信中に、操作がエラー **404001 DeviceNotFound** で失敗します。

## <a name="cause"></a>原因

IoT Hub によってデバイスが検出されなかったため、操作に失敗しました。 デバイスが登録されていないか、無効になっています。

## <a name="solution"></a>解決策

使用したデバイス ID を登録してから、再試行してください。