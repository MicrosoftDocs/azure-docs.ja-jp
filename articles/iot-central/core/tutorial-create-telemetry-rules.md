---
title: チュートリアル - Azure IoT Central アプリケーションでルールを作成して管理する
description: このチュートリアルでは、Azure IoT Central のルールを使用して、ほぼリアルタイムでデバイスを監視し、ルールがトリガーされたときに、電子メールの送信などのアクションを自動的に呼び出す方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b0b5aafd85fe6d992afa9d879f73ef0ec43e00d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834376"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Central アプリケーションで規則を作成して通知を設定する

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

Azure IoT Central を使用して、接続されたデバイスをリモートで監視できます。 Azure IoT Central のルールを使用すると、ほぼリアルタイムでデバイスを監視し、電子メールの送信などのアクションを自動的に呼び出すことができます。 この記事では、自分のデバイスから送信されるテレメトリを監視するルールを作成する方法について説明します。

デバイスは、テレメトリを使用してデバイスから数値データを送信します。 選択したテレメトリが指定されたしきい値を超えると、ルールがトリガーされます。

このチュートリアルでは、シミュレートされたセンサー デバイスの温度が華氏 70&deg; を超えたときにメールを送信するルールを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 規則を作成する
> * 電子メール アクションを作成する

## <a name="prerequisites"></a>前提条件

作業を開始する前に、「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」と「[IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-simulated-device.md)」の各クイックスタートを完了して、作業に使用する **Sensor Controller** デバイス テンプレートを作成しておきます。

## <a name="create-a-rule"></a>規則を作成する

テレメトリ ルールを作成するには、デバイス テンプレートに少なくとも 1 つのテレメトリ値が含まれている必要があります。 このチュートリアルでは、温度と湿度のテレメトリを送信する、シミュレートされた **Sensor Controller** デバイスを使用します。 「[シミュレートされたデバイスを IoT Central アプリケーションに追加する](./quick-create-simulated-device.md)」クイックスタートで、このデバイス テンプレートを追加し、シミュレートされたデバイスを作成しました。 ルールは、デバイスによってレポートされる温度を監視し、70 度を超えたときに電子メールを送信します。

> [!NOTE]
> 1 アプリケーションあたり 50 個がルール数の上限となります。

1. 左側のペインで、 **[ルール]** を選択します。

1. まだルールを作成していない場合は、次の画面が表示されます。

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="ルールの空の一覧を示すスクリーンショット":::

1. **[+ 新規]** を選択して新しいルールを追加します。

1. ルールを識別するために「_Temperature monitor_」という名前を入力して、Enter キーを押します。

1. **[Sensor Controller]** デバイス テンプレートを選択します。 既定では、このルールはデバイス テンプレートに関連付けられたすべてのデバイスに自動的に適用されます。 デバイスのサブセットのためのフィルター処理を行うには、 **[+ フィルター]** を選択し、デバイス プロパティを使用してデバイスを識別します。 ルールを無効にするには、 **[有効/無効]** ボタンを切り替えます。

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="ルール定義でのデバイス テンプレートの選択を示すスクリーンショット":::

### <a name="configure-the-rule-conditions"></a>ルールの条件を構成する

条件には、ルールが監視する基準を定義します。 このチュートリアルでは、温度が華氏 70&deg; を超えたときにルールがトリガーされるように構成します。

1. **[テレメトリ]** ドロップダウンから **[温度]** を選択します。

1. 次に、 **[演算子]** として **[次の値より大きい]** を選択し、 **[値]** に「_70_」を入力します。

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="ルールの温度条件を示すスクリーンショット":::

1. 必要に応じて、 **[時間の集計]** を設定できます。 時間の集計を選択する場合は、集計ドロップダウンから [平均] や [合計] などの集計の種類も選択する必要があります。

    * 集計を選択しない場合、条件を満たすテレメトリ データ ポイントごとにルールがトリガーします。 たとえば、温度が 70 度を超えるとトリガーされるようにルールを構成している場合、その値をデバイスの温度が超えると、ほぼ即座にルールがトリガーされます。
    * 集計を使用している場合は、時間枠のテレメトリ データ ポイントの集計値が条件を満たすとルールがトリガーされます。 たとえば、平均集計時間を 10 分として、温度が 70 度を超えたらトリガーされるように構成している場合、10 分の間隔で計算が行われ、70 度を越える平均温度がデバイスによってレポートされると、ルールがトリガーされます。

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="入力された集計条件を示すスクリーンショット":::

複数の条件をルールに追加するには、 **[+ 条件]** を選択します。 複数の条件を指定する場合、ルールをトリガーするためにはすべての条件が満たされる必要があります。 各条件は、暗黙的な `AND` 句によって結合されます。 複数の条件で時間の集計を使用している場合は、すべてのテレメトリ値を集計する必要があります。

### <a name="configure-actions"></a>アクションを構成する

条件を定義したら、ルールが適用されたときに実行するアクションを設定します。 アクションは、ルールに指定されたすべての条件が true と評価されたときに呼び出されます。

1. **[アクション]** セクションで **[+ 電子メール]** を選択します。

1. アクションの表示名として「_温度の警告_」と入力し、 **[宛先]** フィールドにメール アドレスを入力し、「_デバイスを確認する必要があります_」 と入力します (これは、メールの本文に注記として表示されます)。

    > [!NOTE]
    > 電子メールは、アプリケーションに追加されており、少なくとも 1 回はログインしているユーザーにのみ送信されます。 Azure IoT Central での[ユーザー管理](howto-administer.md)について詳しくは、こちらをご覧ください。

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="ルールの電子メール アクションを示すスクリーンショット":::

1. アクションを保存するには、 **[Done]\(完了\)** を選択します。 ルールには複数のアクションを追加できます。

1. ルールを保存するには、 **[保存]** を選択します。 ルールは数分以内に有効になり、アプリケーションに送信されるテレメトリの監視が開始されます。 ルールで指定した条件を満たすと、構成した電子メールのアクションがトリガーされます。

しばらくしてから、ルールがトリガーされると電子メール メッセージを受け取ります。

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="通知メールを示すスクリーンショット":::

## <a name="delete-a-rule"></a>規則を削除する

ルールが不要になった場合は、ルールを開き、 **[削除]** を選択して削除します。

## <a name="enable-or-disable-a-rule"></a>ルールを有効または無効にする

有効または無効にするルールを選択します。 ルールの **[Enabled/Disabled]\(有効/無効\)** ボタンを切り替えて、ルールのスコープ内にあるすべてのデバイスのルールを有効または無効にします。

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>特定のデバイスのルールを有効または無効にする

カスタマイズしたいルールを選択します。 **[ターゲット デバイス]** セクションで 1 つ以上のフィルターを使用して、監視したいデバイスに対してルールのスコープを絞り込むことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* テレメトリベースのルールを作成する
* アクションを追加する

しきい値に基づくルールを定義したので、次の手順では次の方法を学習することをお勧めします。

> [!div class="nextstepaction"]
> [規則に基づいて Webhook を作成する](./howto-create-webhooks.md)
