---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このチュートリアルでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878833"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>チュートリアル:Azure IoT Central でデバイスのルールとアクションを構成する (プレビュー機能)

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、環境センサー デバイスの温度が華氏 90&deg; を超えた場合にメールを送信するルールを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

## <a name="prerequisites"></a>前提条件

開始する前に、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関するチュートリアルを完了し、操作する **Environment Sensor** デバイス テンプレートを作成しておく必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールを対象のアプリケーションに追加するには、左側のナビゲーション メニューで **[規則]** を選択します。

1. 新しいルールを作成するには、 **[+ 新規]** を選択します。 次に、 **[テレメトリ]** を選択します。

1. ルール名として「**Environmental temperature**」と入力します。

1. **[スコープ]** セクションで、デバイス テンプレートとして **[Environment Sensor]** を選択します。 このルールが適用されるデバイスのスコープ。 **[+ フィルター]** を使用して、さらにフィルター条件を追加できます。

1. **[条件]** セクションで、ルールをトリガーする条件を定義します。 次の情報を使用して、温度テレメトリに基づいて条件を定義します。

    | フィールド                                        | 値                             |
    | -------------------------------------------- | ------------------------------    |
    | Measurement                                  | 気温                       |
    | Operator                                     | が次の値より大きい                   |
    | 値                                        | 90                                |

    さらに条件を追加するには、 **[+ 条件]** を選択します。

    ![ルール条件を作成する](./media/tutorial-configure-rules-pnp/condition.png)

1. ルールがトリガーされたときに実行するアクションを追加するには、 **[+ Action]\(+ アクション\)** を選択し、 **[電子メール]** を選択します。

1. 次の表の情報を使用して、アクションを定義します。

    | Setting   | 値                                             |
    | --------- | ------------------------------------------------- |
    | ターゲット        | メール アドレス                                |
    | メモ     | 環境温度がしきい値を超えました。 |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    ![ルール アクションを作成する](./media/tutorial-configure-rules-pnp/action.png)

1. **[保存]** を選択します。 ルールが **[ルール]** ページに表示されます。

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義された条件が満たされると、アプリケーションによって、アクションで指定したメール アドレスにメッセージが送信されます。

> [!NOTE]
> テストの完了後は、ルールを無効にして、受信トレイに届くアラートを停止してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* テレメトリベースのルールを作成する
* アクションを追加する

これでしきい値に基づいたルールを定義できました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [イベント ルールを作成して通知を設定する](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
