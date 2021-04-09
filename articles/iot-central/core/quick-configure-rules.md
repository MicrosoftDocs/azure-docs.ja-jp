---
title: クイックスタート - Azure IoT Central でルールとアクションを構成する
description: このクイックスタートでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833849"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>クイック スタート:Azure IoT Central でデバイスのルールとアクションを構成する

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

このクイックスタートでは、デバイスのセンサーによってレポートされる湿度が 55% を超えたときにメールを送信するルールを作成します。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」と「[IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-simulated-device.md)」の 2 つのクイックスタートを完了して、作業に使用する **Sensor Controller** デバイス テンプレートを作成しておく必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールを対象のアプリケーションに追加するには、左側のペインで **[規則]** を選択します。

1. 新しいルールを作成するには、**[+ 新規]** を選択します。

1. ルール名として「**Environmental humidity**」と入力します。

1. **[ターゲット デバイス]** セクションで、デバイス テンプレートとして **[Sensor Controller]** を選択します。 このオプションは、デバイス テンプレートの種類によってルールが適用されるデバイスをフィルター処理します。 **[+ フィルター]** を選択することで、さらにフィルター条件を追加できます。

1. **[条件]** セクションで、ルールをトリガーする条件を定義します。 次の情報を使用して、温度テレメトリに基づいて条件を定義します。

    | フィールド        | Value            |
    | ------------ | ---------------- |
    | Measurement  | SensorHumid      |
    | 演算子     | が次の値より大きい  |
    | Value        | 55               |

    さらに条件を追加するには、 **[+ 条件]** を選択します。

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="ルールの条件を示すスクリーンショット":::

1. ルールがトリガーされたときに実行するメール アクションを追加するには、 **[+ 電子メール]** を選択します。

1. 次の表の情報を使用してアクションを定義してから、 **[完了]** を選択します。

    | 設定   | 値                                             |
    | --------- | ------------------------------------------------- |
    | Display name | オペレーターの電子メール アクション                          |
    | ターゲット        | メール アドレス                                |
    | Notes     | 環境湿度がしきい値を超えました。 |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer.md) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="ルールに追加された電子メール アクションを示すスクリーンショット":::

1. **[保存]** を選択します。 ルールが **[ルール]** ページに表示されます。

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義されている条件が満たされると、アプリケーションによって、アクションで指定したアドレスにメールが送信されます。

> [!NOTE]
> テストの完了後は、ルールを無効にして、受信トレイに届くアラートを停止してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、次の方法について説明しました。

* テレメトリベースのルールを作成する
* アクションを追加する

アプリケーションに接続されているデバイスの監視の詳細については、以下のクイックスタートをご覧ください。

> [!div class="nextstepaction"]
> [Azure IoT Central を使用してデバイスを監視する](quick-monitor-devices.md)。
