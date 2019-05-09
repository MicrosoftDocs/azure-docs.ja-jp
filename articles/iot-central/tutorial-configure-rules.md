---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このチュートリアルでは、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2bd22dec13e2dba63c512276a0b36d4fc50e05f8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681567"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>チュートリアル:Azure IoT Central でデバイスのルールとアクションを構成する

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

このチュートリアルでは、コネクテッド空調デバイスの温度が華氏 90&deg; 度を超えた場合にメールが送信されるルールを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

## <a name="prerequisites"></a>前提条件

開始する前に、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルを完了し、操作する **Connected Air Conditioner** デバイス テンプレートを作成しておく必要があります。

## <a name="create-a-telemetry-based-rule"></a>テレメトリベースのルールを作成する

1. 新しいテレメトリベースのルールをアプリケーションに追加するには、左側のナビゲーション メニューで **[デバイス テンプレート]** を選択します。

    ![[デバイス テンプレート] ページ](media/tutorial-configure-rules/templatespage1.png)

    前のチュートリアルで作成した **Connected Air Conditioner (1.0.0)** デバイス テンプレートが表示されます。

2. デバイス テンプレートをカスタマイズするには、前のチュートリアルで作成した **Connected Air Conditioner** テンプレートを選択します。

3. **[ルール]** ビューでテレメトリ ベースのルールを追加するには、**[ルール]** を選択し、**[+ 新しいルール]** を選択して、**[テレメトリ]** を選択します。

    ![[ルール] ビュー](media/tutorial-configure-rules/newrule.png)

5. ルールを定義するには、次の表の情報を使用します。

    | Setting                                      | 値                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | 空調機の温度アラート |
    | [Enable rule for all devices of this template]\(このテンプレートのすべてのデバイスに対してルールを有効にする\) | On                                |
    | 条件                                    | Temperature is greater than 90    |
    | 集計                                  | なし                              |

    ![温度ルールの条件](media/tutorial-configure-rules/temperaturerule.png)

    次に、**[保存]** を選択します。

## <a name="add-an-action"></a>アクションを追加する

ルールを定義する場合、ルールの条件が満たされたときに実行されるアクションも定義します。 このチュートリアルでは、メール通知を送信するアクションに関するルールを作成します。

1. **アクション**を追加するには、最初にルールを**保存**してから、**[Configure Telemetry Rule]\(テレメトリ ルールの構成\)** パネルを下にスクロールします。 **[アクション]** の隣にある **[+]** を選択して、**[電子メール]** を選択します。

    ![温度ルールのアクション](media/tutorial-configure-rules/addaction.png)

2. アクションを定義するには、次の表の情報を使用します。

    | Setting   | 値                          |
    | --------- | ------------------------------ |
    | ターゲット        | メール アドレス             |
    | メモ     | 空調機の温度がしきい値を超えました。 |

    > [!NOTE]
    > メール通知を受け取るには、メール アドレスが[アプリケーションのユーザー ID](howto-administer.md) である必要があります。また、そのユーザーが少なくとも一度アプリケーションにサインインしている必要があります。

    ![温度のアクション](media/tutorial-configure-rules/temperatureaction.png)

3. **[保存]** を選択します。 ルールが **[ルール]** ページに表示されます。

## <a name="test-the-rule"></a>ルールをテストする

ルールは保存した後すぐに有効になります。 ルールで定義された条件が満たされると、アプリケーションによって、アクションで指定したメール アドレスにメッセージが送信されます。

> [!NOTE]
> テストの完了後は、ルールを無効にして、受信トレイに届くアラートを停止してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * テレメトリベースのルールを作成する
> * アクションを追加する

これでしきい値に基づいたルールを定義できました。推奨される次の手順は、[オペレーターのビューのカスタマイズ](tutorial-customize-operator.md)です。

Azure IoT Central のさまざまな種類のルールとルール定義をパラメーター化する方法の詳細については、以下を参照してください。
* [テレメトリ ルールを作成して通知を設定する](howto-create-telemetry-rules.md)。
* [イベント ルールを作成して通知を設定する](howto-create-event-rules.md)。

<!-- Next tutorials in the sequence -->