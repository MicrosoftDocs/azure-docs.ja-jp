---
title: チュートリアル - Azure IoT Central アプリケーションでデバイス グループを使用する | Microsoft Docs
description: チュートリアル - オペレーターとして、デバイス グループを使用して Azure IoT Central アプリケーションでデバイスからのテレメトリを分析する方法について学習します。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a7d26eebb24662a448d8ccb44d037e7706fe776b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832846"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>チュートリアル:デバイス グループを使用してデバイス テレメトリを分析する

この記事では、オペレーターとしてデバイス グループを使用して Azure IoT Central アプリケーションでデバイス テレメトリを分析する方法について説明します。

デバイス グループは、指定されたいくつかの条件に一致しているためにグループ化されたデバイスの一覧です。 デバイス グループは、デバイスをより小さい論理グループにグループ化することで、デバイスを大規模に管理、視覚化、および分析するのに役立ちます。 たとえば、シアトルのすべてのエアコン デバイス グループの一覧を作成して、技術者が自分の担当するデバイスを検索できるようにできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス グループを作成する
> * デバイス グループを使用してデバイス テレメトリを分析する

## <a name="prerequisites"></a>前提条件

作業を開始する前に、「[Azure IoT Central アプリケーションを作成する](./quick-deploy-iot-central.md)」と「[IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-simulated-device.md)」の各クイックスタートを完了して、作業に使用する **Sensor Controller** デバイス テンプレートを作成しておく必要があります。

## <a name="create-simulated-devices"></a>シミュレートされたデバイスを作成する

デバイス グループを作成する前に、このチュートリアルで使用する **Sensor Controller** デバイス テンプレートに基づいてシミュレートされたデバイスを少なくとも 5 つ追加します。


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="5 つのシミュレートされたセンサー コントローラー デバイスを示すスクリーンショット":::

シミュレートされたセンサー デバイスの 4 つには、 **[デバイスの管理]** ビューを使用して、顧客名を「*Contoso*」に設定します。

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="[顧客名] クラウド プロパティの設定方法を示すスクリーンショット":::

## <a name="create-a-device-group"></a>デバイス グループを作成する

デバイス グループを作成するには、次の手順を実行します。

1. 左側のペインで **[デバイス グループ]** を選択します。

1. **[+新規]** を選択します。

1. デバイス グループに *Contoso devices* という名前を付けます。 説明を追加することもできます。 デバイス グループに含めることができるのは、1 つのデバイス テンプレートのデバイスだけです。 このグループに使用する **Sensor Controller** デバイス テンプレートを選択します。

1. **Contoso** に属するデバイスのみを含むようにデバイス グループをカスタマイズするには、 **[+ フィルター]** を選択します。 **[顧客名]** プロパティ、 **[等しい]** 比較演算子、値として **[Contoso]** を選択します。 デバイス グループに設定されている **すべて** のフィルター条件を満たす複数のフィルターとデバイスを追加できます。 作成したデバイス グループには、アプリケーションにアクセスできるすべてのユーザーがアクセスできるため、だれでもデバイス グループを表示、変更、または削除できます。

    > [!TIP]
    > デバイス グループは動的なクエリです。 デバイスの一覧を表示するたびに、一覧内のデバイスが異なることがあります。 一覧は、現在クエリの条件を満たしているデバイスによって異なります。

1. **[保存]** を選択します。

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="デバイス グループのクエリ構成を示すスクリーンショット":::

> [!NOTE]
> Azure IoT Edge デバイスの場合、Azure IoT Edge テンプレートを選択してデバイス グループを作成します。

## <a name="analytics"></a>Analytics

**Analytics** をデバイス グループと共に使用して、グループ内のデバイスからのテレメトリを分析することができます。 たとえば、すべての Contoso 環境センサーによってレポートされた平均温度をプロットできます。

デバイス グループのテレメトリを分析するには、次のようにします。

1. 左側のウィンドウで **[Analytics]** を選択します。

1. 作成した **[Contoso デバイス]** デバイス グループを選択します。 次に、 **[温度]** と **[湿度]** 両方のテレメトリの種類を追加します。

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="分析用に選択されたテレメトリの種類を示すスクリーンショット":::

    テレメトリの種類の横にある歯車アイコンを使用して、集計の種類を選択します。 既定値は、 **[平均]** です。 **[グループ化]** を使用して、集計データの表示方法を変更します。 たとえば、デバイス ID で分割する場合、 **[分析]** を選択するとデバイスごとにプロットが表示されます。

1. 平均テレメトリ値を表示するには、 **[分析]** を選択します。

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="すべての Contoso デバイスの平均値を示すスクリーンショット":::

    ビューをカスタマイズしたり、表示される期間を変更したり、データをエクスポートしたりすることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central アプリケーションでデバイス グループを使用する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [テレメトリのルールを作成する方法](tutorial-create-telemetry-rules.md)
